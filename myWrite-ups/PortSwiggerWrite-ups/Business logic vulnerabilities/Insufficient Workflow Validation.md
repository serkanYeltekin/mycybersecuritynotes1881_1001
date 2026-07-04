## PortSwigger Lab: Insufficient Workflow Validation

### 🎯 Labın Amacı

Hesabınızda yeterli bakiye (store credit) olmamasına rağmen, sistemdeki iş akışı doğrulama zafiyetinden faydalanarak **"Lightweight l33t Leather Jacket"** isimli pahalı ürünü satın almak.

### 🧠 Zafiyetin Temel Nedeni (Root Cause)

Uygulama, satın alma işleminin adımlarını (Sepete Ekle ➔ Ödeme Yap ➔ Siparişi Onayla) zorunlu bir sıraya koymaz. Sistem, `/cart/order-confirmation` (sipariş onay) sayfasına erişen bir kullanıcının ödeme adımını zaten başarıyla geçtiğini **varsayar** ve ödemenin gerçekten yapılıp yapılmadığını sunucu tarafında tekrar kontrol etmez.

---

### 🛠️ Adım Adım Çözüm (Exploitation)

1. **Sisteme Giriş ve Analiz:**
* Size verilen kimlik bilgileriyle (genellikle `wiener:peter`) uygulamaya giriş yapın.
* Burp Suite'i açın ve tarayıcı trafiğinizi dinlemeye (proxy) başlayın.
* Kredi bakiyenizin (örneğin $100) ceketi almaya yetmediğini göreceksiniz.


2. **Normal Akışı İnceleme:**
* Bakiyenizin yettiği çok ucuz bir ürünü sepete ekleyin.
* Sepete gidin ve normal bir şekilde "Place order" (Siparişi tamamla) butonuna tıklayarak satın alın.
* Burp Suite'in **HTTP history** sekmesinden bu işlemi inceleyin. Sipariş tamamlandığında uygulamanın sizi `GET /cart/order-confirmation?order-confirmation=true` uç noktasına yönlendirdiğini göreceksiniz.


3. **Zafiyeti Sömürme:**
* Şimdi asıl hedefimiz olan pahalı ürünü (**Lightweight l33t Leather Jacket**) sepete ekleyin.
* Sepet (Cart) sayfasına gidin. Eğer "Place order" butonuna basarsanız, sistem bakiyenizin yetersiz olduğunu söyleyecek ve işlemi reddedecektir.
* **Bypass Adımı:** "Place order" butonuna basmak (yani ödeme adımından geçmek) yerine, tarayıcınızın URL çubuğuna gidip adresi doğrudan onay sayfasıyla değiştirin:
`https://<LAB-ID>.web-security-academy.net/cart/order-confirmation?order-confirmation=true`
* Enter'a basın.


4. **Sonuç:**
* Uygulama, sipariş onay sayfasına doğrudan eriştiğiniz için ödemeyi yaptığınızı varsayacak ve ceketin siparişini onaylayacaktır.
* Lab başarıyla çözülmüş (Solved) olacaktır!



---

### 🛡️ Nasıl Önlenir? (Remediation)

Bu tür zafiyetlerin önüne geçmek için geliştiricilerin şunlara dikkat etmesi gerekir:

* **Durum (State) Kontrolü:** Kullanıcının çok adımlı bir işlemin hangi aşamasında olduğu sunucu tarafında (örneğin oturum değişkenleri aracılığıyla) takip edilmelidir.
* **Adım Doğrulaması:** Kullanıcı 3. adıma (Sipariş Onayı) geçmek istediğinde, sunucu "Bu kullanıcı 2. adımı (Ödeme) başarıyla tamamladı mı?" sorusunu sormalı ve veritabanı/oturum üzerinden bunu doğrulamalıdır. Sadece URL'ye erişebilmek işlemi onaylamak için yeterli olmamalıdır.