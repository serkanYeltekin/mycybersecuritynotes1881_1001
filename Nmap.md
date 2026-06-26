Kali Linux üzerinde **Nmap (Network Mapper)**, ağ keşfi, güvenlik denetimleri ve zafiyet tespiti için siber güvenlik uzmanlarının en çok kullandığı araçların başında gelir. 

Aşağıda, Nmap kullanırken en çok ihtiyaç duyacağınız komutları ve kullanım amaçlarını kategorize edilmiş şekilde bulabilirsiniz:

### 1. Temel ve Ağ Keşfi Taramaları
Bir ağda hangi cihazların aktif olduğunu bulmak için kullanılır.

* **Tek bir hedefi tarama:**
    ```bash
    nmap 192.168.1.1
    ```
    Hedefin en çok bilinen 1000 portunu tarar. Hedef olarak IP adresi veya domain (`nmap ornek.com`) girilebilir.
* **Ağdaki aktif cihazları bulma (Ping Scan):**
    ```bash
    nmap -sn 192.168.1.0/24
    ```
    Sadece hangi cihazların açık olduğunu (ayakta olduğunu) bulur, port taraması yapmaz. Ağı hızlıca haritalamak için idealdir.

### 2. Gizlilik ve Tarama Teknikleri
Firewall (Güvenlik Duvarı) ve IDS/IPS sistemlerini atlatmak veya loglara daha az düşmek için kullanılan tekniklerdir.

* **TCP SYN Taraması (Gizli Tarama - Stealth Scan):**
    ```bash
    sudo nmap -sS 192.168.1.1
    ```
    *En çok kullanılan tarama türüdür.* TCP 3'lü el sıkışma (3-way handshake) sürecini yarıda keserek hedef sistemin loglarında iz bırakma ihtimalini düşürür. (Çalıştırmak için "root" yetkisi yani `sudo` gerektirir).
    
* **TCP Connect Taraması:**
    ```bash
    nmap -sT 192.168.1.1
    ```
    Bağlantıyı tamamen kurar. Çok fazla iz bırakır ancak root yetkiniz yoksa kullanabileceğiniz en güvenilir yöntemdir.
* **UDP Taraması:**
    ```bash
    sudo nmap -sU 192.168.1.1
    ```
    Sadece UDP üzerinden çalışan servisleri (DNS, SNMP, DHCP vb.) tespit etmek için kullanılır.

### 3. Bilgi Toplama (Servis ve İşletim Sistemi)
Açık portların arkasında nelerin çalıştığını anlamak için kullanılır.

* **Servis Versiyonu Tespiti:**
    ```bash
    nmap -sV 192.168.1.1
    ```
    Açık olan portlarda çalışan uygulamaların tam sürümlerini (örn: Apache 2.4.49, OpenSSH 8.2) listeler. Zafiyet aramak için çok önemlidir.
* **İşletim Sistemi (OS) Tespiti:**
    ```bash
    sudo nmap -O 192.168.1.1
    ```
    Hedefin kullandığı işletim sistemini (Windows, Linux, macOS vb.) tahmin etmeye çalışır.
* **Agresif Tarama (Tümü Bir Arada):**
    ```bash
    sudo nmap -A 192.168.1.1
    ```
    İşletim sistemi tespiti (`-O`), versiyon tespiti (`-sV`), script taraması (`-sC`) ve Traceroute işlemlerini tek bir komutla yapar. Oldukça gürültülüdür, güvenlik sistemleri tarafından hemen fark edilir.

### 4. Port Seçimi
Belirli portlara odaklanarak tarama süresini kısaltmak için kullanılır.

* **Belirli portları tarama:**
    ```bash
    nmap -p 22,80,443 192.168.1.1
    ```
* **Port aralığı tarama:**
    ```bash
    nmap -p 1-1000 192.168.1.1
    ```
* **Bütün portları (65535) tarama:**
    ```bash
    nmap -p- 192.168.1.1
    ```
    Hızlı ancak en kapsamlı taramadır, hedefteki gözden kaçabilecek hiçbir portu atlamaz.

### 5. Nmap Scripting Engine (NSE)
Nmap'in içine gömülü olan scriptleri kullanarak spesifik zafiyet taramaları yapmanızı sağlar.

* **Varsayılan Scriptleri Çalıştırma:**
    ```bash
    nmap -sC 192.168.1.1
    ```
    Nmap'in "default" kategorisindeki bilgi toplama ve temel güvenlik scriptlerini çalıştırır.
* **Zafiyet (Vulnerability) Taraması Yapma:**
    ```bash
    nmap --script vuln 192.168.1.1
    ```
    Hedefteki bilinen güvenlik açıklarını (CVE'leri) test eden scriptleri tetikler.

### 6. Performans ve Zamanlama (Hız Ayarları)
Taramanın hızını ayarlamak için `-T` parametresi kullanılır. (0 ile 5 arası değer alır).

* **Hızlı Tarama (Genelde tavsiye edilen):**
    ```bash
    nmap -T4 192.168.1.1
    ```
    Daha dengeli ve hızlı bir tarama sunar. (`-T5` en hızlısıdır ama ağda paket kaybına neden olup yanlış sonuçlar verebilir. `-T0` ve `-T1` ise IDS sistemlerinden kaçmak için çok yavaş tarama yapar).

### 7. Çıktıyı Kaydetme
Elde ettiğiniz tarama sonuçlarını daha sonra incelemek veya raporlamak için kaydetmeniz gerekebilir.

* **Normal Metin Formatında Kaydetme:**
    ```bash
    nmap 192.168.1.1 -oN sonuc.txt
    ```
* **Grep Edilebilir (Araçlarla entegre edilebilir) Format:**
    ```bash
    nmap 192.168.1.1 -oG sonuc.gnmap
    ```
* **Tüm Formatlarda Kaydetme:**
    ```bash
    nmap 192.168.1.1 -oA tum_sonuclar
    ```
    Bu komut `tum_sonuclar.nmap`, `tum_sonuclar.gnmap` ve `tum_sonuclar.xml` isimli üç farklı dosya oluşturur.

---
> **Not:** Nmap son derece güçlü bir araçtır. Yetkiniz veya izniniz olmayan sistemler üzerinde tarama yapmak (özellikle agresif veya zafiyet taramaları) yasa dışı kabul edilebilir. Her zaman etik kurallar çerçevesinde ve izinli hedeflerde (örn: HackTheBox, TryHackMe veya kendi test ağınız) kullanın.