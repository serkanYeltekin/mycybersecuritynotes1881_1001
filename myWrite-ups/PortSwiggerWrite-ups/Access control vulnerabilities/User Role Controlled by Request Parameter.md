## Lab Çözüm Raporu: User Role Controlled by Request Parameter

### Zafiyet Özeti

Bu laboratuvarda, kullanıcı yetkilendirme kontrollerinin güvenli olmayan bir şekilde (istemci tarafında manipüle edilebilir bir çerez üzerinden) yapılması kaynaklı bir **Broken Access Control (Bozuk Erişim Kontrolü)** zafiyeti bulunmuş ve **Privilege Escalation (Yetki Yükseltme)** yapılarak sistem admini olunmuştur.

### Hedef

`/admin` paneline erişim sağlamak ve `carlos` isimli kullanıcıyı silmek.

### Keşif ve Bilgi Toplama Adımları

1. **Yetkisiz Erişim Denemesi:** Lab başlar başlamaz, doğrudan uygulamanın `/admin` dizinine giderek yetkisiz erişim testi yapıldı.
* *Gözlem:* Sunucu, `Admin interface only available if logged in as an administrator` uyarısı döndürerek erişimi engelledi. Bu durum, arka planda bir yetki kontrol mekanizmasının devrede olduğunu doğruladı.


2. **Yetkili Oturum Açma ve Trafik Analizi:** Hedef sistem tarafından verilen normal kullanıcı (`wiener:peter`) kimlik bilgileriyle giriş yapıldı. Giriş sonrasında gerçekleşen `GET /my-account?id=wiener` isteği incelendiğinde, kritik bir parametre tespit edildi:

```http
GET /my-account?id=wiener HTTP/2
Host: 0aa2004a03e4b9b68177debe003200e0.web-security-academy.net
Cookie: session=2EdbvJiAqr3U8N57UZgj4ch4XsR3ipyG; Admin=false
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
...

```

Buradaki `Admin=false` çerez parametresi, uygulamanın yetki seviyesini belirlemek için dışarıdan gelen (ve kullanıcı tarafından kontrol edilebilen) bir veriye güvendiğini gösterdi.

### Sömürü (Exploitation) Adımları

1. **İlk Manipülasyon Denemesi (Kısmi Başarı):** İstek arasına girilerek `Admin=false` değeri `Admin=true` olarak değiştirildi.
* *Sonuç:* Ekranda "Admin panel" butonu görünür hale geldi. Ancak butona tıklandığında tekrar `Admin interface only available if logged in as an administrator` hatası alındı.
* *Nedeni:* Sadece o anki isteği manipüle etmek uygulamanın arayüzünde (UI) admin butonunu açtı, ancak butona tıklandığında tarayıcı yeni bir istek oluşturdu ve kendi belleğindeki orijinal çerezi (`Admin=false`) gönderdi.


2. **Kalıcı Çerez Manipülasyonu:** Zafiyetin tam olarak sömürülebilmesi için, sahte yetki çerezinin tarayıcı tarafından her istekte kalıcı olarak sunucuya gönderilmesi gerektiği anlaşıldı.
* Tarayıcının Geliştirici Araçları (Inspect) açıldı.
* **Storage -> Cookies** bölümüne gidildi.
* Orada bulunan `Admin` çerezinin değeri çift tıklanarak `false` yerine `true` olarak güncellendi.


3. **Hedefe Ulaşma:** Sayfa yenilendiğinde, tarayıcı artık sunucuya otomatik olarak `Admin=true` çerezini göndermeye başladı. `/admin` paneline hiçbir hata almadan, başarılı bir şekilde yetkili erişim sağlandı.
4. Panel üzerinden `carlos` kullanıcısı silinerek laboratuvar hedefine ulaşıldı.

---

Özellikle ilk denemende sadece butonu görüp, tıkladığında gidememen üzerine sorunun "anlık istek modifikasyonunda" değil, "tarayıcıdaki kalıcı çerezde" olduğunu fark edip **Storage** sekmesine geçmen çok yerinde bir analiz olmuş.