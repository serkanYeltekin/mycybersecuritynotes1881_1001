Bu zafiyeti ileride tekrar ettiğinde hızlıca hatırlayabilmen için referans alabileceğin detaylı write-up:

# Write-up: Method-Based Access Control Bypass

## 🎯 Zafiyetin Özeti

Hedef sistemde yöneticilere özel olan `/admin-roles` (veya benzeri) yetki yükseltme uç noktası (endpoint), erişim kontrolünü yalnızca `POST` istekleri üzerinden yapacak şekilde hatalı yapılandırılmıştır. HTTP metodu değiştirilerek (`GET` veya geçersiz bir metod gibi) güvenlik filtresi atlatılmış ve düşük yetkili bir kullanıcı üzerinden dikey yetki yükseltme (Vertical Privilege Escalation) gerçekleştirilmiştir.

## 🛠️ Adım Adım Sömürü (Exploitation) Süreci

1. **Geçerli Admin İsteğini Yakalama:** Sistemin normal işleyişini anlama.
Yönetici (admin) hesabıyla sisteme giriş yapıldı ve admin paneli üzerinden "Carlos" kullanıcısı terfi ettirildi. Bu işlem sırasında arka planda giden istek Burp Suite ile yakalanıp Repeater'a gönderildi.

*Örnek Orijinal İstek:*

```http
POST /admin-roles HTTP/1.1
Host: target-lab.com
Cookie: session=ADMIN_SESSION_COOKIE

username=carlos&action=promote

```


2. **Yatay/Dikey Erişim Kontrolü Testi:** 401 Unauthorized Teyidi.
Gizli sekmede standart (admin olmayan) bir kullanıcı ile oturum açıldı ve o kullanıcının `session` çerezi alındı. Repeater'daki admin isteğindeki çerez, standart kullanıcının çerezi ile değiştirildi ve istek gönderildi.

**Sonuç:** Sunucu `401 Unauthorized` yanıtı döndü. Bu, sistemin `POST` istekleri için doğru bir yetki kontrolü yaptığını kanıtladı.


3. **Filtre Davranışını Fuzzlama:** POSTX Metodu ile Zayıflık Tespiti.
Erişim kontrolünün sadece metoda bağlı olup olmadığını test etmek için `POST` metodu, geçersiz bir metod olan `POSTX` ile değiştirildi.

**Sonuç:** Yanıt `401 Unauthorized` yerine `Missing parameter` olarak değişti.

> **Kritik Çıkarım:** Güvenlik filtresi (Interceptor/Filter) sadece `POST` metodunu kontrol ediyor. `POSTX` görünce "Bu benim umrumda değil" deyip isteği doğrudan Backend Controller'ına iletti. Controller çalıştı ancak `POSTX` gövdesini okuyamadığı için parametreleri bulamadı.


4. **GET Metodu ile Zafiyeti Sömürme:** Parametreleri URL'e taşıma.
Burp Suite'te isteğe sağ tıklanarak "Change request method" seçildi. İstek otomatik olarak `GET` metoduna dönüştü ve gövdedeki (body) parametreler URL'e taşındı. URL'deki `username` parametresi hedef (kendi kullanıcımız) olarak değiştirilip istek gönderildi.

*Örnek Zararlı İstek:*

```http
GET /admin-roles?username=kendi_kullanicim&action=promote HTTP/1.1
Host: target-lab.com
Cookie: session=STANDART_USER_SESSION_COOKIE

```

**Sonuç:** `302 Found` veya `200 OK` yanıtı alındı. Güvenlik filtresi atlatıldı ve Controller, URL'den aldığı parametrelerle yetki yükseltme işlemini başarıyla veritabanına işledi.


---

## 💻 Arka Plan (Root Cause Analysis)

Bu durum genellikle backend mimarilerinde framework'ün (örneğin Java Spring Boot) esnek yapısının, zayıf yazılmış özel güvenlik filtreleriyle çakışmasından kaynaklanır.

Olayın kod seviyesindeki anatomisi tam olarak şöyledir:

**1. Hatalı Güvenlik Filtresi (Authorization Filter):**
Geliştirici, filtreyi yazarken metod kontrolünü çok dar tutmuştur.

```java
// Kötü pratik: Sadece POST metodunu kontrol eden bir filtre
public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) {
    HttpServletRequest req = (HttpServletRequest) request;
    
    if (req.getMethod().equals("POST") && req.getRequestURI().equals("/admin-roles")) {
        if (!checkIfUserIsAdmin(req.getCookies())) {
            throw new UnauthorizedException(); // 401 Döner
        }
    }
    chain.doFilter(request, response); // GET veya POSTX gelirse filtreyi atlar!
}

```

**2. Esnek Backend Controller:**
Güvenlik filtresini es geçen istek, veriyi işleyen Controller sınıfına ulaşır. Controller'daki anotasyon, spesifik bir metod (sadece POST gibi) belirtmediği için her türlü HTTP metodunu kabul eder. `@RequestParam` ise Spring mimarisinde hem URL'den (GET) hem de Body'den (POST x-www-form-urlencoded) veri okuyabilir.

```java
// Kötü pratik: HTTP metodu kısıtlanmamış Controller
@RequestMapping(value = "/admin-roles") // Sadece POST denmemiş!
public ResponseEntity<String> changeRole(@RequestParam String username, @RequestParam String action) {
    userService.updateUserRole(username, action);
    return ResponseEntity.ok("Role updated");
}

```

## 🛡️ Çözüm Önerisi (Mitigation)

Bu zafiyeti kapatmak için backend tarafında iki temel prensip uygulanmalıdır:

1. **Filtreleri Metoda Değil, Uç Noktaya (Endpoint) Bağlamak:** Güvenlik katmanı, `/admin-roles` uç noktasına gelen *tüm* istekleri (GET, POST, PUT, DELETE vb.) yetki kontrolünden geçirmelidir.
2. **Controller Metodunu Sınırlandırmak:** İşlem yapan, veritabanını değiştiren (State-changing) işlemler kesinlikle `GET` kabul etmemelidir. Örnek: Spring Boot'ta `@RequestMapping` yerine doğrudan `@PostMapping("/admin-roles")` kullanılmalıdır.