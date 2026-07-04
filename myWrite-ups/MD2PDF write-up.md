# 🧩 WRITE-UP

---

## 🏷️ Başlık

* Cross-Site Scripting (XSS) → Admin Bot Exploitation

---

## 🎯 Hedef

* TryHackMe MD2PDF CTF 

---

## 🔍 Keşif (Recon)

* İlk olarak sayfanın kaynak kodunu ve HTML yapısını inceledim.
* Input alanlarının kullanıcı girdisini filtrelemeden sayfaya bastığını fark ettim.
* Endpoint keşfi yaptım ve `/admin` gibi potansiyel yolları kontrol ettim.

---

## 🧪 Denemeler

* Nmap ile hedef serviste açık portları kontrol ettim.
* Gobuster ile web dizin taraması yaptım.
* `/admin` endpointine doğrudan erişmeye çalıştım ancak erişim engellendi.
* `/admin` sayfasının yalnızca **localhost (127.0.0.1)** üzerinden erişilebilir olduğu tespit edildi.

---

## 💥 Açığın Bulunması

* Sayfaya HTML injection yapılabildiğini fark ettim.
* Burp Suite ile request/response trafiğini analiz ettim.
* Kullanıcı inputunun sanitize edilmeden DOM’a basıldığını gördüm.
* Bu durum **XSS (Cross-Site Scripting)** zafiyetine işaret ediyordu.

---

## ⚙️ Exploit

Aşağıdaki payload kullanıldı:

```html
<iframe src="http://127.0.0.1:5000/admin"></iframe>
```

### 🔥 Açıklama:

Bu payload, XSS bulunan alana enjekte edilir.

* Bu sayfa daha sonra **admin bot tarafından ziyaret edilir**
* Payload admin botun tarayıcısında çalışır (client-side execution)
* iframe, admin botun kendi ortamında şu isteği yapar:

```
http://127.0.0.1:5000/admin
```

* Çünkü `127.0.0.1` admin bot için **kendi localhost’u** anlamına gelir
* Böylece admin paneli açılır ve flag görüntülenebilir

---

## 🧠 Öğrendiklerim

* **XSS (Cross-Site Scripting)**: Kullanıcı inputunun filtrelenmeden HTML/JS olarak çalıştırılmasıdır.
* **iframe**, tarayıcıya verilen URL’yi ayrı bir sayfa olarak yükler.
* Execution server-side değil, **client-side (tarayıcıda)** gerçekleşir.
* `localhost` her zaman **çalıştıran kişinin makinesini ifade eder**.
* CTF’lerde bu tip açıklar genellikle **admin bot üzerinden exploit edilir**.

---

## ⚠️ Önemli Notlar

* iframe tek başına veri çalmaz, sadece sayfa yükler.
* Bu saldırı ancak **admin bot payload’ı ziyaret ederse çalışır**.
* Amaç “admin’e girmek” değil, **admin’in tarayıcısında kod çalıştırmaktır**.

---

## 🚀 Sonuç

Bu zafiyet sayesinde XSS kullanılarak admin botun localhost ortamına istek gönderildi ve `/admin` endpointine erişim sağlandı. Bu endpoint üzerinden flag elde edildi.
