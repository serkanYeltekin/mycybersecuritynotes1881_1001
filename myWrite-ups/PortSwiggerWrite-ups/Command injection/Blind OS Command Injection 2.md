# Lab: Blind OS Command Injection with Output Redirection

## Amaç

Komut enjeksiyonu gerçekleşmesine rağmen uygulama komut çıktısını HTTP yanıtında göstermiyorsa (blind OS command injection), çıktıyı web sunucusunun erişebildiği bir dosyaya yönlendirerek sonucu doğrulamayı öğrenmek.

## Temel Mantık

* Uygulama arka planda işletim sistemi komutları çalıştırmaktadır.
* Komutun çıktısı kullanıcıya gösterilmez.
* Çıktı, web sunucusunun yayınladığı (static) bir dizindeki dosyaya yazdırılır.
* Daha sonra bu dosya tarayıcıdan açılarak komutun çıktısı okunur.

## Output Redirection (`>`)

`>` operatörü standart çıktıyı bir dosyaya yönlendirir.

Örnek:

```bash
whoami > /var/www/static/whoami.txt
```

Anlamı:

* `whoami` çalışır.
* Çıktısı ekrana değil `whoami.txt` dosyasına yazılır.
* Dosya mevcut değilse oluşturulur.
* Dosya mevcutsa üzerine yazılır.

## Başarılı Olması İçin

* Hedef dizin (ör. `/var/www/static`) mevcut olmalıdır.
* Uygulamanın çalıştığı kullanıcı bu dizine yazabilmelidir.
* Web sunucusu bu dizindeki dosyaları HTTP üzerinden yayınlıyor olmalıdır.

## Blind Command Injection'dan Farkı

* **Time Delay yöntemi:** Komutun çalıştığı gecikme ile anlaşılır.
* **Output Redirection yöntemi:** Komutun çıktısı bir dosyaya yazılır ve sonradan okunur.

## Önemli Noktalar

* `>` : Dosyaya yazar (üzerine yazar).
* `>>` : Dosyanın sonuna ekler.
* Çıktı HTTP cevabında görünmeyebilir; bu nedenle dosyaya yönlendirme kullanılır.
* Dosyanın önceden var olması gerekmez; uygun izinler varsa otomatik oluşturulur.

## Öğrenilen Kavramlar

* Blind OS Command Injection
* Output Redirection (`>`, `>>`)
* Web Root / Static Directory
* Standart Çıktı (stdout)
* Dosya izinleri (write permission)
