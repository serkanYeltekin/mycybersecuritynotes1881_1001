# Metasploit Framework Araçları

##  💥 msfconsole

* Metasploit Framework’ün ana terminal arayüzüdür
* Siber güvenlikte açık tarama ve exploit çalıştırma aracıdır
* Penetration testing (sızma testi) için kullanılır

---

#### 🧠 Temel amaç

* Hedef sistemde açık bulmak
* Bulunan açığı kullanarak erişim sağlamak (lab ortamında)
* Payload gönderip kontrol almak

---

### 🔧 Ne işe yarar?

#### 🔍 1. Tarama (Scanning)

* Port ve servis tespiti
* Açık servislerin sürüm bilgisi

#### 💥 2. Exploit çalıştırma

* Bilinen güvenlik açıklarını kullanır
* Hedef sisteme erişim sağlar

#### 🎯 3. Payload gönderme

* Reverse shell açma
* Meterpreter session alma

#### 🧪 4. Post-exploitation

* Sistem bilgisi toplama
* Dosya görüntüleme
* Yetki yükseltme (privilege escalation)

---

### 🖥️ Başlatma

```bash
msfconsole
```

Başlangıç prompt:

```
msf6 >
```

---

#### 📌 Temel komutlar

#### 🔎 Arama

```bash
search exploit_ismi
```

#### 📦 Modül seçme

```bash
use exploit/...
```

#### 🎯 Hedef ayarlama

```bash
set RHOST 192.168.1.10
```

#### 🚀 Çalıştırma

```bash
run
```

---

#### ⚠️ ÖNEMLİ

* Sadece izinli sistemlerde kullanılmalı
* CTF ve lab ortamı için uygundur
* İzinsiz kullanım yasadışıdır

---

#### 🧠 KISA ÖZET

* msfconsole = Metasploit’in ana kontrol paneli
* Exploit + scan + payload işlemleri yapılır
* Siber güvenlikte en güçlü araçlardan biridir

---

## 💥 Meterpreter

👉 Metasploit Meterpreter
👉 Hedef sisteme girdikten sonra kontrolü sağlamak için kullanılan “akıllı terminal”dir.

---

### 🧠 Basit anlatım

Meterpreter =
👉 “Hedef bilgisayarı uzaktan kontrol etmeni sağlayan gelişmiş shell”

Normal shell’den farkı:

* Daha güçlü
* Daha gizli (diskte dosya bırakmaz)
* Modüler (ek özellikler yüklenebilir)

---

### 🔧 Ne yapabilirsin?

### 🖥️ Sistem kontrolü

* Komut çalıştırma
* Sistem bilgisi alma

```bash id="m1"
sysinfo
```

---

### 👤 Kullanıcı bilgisi

```bash id="m2"
getuid
```

---

### 📁 Dosya işlemleri

* Dosya listeleme
* İndirme / yükleme

```bash id="m3"
ls
download file.txt
upload backdoor.exe
```

---

### 📸 İzleme

* Screenshot alma
* Webcam erişimi (izinli lab ortamında)

```bash id="m4"
screenshot
```

---

### 🔐 Hash / parola (yetkiye bağlı)

```bash id="m5"
hashdump
```

---

### 🌐 Ağ bilgisi

```bash id="m6"
ipconfig
netstat
```

---

### 🚀 Nasıl gelir?

Meterpreter genelde şu şekilde açılır:

* Exploit çalıştırılır
* Payload gönderilir
* Reverse connection oluşur

Örnek:

```text id="m7"
Meterpreter session 1 opened
```

---

### 🧠 Neden güçlü?

* Diskte iz bırakmaz (memory-based)
* Modüler yapı (plugin eklenebilir)
* Post-exploitation için tasarlanmıştır

---

### ⚠️ ÖNEMLİ

Meterpreter:

* ✔️ CTF / lab / pentest
* ❌ izinsiz sistemlerde kullanım = yasa dışı

---

### 🧠 KISA ÖZET

* Meterpreter = Metasploit’in gelişmiş shell’i
* Hedef sistemi uzaktan kontrol eder
* Dosya, sistem, ağ, kullanıcı yönetimi yapar
* Pentest ve CTF’te çok kullanılır

---

## 💥 msfvenom

### Ne yapabilir?

- Farklı işletim sistemleri için (Windows, Linux, Android vb.) payload üretmek  
- Bu payload’ları farklı dosya formatlarında hazırlamak (`.exe`, `.apk`, `.elf` gibi)  
- Gerçek saldırıları simüle etmek için kodları şifrelemek veya gizlemek  

### Ne için kullanılır?

Bu araç genelde şu amaçlarla kullanılır:

- Güvenlik açıklarını test etmek  
- Antivirüs ve güvenlik sistemlerinin tepki verip vermediğini görmek  
- Kontrollü ve izinli ortamlarda saldırı simülasyonu yapmak  

### Kullanım şekli 
    windows/meterpreter/reverse_tcp
payload platform / payload type / payload protocol

#### Çalıştırma örnek
-  msfvenom --payload windows/meterpreter/reverse_tcp -- list-options