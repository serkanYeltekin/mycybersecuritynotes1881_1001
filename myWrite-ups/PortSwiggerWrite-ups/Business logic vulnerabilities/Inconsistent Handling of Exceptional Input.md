PortSwigger Web Security Academy'deki **"Inconsistent handling of exceptional input"** lab'i, sistemlerin beklenen girdi sınırlarını (örneğin uzunluk) aştığında farklı bileşenlerin nasıl tutarsız davrandığını gösteren harika bir Business Logic zafiyeti örneğidir.

Notların için hazırladığım özet write-up'ı aşağıda bulabilirsin.

---

## Lab: Inconsistent Handling of Exceptional Input

### 🎯 Amaç

Yönetici (Admin) paneline erişmek ve `carlos` adlı kullanıcıyı silmek. Admin paneline sadece `@dontwannacry.com` uzantılı bir e-posta adresiyle kayıt olan kullanıcılar erişebilir.

### 🧠 Zafiyetin Mantığı (Teori)

Bu zafiyet, uygulamanın e-posta adresini doğrulayan bileşeni ile veritabanına kaydeden bileşeni arasındaki **tutarsızlıktan** kaynaklanır:

1. **Mail Gönderim Sistemi:** E-posta adresinin tamamını işler ve doğrulama mailini oraya gönderir.
2. **Veritabanı:** E-posta sütunu için genelde standart bir sınır olan **255 karakter** limiti kullanır. Eğer 255 karakterden uzun bir e-posta girilirse, ilk 255 karakteri kaydeder, kalanını keser (truncate).

Biz de hedef e-posta adresini (`@dontwannacry.com`) 255. karaktere denk getirecek şekilde uzun bir girdi hazırlarız. Sonrasına ise kendi kontrolümüzde olan e-posta sunucusunun uzantısını ekleriz. Sistem doğrulama mailini bizim sunucumuza atar ancak veritabanına onaylı kullanıcıyı kaydederken son kısmı kestiği için sistemde admin yetkilerine sahip `@dontwannacry.com` kullanıcısı olarak görünürüz.

---

### 🛠️ Adım Adım Çözüm

**1. E-posta İstemcisine Eriş:**
Lab'in sağladığı "Email client" butonuna tıkla ve sana atanan e-posta adresini kopyala.
*(Örnek: `exploit-0a1b...web-security-academy.net`)*

**2. Payload Hesaplaması:**
Veritabanında 255 karakter sınırı var. Hedefimiz olan `@dontwannacry.com` metni **17 karakter** uzunluğunda.
Geriye kalan kısmı doldurmak için (255 - 17 = 238) **238 adet padding (dolgu)** karakterine ihtiyacımız var.

**3. Malicious (Zararlı) E-posta Adresini Oluştur:**
238 adet harf + `@dontwannacry.com` + `[Sana verilen email istemcisi uzantısı]`

Kullanacağın adres şu formata benzemeli:

```text
AAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA@dontwannacry.com.exploit-0a1b...web-security-academy.net

```

*(Bu metni oluşturmak için Python, Burp Suite Decoder veya bir kelime işlemci kullanabilirsin. Toplam uzunluğun `.com` bitiminde tam 255 olduğuna emin ol.)*

**4. Kayıt İşlemi:**

* **My Account -> Register** sayfasına git.
* Hesaplamak için oluşturduğun bu aşırı uzun e-posta adresini gir.
* Bir şifre belirle ve kayıt ol.

**5. Hesabı Doğrula:**

* Uygulama e-postayı onaylamak için "Email client"ına bir link gönderecek (Çünkü mail sistemi kesme yapmıyor ve maili doğrudan senin `.net` ile biten exploit sunucuna gönderiyor).
* Email client'a git ve gelen doğrulama (registration) linkine tıkla.

**6. Yetki Yükseltme ve Hedefi Tamamlama:**

* Sistem, doğrulama linkine tıkladığında veritabanındaki kaydı günceller. Ancak veritabanı 255 karakter sınırı yüzünden senin mail adresinin sonundaki kendi sunucu uzantanı kaydetmedi.
* Veritabanında kayıtlı olan e-posta adresin şu an: `AAAA...AAA@dontwannacry.com`
* Kayıt olduğun şifreyle giriş yap. Artık sistem seni `dontwannacry.com` şirketinin bir çalışanı olarak göreceği için üst menüde **Admin Panel** sekmesi belirecek.
* Admin paneline gir ve `carlos` kullanıcısını silerek lab'i tamamla.

---

### 📌 Çıkarılacak Ders (Mitigation)

Kullanıcı girdileri sistemin **tüm katmanlarında (frontend, backend, database)** aynı kurallarla (uzunluk, tip, format) doğrulanmalıdır. Beklenmeyen veya sınırları aşan girdiler sessizce kesilmek (truncate) yerine doğrudan reddedilmelidir (Validation Error).