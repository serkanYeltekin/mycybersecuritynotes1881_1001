Bu lab, e-ticaret sistemlerindeki indirim kuponu ve hediye kartı (gift card) mantık hatalarının nasıl birleşerek kritik bir finansal zafiyete (Business Logic Flaw) dönüştüğünü gösteren harika bir örnektir.

Daha sonra dönüp okuduğunda hem zafiyetin mantığını hem de Burp Suite ile otomasyon adımlarını kolayca hatırlayabilmen için hazırladığım write-up:

## 💰 Zafiyetin Mantığı (Vulnerability Logic)

Buradaki temel güvenlik zafiyeti, **nakit paraya eşdeğer olan "Hediye Kartı" (Gift Card) alımlarında indirim kuponu kullanılmasına izin verilmesidir.**

Sistem matematiği şu şekilde hatalı çalışır:

1. **Hediye Kartı Değeri:** $10.00
2. **%30 İndirim Kuponu (SIGNUP30):** -$3.00
3. **Kasadan Çıkan Tutar (Ödenen):** $7.00
4. **Hesaba Yüklenen Bakiye:** $10.00
5. **Her Döngüde Net Kâr:** $3.00

Bu döngüyü otomatize ettiğimizde, mağaza kredimizi (Store Credit) sonsuza kadar artırabiliriz.

---

## 🔍 Keşif Aşaması (Discovery)

Zafiyeti bulmak için sürecin normal akışını manuel olarak test etmemiz gerekiyor:

1. Sisteme kayıt olup bültene abone olarak **`SIGNUP30`** kupon kodunu elde ettik.
2. Sepete $10 değerinde bir hediye kartı ekledik.
3. Sepette **`SIGNUP30`** kodunu uyguladık ve fiyatın $7'a düştüğünü gördük.
4. Siparişi tamamladık ve ekrandaki hediye kartı kodunu kopyaladık.
5. "My Account" sayfasına gidip bu kodu bozdurduk (redeem).
6. **Sonuç:** Hesabımızdan $7 çıktı, ancak $10 eklendi. Toplam bakiyemiz $3 arttı.

Bu noktada zafiyeti doğruladık. Asıl zorluk, hedefteki pahalı deri ceketi (yaklaşık $1337) alabilmek için bu $3'lık kârı yüzlerce kez elde etmektir. Bunu manuel yapmak yerine Burp Suite'in **Macro** özelliğini kullanıyoruz.

---

## ⚙️ Exploit Aşaması: Burp Suite ile Otomasyon

Bu adımlar, manuel yaptığımız satın alma ve kodu bozdurma işlemlerini Burp Suite'e öğretip döngüye soktuğumuz kısımdır. Sıralama ve parametre aktarımı (önceki yanıtın bir sonraki isteğe eklenmesi) kritik öneme sahiptir.

1. **Session Handling (Oturum Yönetimi) Kuralı Oluşturma:** Makronun çalışacağı kapsamı belirliyoruz.
Burp'te üst menüden **Settings > Sessions** sekmesine git. "Session handling rules" bölümünde **Add** butonuna tıkla. Açılan pencerede **Scope** sekmesine geçip URL scope altından **Include all URLs** seçeneğini işaretle. Bu, kuralımızın tüm sayfalarda tetiklenebilmesini sağlar.


2. **Makro Kaydını Başlatma:** Satın alma zincirini seçiyoruz.
Aynı pencerede **Details** sekmesine dön. Rule actions kısmında **Add > Run a macro** seçeneğine tıkla. Açılan listeden **Add** diyerek Macro Recorder'ı (Makro Kaydedici) aç. Proxy geçmişinden sırasıyla şu istekleri seç:

* `POST /cart` (Sepete ekleme)
* `POST /cart/coupon` (Kupon uygulama)
* `POST /cart/checkout` (Satın alma)
* `GET /cart/order-confirmation?order-confirmed=true` (Kodu görüntüleme)
* `POST /gift-card` (Kodu hesaba yükleme)

Seçimi tamamlayıp OK'e tıkla.


3. **Hediye Kartı Kodunu Yanıttan Çıkartma:** Dinamik veriyi yakalama.
Macro Editor açıldığında, listeden kodu ekranda gördüğümüz `GET /cart/order-confirmation...` isteğini seç ve **Configure item**'a tıkla.
Açılan pencerede **Add** diyerek yeni bir custom parametre oluştur. Parametre adını `gift-card` yap. Yanıt (response) gövdesinin en altındaki üretilmiş hediye kartı kodunu fare ile seçerek (highlight) Burp'ün bu kodu dinamik olarak yakalamasını sağla. İki kez OK diyerek Macro Editor'e dön.


4. **Kodu Bir Sonraki İsteğe Aktarma:** Parametre zincirleme (Parameter Handling).
Macro Editor listesinden son istek olan `POST /gift-card` isteğini seç ve **Configure item**'a tıkla. "Parameter handling" bölümünde, `gift-card` parametresinin **bir önceki isteğin yanıtından (response 4)** alınacağını belirt. OK diyerek ana Burp ekranına kadar pencereleri kapat. *(Kapatmadan önce Test Macro diyerek kodun başarıyla alınıp 302 yanıtı döndürdüğünü teyit edebilirsin).*


5. **Intruder ile Döngüyü Başlatma:** Hedefe ulaşmak için saldırıyı tetikleme.
Proxy geçmişinden `GET /my-account` isteğini sağ tıklayıp **Send to Intruder** de. Intruder sekmesinde attack type olarak **Sniper** seç.

* **Payloads:** Payload type olarak **Null payloads** seç ve "Generate" kısmına **412** yaz (Ceketi alacak bakiyeye ulaşmak için gereken döngü sayısı).
* **Resource Pool:** Bu işlemlerin sırayla ve hatasız yapılması gerektiği için, Resource pool sekmesinden "Maximum concurrent requests" (Eşzamanlı istek) ayarını **1** yap.


### 🎯 Çözüm

Saldırıyı başlat (Start attack). Burp Suite, gönderdiği her boş (Null) payload için arka planda yazdığın Makroyu (Sepete ekle > Kupon gir > Al > Kodu kopyala > Bozdur) tek tek çalıştıracaktır.

Saldırı bittiğinde sayfayı yenile. Bakiyenin deri ceketi alacak kadar (yaklaşık $1300+) arttığını göreceksin. Ceketi sepete ekleyip satın aldığında lab çözülmüş olacaktır.