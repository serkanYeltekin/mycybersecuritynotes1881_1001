# Cyber Security Tools

## 📧 smtp-user-enum (Kali)

👉 SMTP sunucusunda **geçerli kullanıcıları bulur**

---

### 🚀 Kullanım

```bash
smtp-user-enum -M RCPT -U users.txt -t 192.168.1.10
```

---

### 📌 Parametreler

* `-M` → yöntem (VRFY / EXPN / RCPT)
* `-U` → kullanıcı listesi
* `-t` → hedef IP

---

### 📊 Çıktı

* `+` → kullanıcı VAR
* `-` → kullanıcı YOK

---

### 🧠 Özet

👉 Mail sunucusunda **hangi hesaplar var öğrenilir**


## 🧰 Netcat (nc)

**Tanım:**

* Netcat, ağ üzerinden veri gönderip almak için kullanılan **çok amaçlı bir komut satırı aracıdır**
* “**TCP/IP Swiss Army Knife**” olarak bilinir

---

### 🎯 Ne İşe Yarar?

* Port dinleme (listener)
* Bağlantı kurma (client)
* Basit chat / veri transferi
* Port tarama (basit)
* Banner grabbing (servis bilgisi çekme)

---

### ⚙️ Temel Kullanım

**1. Bağlantı kurma (client):**

```bash
nc <hedef_ip> <port>
```

**2. Dinleme (server):**

```bash
nc -l -p <port>
```

---

### 📌 Yaygın Parametreler

* `-l` → listen (dinleme modu)
* `-p` → port belirtir
* `-v` → verbose (detaylı çıktı)
* `-n` → DNS çözümleme yapmaz
* `-z` → sadece port kontrolü (scan)
* `-u` → UDP kullanır

---

### 💡 Örnekler

**Port kontrolü:**

```bash
nc -zv 192.168.1.1 80
```

**Basit chat:**

* Server:

```bash
nc -l -p 1234
```

* Client:

```bash
nc 192.168.1.10 1234
```

**Dosya gönderme:**

* Alıcı:

```bash
nc -l -p 1234 > file.txt
```

* Gönderici:

```bash
nc 192.168.1.10 1234 < file.txt
```

---

### ⚠️ Güvenlik Notu

* Şifreleme yok → veri **düz metin gider**
* Saldırganlar tarafından **backdoor** açmak için kullanılabilir
* Firewall/IDS sistemleri tarafından izlenebilir

---

### 🧠 Kısa Özet

* Netcat = bağlantı kur + veri gönder/al
* Hem client hem server gibi çalışabilir
* Pentest ve debugging için çok kullanılır

## Subbrute

### 🔧 Ne yapar?
- Domain için olası subdomain’leri dener
→ **mail.example.com**, **blog.example.com** gibi
DNS sorguları yaparak var olanları tespit eder
- Sonuçları liste halinde verir veya dosyaya kaydeder

## Gobuster

- Kısaca, bir hedef sistemde gizli klasörleri, dosyaları veya alt alan adlarını brute-force yöntemiyle keşfetmeye yarar.

- Wordlistler üzerinden denemeler yapar. /admin, /source gibi uzantıları bulmaya çalışır.

## LinPEAS (Linux Privilege Escalation Awesome Script)

### Temel Amacı:

- Red team operasyonlarında ve sızma testlerinde, sisteme düşük yetkili giriş yapıldıktan (örneğin web zafiyeti üzerinden) sonra root (en yüksek) yetkisine ulaşacak yolları otomatize ederek bulmaktır.

### Neleri Tarar? (Odak Noktaları):

- Kernel & OS: Mevcut çekirdek (kernel) sürümündeki bilinen açıkları (exploit) arar.

- SUID/SGID Dosyaları: Normal kullanıcının geçici root yetkisiyle çalıştırabildiği, yapılandırması hatalı dosyaları listeler.

- Cron Jobs (Zamanlanmış Görevler): Düşük yetkili kullanıcıların yazabildiği ama root'un arka planda çalıştırdığı betikleri tespit eder.

- Açık Parolalar: Yapılandırma (config), geçmiş komut (.bash_history) veya veritabanı dosyalarında unutulan şifre ve API anahtarlarını çıkarır.

### Renk Kodları (Çıktıyı Hızlı Okuma):

- 🔴🟡 Kırmızı Yazı / Sarı Arka Plan: %99 yetki yükseltme sağlar (İlk bakılacak yer).

- 🔴 Kırmızı: Yüksek ihtimalli zafiyet veya açık şifre.

- 🔵🟢 Mavi/Yeşil: Standart bilgi, sömürülecek bir durum yok.

**Önemli Özellik :**

Sistemde kalıcı değişiklik yapmaz, doğrudan bellek üzerinden (in-memory) çalıştırılabilir.

## 🔐 HASHCAT

### 📌 Hashcat nedir?

- Hash (şifre) kırma aracıdır
GPU/CPU kullanır
Wordlist, mask, brute-force saldırıları yapar

### 📌 Temel kullanım:
- hashcat -m <hash_file>

### 📌 ÖNEMLİ HASH MODE’LAR

- 500 → md5crypt ($1$)
- 1800 → sha512crypt ($6$)
- 1000 → 100 → md5crypt (raw SHA1)

### 📌 SENİN KULLANDIĞIN KOMUTLAR

🔹 **MD5crypt (mode 500):
hashcat.exe -m 500 mylinuxhash.txt wordlist.txt**

🔹 **SHA512crypt (mode 1800):
hashcat.exe -m 1800 mykalihash.txt wordlist.txt**

🔹 **MD5crypt (mode 1000):
hashcat.exe -m 1000 mywindowshash.txt wordlist.txt**

### 📌 ATTACK MODLARI

0 → Wordlist attack
3 → Mask attack
6 → Hybrid (wordlist + mask)
7 → Hybrid (mask + wordlist)

### 📌 ÖRNEKLER

Wordlist:
hashcat -m 1800 hash.txt rockyou.txt

Mask:
hashcat -m 0 -a 3 hash.txt ?d?d?d?d

Hybrid:
hashcat -m 0 -a 6 hash.txt wordlist.txt ?d?d

### 📌 YAYGIN HASH MODE’LARI

- **0 → MD5**
- **500 → MD5crypt ($1$)**
- **100 → SHA1**
- **1700 → SHA512**
- **1800 → SHA512crypt ($6$)**

### 📌 KONTROL KOMUTLARI

hashcat -I → GPU/CPU gör
hashcat -b → benchmark
hashcat --example-hashes → hash örnekleri

## Dirb 
    Dirb yerine artık Gobuster / ffuf / dirsearch tercih ediliyor.

siber güvenlikte kullanılan bir web içerik tarama (content discovery) aracıdır.

### Ne yapar ?
- Gizli dizin ve dosyaları bulur: Web sitesinde görünmeyen /admin, /backup, /test gibi klasörleri keşfetmeye çalışır.
- Wordlist kullanır: Önceden hazırlanmış kelime listeleriyle URL’leri tek tek dener.

### Nasıl çalışır ?
Hedef siteye çok sayıda HTTP isteği gönderir
Sunucudan gelen cevaplara (200, 403, 404 vs.) bakarak hangi dizinlerin var olduğunu anla

## Dirbuster
    Dirbuster yerine artık Gobuster / ffuf / dirsearch tercih ediliyor.

DirBuster, web uygulamalarında gizli dizin ve dosyaları keşfetmek için kullanılan bir güvenlik test aracıdır.

**Kısaca not:**

- DirBuster, özellikle web sunucularında normal yollarla erişilemeyen dizin ve dosyaları (örneğin `/admin`, `/backup`, `/test`) bulmak için kelime listesi (wordlist) ile brute-force yöntemi uygular. OWASP tarafından geliştirilmiştir ve web uygulama güvenlik testlerinde kullanılır.

**Öne çıkan özellikleri:**

* Dizin ve dosya keşfi (directory/file brute force)
* Wordlist tabanlı tarama
* Çok iş parçacıklı (multi-threaded) çalışma
* Gizli yönetim panellerini ve yedek dosyaları bulma

Kullanım amacı genelde penetrasyon testlerinde web uygulamasının açıklarını ve gizli yüzeyini ortaya çıkarmaktır.


## Sqlmap
- SQLMap → Web sitelerinde SQL Injection açıklarını otomatik bulan ve veritabanını sömüren araçtır.

1. Açığı otomatik tespit eder
2. Veritabanı bilgilerini çıkarır
3. Şifre hash’lerini dump edebilir
4. Hatta bazı durumlarda sisteme erişim sağlayabilir

## 🔍 SSLStrip Nedir?

SSLStrip, bir Man-in-the-Middle (MitM) saldırı tekniği kullanarak, normalde HTTPS (şifreli) olması gereken bağlantıyı HTTP (şifresiz) hale düşürür.

**Yani:**

* Kullanıcı güvenli bir siteye girdiğini sanır (örneğin banka sitesi)
* Ama aslında saldırgan, bağlantıyı gizlice şifresiz hale getirir
* Böylece girilen tüm veriler (şifreler, kart bilgileri vb.) okunabilir olur

## weevely

Weevely, PHP tabanlı web uygulamalarında uzaktan yönetim ve güvenlik testleri için kullanılan bir araçtır. Bir PHP ajanı (agent) oluşturur ve bu ajan üzerinden hedef sistemle komut tabanlı iletişim kurulmasını sağlar (Karşı tarafa bağlantı açar).

Temel Özellikler:

* PHP tabanlı ajan oluşturma
* Uzaktan komut çalıştırma
* Dosya yönetimi işlemleri
* Ağ ve sistem bilgisi toplama
* Modüler yapı sayesinde ek özellikler

Temel Kullanım Mantığı:

1. Bir ajan dosyası oluşturulur.
2. Ajan hedef sisteme yüklenir.
3. URL ve parola kullanılarak bağlantı kurulur.
4. Yetkili güvenlik testi kapsamında sistem üzerinde işlemler gerçekleştirilir.

## commix
Commix (Command Injection Exploiter), web uygulamalarındaki komut enjeksiyonu (command injection) zafiyetlerini otomatik olarak tespit etmek ve sömürmek (exploit) için geliştirilmiş, açık kaynaklı bir sızma testi aracıdır. Python ile yazılmıştır ve genellikle siber güvenlik uzmanları tarafından kullanılır.

**Temel Özellikleri**
- Tam Otomasyon: Manuel olarak tespit etmesi ve sömürmesi zor olan komut enjeksiyonu açıklarını otomatik olarak tarar.

- Çoklu Enjeksiyon Tekniği: Farklı zafiyet türlerine karşı üç ana tekniği destekler:

    - Results-based (Sonuç odaklı - çıktının ekrana yansıdığı durumlar)

    - Blind (Kör - çıktının ekranda görünmediği, zamana veya tepkiye dayalı durumlar)

    - Out-of-Band (Bant dışı - farklı bir protokol/kanal üzerinden veri hırsızlığı)

- **Shell (Kabuk) Erişimi:** Zafiyet başarılı bir şekilde sömürüldüğünde, hedef sistemde Reverse Shell veya Web Shell gibi yetkili erişim bağlantıları kurmanızı sağlar.

- **Geniş Uyumluluk:** Python'un çalıştığı her platformda (Linux, Windows, macOS) kullanılabilir. Kali Linux ve Parrot OS gibi güvenlik dağıtımlarında genellikle yüklü olarak gelir.

**Kullanım Amacı**

Web sunucularının arka planında (işletim sistemi seviyesinde) yetkisiz komut çalıştırılıp çalıştırılamayacağını denetlemek, sistemin güvenlik duruşunu ölçmek ve tespit edilen açıkları kapatmadan önce etkisini kanıtlamak (Proof of Concept) amacıyla kullanılır.

## DotDotPwn

> **Dizin Geçişi (Directory Traversal)** zafiyetlerini (Örn: `../../etc/passwd`) tespit eden otomatik fuzzer aracıdır.

### ⚙️ Temel Parametreler

* **`-m`**: Modül seçimi (`http`, `http-url`, `ftp`, `tftp`)
* **`-h`**: Hedef IP veya Domain
* **`-u`**: Hedef URL (Test edilecek parametreye **`TRAVERSAL`** yazılır)
* **`-d`**: Dizin atlama derinliği (Varsayılan: 6)
* **`-f`**: Okunacak dosya (Örn: `/etc/passwd` veya `windows/win.ini`)
* **`-O`**: Nmap ile işletim sistemi tespiti (Yanlış payloadları eleyerek zaman kazandırır)

---

### 💻 Hızlı Kullanım Örnekleri

**1. Standart Tarama:**

```bash
dotdotpwn -m http -h 192.168.1.50

```

**2. Belirli Bir URL Parametresini Tarama (OS Tespiti ile):**

```bash
dotdotpwn -m http-url -u "http://192.168.1.50/index.php?file=TRAVERSAL" -O

```

**3. Özel Derinlik ve Hedef Dosya Belirleme:**

```bash
dotdotpwn -m http -h 192.168.1.50 -d 8 -f /etc/shadow
```

## hash-identifier
Hash-Identifier, verilen bir hash değerinin hangi algoritma ile oluşturulmuş olabileceğini tahmin etmeye yarayan bir araçtır. MD5, SHA-1, SHA-256 gibi farklı hash türlerini hızlıca analiz ederek olası eşleşmeleri listeler. Özellikle sızma testleri ve adli bilişim çalışmalarında, hash türünü belirlemek için pratik bir başlangıç noktası sağlar.

## WAFW00F

WAFW00F, web uygulamalarının arkasında bir **Web Application Firewall (WAF)** olup olmadığını tespit etmek için kullanılan bir güvenlik aracıdır.

Temel amacı, bir web sitesinin hangi WAF teknolojisini kullandığını (örneğin Cloudflare, AWS WAF, Imperva vb.) analiz etmektir. Bunu yaparken HTTP istekleri gönderir ve gelen yanıtları çeşitli imza ve davranış kalıplarıyla karşılaştırır.

**Öne çıkan özellikleri:**

* Hedef sitede WAF olup olmadığını tespit eder
* Kullanılan WAF türünü tahmin edebilir
* Birden fazla teknik (header analizi, hata mesajları, davranış analizi) kullanır
* Pentest ve güvenlik analizlerinde yaygın kullanılır
* wafw00f google.com şeklinde kullanılır.


## LBD (Load Balancing Detector) kısa not

LBD, bir web sitesinin arkasında **load balancing (yük dengeleme)** olup olmadığını tespit etmek için kullanılan basit bir güvenlik aracıdır.

Temel amacı, bir alan adının tek bir sunucuya mı yoksa birden fazla sunucuya mı yönlendirildiğini anlamaktır. Bunu genellikle DNS yanıtları ve HTTP cevaplarındaki tutarsızlıkları analiz ederek yapar.

**Öne çıkan özellikleri:**

* Hedefte load balancer olup olmadığını tespit eder
* Farklı IP veya sunucu yanıtlarını karşılaştırır
* Basit ve hızlı bir kontrol sağlar
* Pentest ve ağ keşif aşamasında kullanılır
* lbd google.com şeklinde kullanılır.

## Nikto

* **Tanım:** Nikto, açık kaynaklı bir **web sunucu güvenlik tarama aracıdır**.
* **Amaç:** Web sunucularındaki zafiyetleri, yanlış yapılandırmaları ve bilinen açıkları tespit eder.
* **Özellikler:**

  * 6000+ potansiyel tehlikeli dosya/program taraması
  * Eski yazılım sürümlerini tespit etme
  * Sunucu yapılandırma hatalarını bulma
  * SSL ve HTTP başlık analizleri
* **Kullanım Alanı:** Penetrasyon testleri ve güvenlik analizleri
* **Avantaj:** Hızlı ve kapsamlı tarama, kolay kullanım
* **Dezavantaj:** Gürültülü (çok istek gönderir), IDS/IPS sistemlerine kolay yakalanır

**Örnek komut:**

```bash
nikto -h http://hedefsite.com
```

İstersen daha detaylı kullanım parametrelerini veya örnek senaryoları da ekleyebilirim.
