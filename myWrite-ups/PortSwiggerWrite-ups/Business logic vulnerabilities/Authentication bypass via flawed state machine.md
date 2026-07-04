## Lab: Authentication bypass via flawed state machine

**Amaç:** Yönetici (Admin) haklarını elde etmek ve `carlos` kullanıcısını sistemden silmek.
**Kullanılan Araçlar:** Tarayıcı, Burp Suite

1. **Keşif ve Hedef Belirleme:**
İlk olarak sistemde basit bir endpoint keşfi yaptın ve `/admin` adında bir panel olduğunu buldun. Ancak doğrudan `[https://hedef-site.com/admin](https://hedef-site.com/admin)` adresine gitmeye çalıştığında yetkisiz erişim hatası aldın. Hedefin belli olmuştu: Bir şekilde bu panele erişim hakkı kazanmak.


2. **Uygulamanın Davranışını Analiz Etme:**
Sana verilen standart kullanıcı hesabıyla (`wiener` - parolası genelde `peter`'dır) giriş yaptın. Sistem, girişten hemen sonra seni standart ana sayfaya atmak yerine bir **rol seçme ekranına** yönlendirdi.


3. **Parametre Manipülasyonu (Başarısız Deneme):**
Ekranda bir rol seçip, giden POST isteğini Burp Suite ile yakaladın. Amacın, gönderilen rol parametresini `admin` veya benzeri bir değere çevirerek yetki yükseltmekti. Ancak sunucu bu basit manipülasyonu yemedi ve denemen başarısız oldu. *Bunun işe yaramaması, zafiyetin veride değil, uygulamanın durum/akış (state) kontrolünde olduğunu gösteriyordu.*


4. **Giriş Akışını (State Machine) Yakalama:**
Hesaptan çıkış yapıp tekrar giriş yaptın. Bu sefer Burp Suite'te **Intercept'i en başından açarak** aradaki tüm yönlendirmeleri (Redirects) adım adım izlemeye başladın. Kullanıcı adı ve şifreni gönderdikten hemen sonra, tam rol seçme ekranı yüklenmeden önce tarayıcının `GET /role-selector` isteğini yaptığını gördün.


5. **Zafiyeti Tetikleme: İsteği Drop'lama (Bypass):**
İşte işi çözen hamle: Burp Suite üzerinden bu `GET /role-selector` isteğine **Drop** diyerek bağlantıyı kopardın/engelledin. Uygulama, rol seçimi adımını atladığın için durum makinesindeki (state machine) beklentisini karşılayamadı ve hata durumuna düşerek (fail-open) seni sistemin varsayılan en yüksek yetkili kullanıcısı olan **admin** konumuna getirdi.


6. **Sonuç: Carlos'u Silme:**
Artık admin yetkilerine sahip olarak en başta bulduğun `/admin` paneline erişebildin ve oradan `carlos` kullanıcısını silerek laboratuvarı tamamladın.


---

### 💡 İşin Mantığı: Neden İşe Yaradı? (Kendi Notun)

**Flawed State Machine (Kusurlu Durum Makinesi)** zafiyetleri, geliştiricilerin uygulamanın adımlarını (A -> B -> C) kullanıcının kesinlikle sırasıyla geçeceğini varsaymasından kaynaklanır.

Bu senaryoda geliştirici şöyle düşünmüştü:

1. Kullanıcı giriş yapar (Sistem arkada ona geçici olarak admin veya süper yetki atar).
2. Kullanıcı `role-selector` sayfasına yönlendirilir.
3. Kullanıcı rolünü seçer ve sistem onun yetkisini seçtiği role **düşürür** (downgrade).

Sen aradaki **2. ve 3. adımı (GET /role-selector) Burp ile drop edip atladığında**, sistem yetkini düşürme işlemini yapamadı. Dolayısıyla 1. adımdaki o yüksek yetkili "başlangıç durumu" senin üzerinde kalmış oldu.

Bir dahaki sefere mantıksal hataları (Business Logic Flaws) ararken, bir işlemi yarıda kesmenin, sayfaları atlamanın veya adımları sondan başa doğru yapmanın sistemi nasıl şaşırtabileceğini hatırlamak için bu güzel bir örnek!