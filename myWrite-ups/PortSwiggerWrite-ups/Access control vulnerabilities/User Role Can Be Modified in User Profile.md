Harika bir yakalama! Özellikle login gibi belirgin yerler yerine, profil güncelleme gibi işlemlerde dönen response'ları (yanıtları) detaylıca incelemen, API zafiyetlerini bulmak için sahip olman gereken en kritik reflekslerden biri.

Anlattığın çözüm adımlarına tamamen sadık kalarak, bu süreci profesyonel bir Bug Bounty/Pentest write-up formatına dönüştürdüm.

---

## Lab Write-Up: User Role Can Be Modified in User Profile

**Zafiyet Türü:** Mass Assignment / Broken Access Control
**Hedef:** Normal bir kullanıcı hesabından yetki yükseltmesi (Privilege Escalation) yaparak `/admin` paneline erişmek.

### 1. Keşif ve Bilgi Toplama (Reconnaissance)

Lab açıklamasına göre, sistemde bir `/admin` paneli bulunuyor ve bu panele erişim sağlayabilmek için kullanıcının ilgili rol ID'sine (bizim senaryomuzda yetkili role) sahip olması gerekiyor.

İlk adım olarak, sistemin doğrulama ve kullanıcı oturumu yönetimini anlamak için HTTP trafiği araya girilerek (intercept) incelendi.

* **Login İşlemi:** Giriş (login) sırasında gönderilen request (istek) ve sunucudan dönen response incelendiğinde, yetki yükseltmeye dair manipüle edilebilecek herhangi bir parametre veya dikkat çekici bir veri tespit edilmedi.

### 2. Zafiyetin Tespiti (Vulnerability Identification)

Login işleminden sonuç alınamayınca, oturum açmış kullanıcının profilindeki fonksiyonlar test edilmeye başlandı. Kullanıcı profilinde yer alan "E-posta Değiştirme" fonksiyonu kullanıldığında zafiyetin ipucu yakalandı.

E-posta değişikliği yapıldığında, sunucunun döndürdüğü JSON yanıtında yalnızca başarılı işlem mesajı değil, kullanıcının tüm profil objesi ifşa ediliyordu:

```json
{
  "username": "wiener",
  "email": "test@gmail.com",
  "apikey": "ogabt6Rpfjrujfr5tp8h1ez5tADe8aWe",
  "roleid": 1
}

```

Bu yanıttaki `"roleid": 1` parametresi oldukça dikkat çekiciydi ve sunucunun kullanıcı rollerini bu ID üzerinden yönettiğini, mevcut kullanıcının rolünün ise `1` (standart kullanıcı) olduğunu gösteriyordu.

### 3. Sömürü (Exploitation)

Sistemin, dışarıdan gelen verileri doğrudan içerideki kullanıcı modeliyle eşleştirip eşleştirmediğini (Mass Assignment zafiyeti) test etmek için e-posta değiştirme isteği manipüle edildi.

**Orijinal İstek (Request):**
Normal şartlarda e-posta değiştirme işlemi için sunucuya giden gövde (body) şu şekildeydi:

```json
{
  "email": "test@gmail.com"
}

```

**Manipüle Edilmiş İstek (Payload):**
Sunucunun `roleid` parametresini dışarıdan kabul edip etmeyeceğini görmek için request gövdesine bu parametre eklendi ve değer yetkili kullanıcıyı temsil eden `2` olarak değiştirildi:

```json
{
  "email": "test@gmail.com", 
  "roleid": 2
}

```

İstek gönderildikten sonra dönen yanıt incelendiğinde, işlemin başarılı olduğu ve kullanıcının `roleid` değerinin sunucu tarafında `2` olarak güncellendiği doğrulandı.

### 4. Sonuç ve Etki

Role ID'si başarıyla `2` yapılan kullanıcı hesabıyla `/admin` dizinine gidildiğinde, admin paneline tam erişim sağlandığı görüldü ve lab başarıyla tamamlandı.

---

**Geliştirici Notu (Nasıl Çözülür?):**
Bu zafiyetin (Mass Assignment / Auto-binding) temel nedeni, uygulamanın istemciden gelen ham JSON verisini doğrudan veritabanı modeline (Entity) eşlemesidir. Bu tür kritik mantık hatalarını önlemek için mimaride **DTO (Data Transfer Object)** katmanları kullanılmalıdır. Güncelleme işlemi için sadece izin verilen alanları (örneğin sadece `email`) içeren bir DTO oluşturulsaydı, saldırgan request'e `roleid` eklese bile bu veri DTO'da tanımlı olmadığı için sunucu tarafından güvenli bir şekilde tamamen görmezden gelinecekti.