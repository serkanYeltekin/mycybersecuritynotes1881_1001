## Lab: Weak isolation on dual-use endpoint - Çözüm Raporu

**Zafiyetin Özeti (Kök Neden)**
"Dual-use endpoint" (çift kullanımlı uç nokta), hem normal kullanıcıların kendi parolalarını güncellediği hem de yöneticilerin diğer kullanıcıların parolalarını sıfırladığı tek bir API noktasıdır. Uygulama, mevcut parola parametresi (current password) istekte hiç bulunmadığında, isteğin bir "şifre sıfırlama" işlemi olduğunu varsayarak yetki kontrolünü tam yapmamış ve doğrulama adımını atlamıştır.

1. **Keşif ve Normal Kullanıcı ile Giriş:**
Laboratuvar ortamına verilen standart kullanıcı kimlik bilgileriyle giriş yapıldı. Hedef, `administrator` hesabını ele geçirip yetkisiz erişim sağlayarak `carlos` kullanıcısını silmekti.


2. **Form Manipülasyonu ve İlk Deneme:** Parametre boş bırakılıyor.
Hesap ayarları kısmındaki parola değiştirme işlemi incelendi. İsteği manipüle etmek amacıyla `username` değeri `administrator` olarak değiştirildi. Yeni parola girilirken mevcut parola (current password) kısmı **boş bırakılarak** istek gönderildi. Sunucu, boş string (`""`) değerini doğrulamaya çalışıp başarısız olduğu için bu işlem reddedildi.


3. **Parametre Silme (Parameter Omission) ile Zafiyetin Sömürülmesi:** Burp Suite ile araya girildi.
Parola değiştirme isteği Burp Suite Proxy üzerinden yakalandı. İstek gövdesinde (body) yer alan mevcut parola doğrulama parametresi (örneğin `current-password=`) **istekten tamamen silindi**. Sadece `username=administrator` ve yeni parola parametreleri bırakılarak istek sunucuya iletildi. Uygulama, doğrulama parametresini göremediği için kontrolü atladı ve yöneticinin şifresini başarıyla güncelledi.


4. **Görevin Tamamlanması:**
Mevcut oturumdan çıkış yapıldı ve oluşturulan yeni parola ile `administrator` hesabına başarılı bir şekilde giriş yapıldı. Admin paneline (`/admin`) erişilerek `carlos` kullanıcısı silindi ve laboratuvar tamamlandı.


Ellerine sağlık, doğrulama parametresini boş bırakmak yerine tamamen silmeyi denemek harika bir pentester refleksi!