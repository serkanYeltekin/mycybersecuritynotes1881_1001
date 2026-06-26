### TEMEL KOMUTLAR

---

* komut --help : komutu dökümantasyonu.
* **setxkbmap tr** : klavye dil değişikliği komutu.
* pwd (print working directory): içinde bulunan klasörü yazdırır.
* ls : İçerisinde bulunduğu klasör ve dosyaları gösterir.
* ls -la : klasör ve dosyaları daha detaylı gösterir.
* cd (change directory) : istenen klasöre girmeye yarar. örn -> (cd document).
* mkdir (make directory) : klasör oluştur.
* clear : cmd yi temizler.
* cd .. : 1 dosya (adım) geri gider.
* touch : dosya oluşturur. örn -> touch notes.txt
* cp : kopyalamak örn -> cp notes.txt test/notes.txt (notesin kopyasını testin içine atar).
* mv : kesmek (aynı klasörde olmayınca dosya yolunu yazmak gerek).
* rm : dosyayı kaldırır.
* rm -f : zorla kaldırır.
* cat: Dosyanın tüm içeriğini tek seferde terminale yazdırır.
* more: Dosyayı sayfa sayfa ileri doğru görüntüler.
* less: Dosyayı ileri–geri gezinebilir ve arama yapabilir şekilde görüntüler.


### YETKİ FARKLILIKLARI

---

* su - : root'a geçer.
* su : kullanıcı arası geçiş.
* sudo -l : O anki kullanıcının admin kullanıcısı gibi (örn: root) çalıştırabileceği komutları listeler.


### PAKET YÖNETİCİLERİ

---

* apt update : kısa bi güncelleme yapar.
* apt upgrade : tüm linüxü günceller.
* apt install paketİsmi : istenen paketi yükler.
* apt search paketİsmi : Paket hakkında bilgi verir.
* ifconfig : IP adresini gösterir.



### NANO

---

* nano dosyaİsmi : nanoyu açar.



### GENEL KOMUTLAR

---

* ping internetSitesi : İstenilen siteye istekler atar. ctrl C ile durdurulur.

* openvpn vpnadresi : vpn'e girer.

* netdiscover : ip adresini öğrenmek / bilgi edinmeye yarar

* ssh kullanici@sunucu_adresi : SSH (Secure Shell), uzak bir bilgisayara güvenli şekilde bağlanmayı sağlayan bir protokoldür ve aynı isimli komut bu bağlantıyı başlatır.

* put dosyaismi : karşıya gir dosya yüklemenizi sağlar.

- ps : o an çalışan dosyaları gösterir. (meterpreterde hacklediğimiz cihazda çalışan dosyaları görmek için kullanılır).

- migrate komutu : Çalışan Meterpreter oturumunu (process’i), başka bir process’e taşır. (gizlemek için kullanılır).

- arp -a : kendi ve bizimle iletişimde olan kullanıcıların IP ve MAC'ini gösterir.

- source .venv/bin/activate (Sanal ortama girme.)

### Hata giderme komutları

**ikisini aynı anda çalıştır burpsuite önbelleğini temizler, açılmasını sağlar.**
- rm -rf ~/.BurpSuite
- rm -rf ~/.java/.userPrefs/burp