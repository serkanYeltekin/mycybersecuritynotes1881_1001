## 📌 FTP (file transfer protocol) :
- internet üzerinden iki bilgisayar arasında dosya alışverişi yapmak için kullanılan bir iletişim protokolüdür.

## 📌 TELNET vs SSH NOTLARI

🔹 TELNET NEDİR?

* Uzak bir bilgisayara bağlanmayı sağlar
* Komut satırı üzerinden kontrol edilir
* Eski bir protokoldür

🔹 TELNET ÖZELLİKLERİ

* Varsayılan port: 23
* Şifreleme YOK (plain text)
* Güvensiz ❌
* Daha çok test ve eğitim amaçlı kullanılır

🔹 TELNET KULLANIM ÖRNEĞİ
telnet example.com 80
→ Port açık mı kontrol eder

---

🔹 SSH NEDİR?

* Uzak sistemlere güvenli bağlantı sağlar
* Telnet’in güvenli alternatifidir

🔹 SSH ÖZELLİKLERİ

* Varsayılan port: 22
* Şifreleme VAR ✅
* Güvenli ✔️
* Günümüzde standarttır

🔹 SSH KULLANIM ÖRNEĞİ
ssh kullanıcı@ip_adresi
→ Uzak makineye güvenli giriş

---

🔥 TELNET vs SSH KARŞILAŞTIRMA

Telnet:

* Şifreleme: ❌ Yok
* Güvenlik: ❌ Düşük
* Kullanım: Test / eski sistemler

SSH:

* Şifreleme: ✅ Var
* Güvenlik: ✅ Yüksek
* Kullanım: Gerçek bağlantılar

---

💡 ÖZET
Telnet = Eski ve güvensiz
SSH = Yeni ve güvenli

## SAMBA
Samba, Linux/Unix sistemlerin Windows ağlarıyla dosya ve yazıcı paylaşmasını sağlayan bir servis/uygulama paketidir.

---

## "exploit/multi/handler" modülü

exploit/multi/handler, Metasploit’te gerçek bir hedefe saldıran exploit değildir. Tam tersine, “dinleyici (listener)” görevi görür.

### Ne işe yarar?

- Bu modül, geri bağlantı (reverse connection) bekleyen payload’ları karşılamak için kullanılır.

#### Yani:

- Hedef makine sana bağlanacaksa, onu karşılayacak “açık kapı” bu modüldür.

Basit mantık
- Sen payload’u bir hedefe gönderirsin
Payload çalışır ve geri sana bağlanmaya çalışır (reverse shell / meterpreter)
multi/handler bu bağlantıyı yakalar



## OWASP ZAP
- OWASP ZAP → Web uygulamalarındaki güvenlik açıklarını bulmak için kullanılan proxy tabanlı otomatik tarama aracıdır.

1. Pasif tarama (trafik dinler, riskleri bulur)
2. Aktif tarama (siteye saldırı simülasyonu yapar)
3. Spider (siteyi otomatik gezer)
4. Proxy (istekleri yakala & değiştir)
5. XSS, SQLi, CSRF gibi açıkları tespit eder


## ARP nedir ?

ARP (Address Resolution Protocol), bilgisayar ağlarında kullanılan bir protokoldür ve temel görevi IP adresini MAC adresine çevirmektir.

### Kısaca ne işe yarar?

Bir cihaz ağda başka bir cihaza veri göndermek istediğinde:

- IP adresini bilir (örneğin: 192.168.1.5)
- Ama veriyi fiziksel olarak göndermek için **MAC adresine** ihtiyaç duyar

*İşte burada ARP devreye girer 👇*

#### Nasıl çalışır?
1. Cihaz, “Bu IP adresine sahip olan kim?” diye ağda yayın (broadcast) yapar
2. İlgili cihaz cevap verir: “Benim MAC adresim şu”
3. Gönderen cihaz bu bilgiyi kaydeder (ARP cache)
4. Artık veri doğru MAC adresine gönderilir
#### Basit örnek
- Senin bilgisayarın: 192.168.1.10
- Router: 192.168.1.1

Bilgisayar router’a veri göndermek ister →
ARP kullanarak router’ın MAC adresini öğrenir →
Sonra veriyi gönderir

Önceki sorunuzun bir siber güvenlik aracı (Commix) hakkında olmasından yola çıkarak, bu terimi web teknolojileri ve siber güvenlik bağlamındaki anlamıyla açıklıyorum:

## SSI (Server-Side Includes) Nedir?

**SSI (Sunucu Taraflı Kapsamalar)**, web sunucularında (özellikle Apache veya IIS) kullanılan basit bir sunucu taraflı betik (scripting) dilidir.

Temel amacı, bir HTML sayfasının içine dinamik içerikler yerleştirmek veya başka bir dosyanın içeriğini mevcut sayfaya dahil etmektir. Genellikle `.shtml`, `.shtm` veya `.stm` uzantılı dosyalarda kullanılır.

### Ne İşe Yarar?

Eskiden modern web framework'leri (PHP, Node.js, ASP.NET vb.) yaygın değilken, web geliştiricileri her HTML sayfasına menü (header) veya alt bilgi (footer) kodlarını tek tek kopyalamak zorundaydı. SSI, bu işlemi otomatikleştirir.

Örneğin, HTML içine şu kod yazılır:

```html
<!--#include virtual="/header.html" -->

```

Sayfa ziyaretçiye gönderilmeden önce sunucu bu etiketi görür, `header.html` dosyasının içeriğini alır ve tam o noktaya yerleştirip sayfayı öyle yükler.

---

## Siber Güvenlik Boyutu: SSI Injection (SSI Enjeksiyonu)

Bir web uygulaması, kullanıcıdan aldığı girdileri (örneğin bir iletişim formu veya arama kutusu) yeterince filtrelemeden (sanitize etmeden) ekrana yansıtıyor ve arka planda SSI çalıştırıyorsa, saldırganlar zararlı SSI komutları enjekte edebilirler.

### Nasıl Sömürülür?

Saldırgan form alanına normal bir metin yerine sunucuda işletim sistemi komutu çalıştıracak bir SSI direktifi yazar:

```html
<!--#exec cmd="whoami" -->

```

Eğer sistem zafiyet barındırıyorsa, sunucu bu satırı bir yorum satırı olarak değil, bir "çalıştırılabilir komut" olarak algılar ve terminalde `whoami` komutunu çalıştırıp sonucunu ekrana basar.

> **Bağlantı Notu:** Tıpkı bir önceki sorunuzdaki **Commix** aracının hedeflediği *Command Injection* zafiyetleri gibi, SSI Injection da nihayetinde sunucuda yetkisiz komut çalıştırmaya (RCE - Remote Code Execution) olanak tanıyan oldukça tehlikeli bir açıktır.

## XXE (XML External Entity) açığı

**XXE (XML External Entity) açığı**, XML işleyen uygulamalarda ortaya çıkan bir güvenlik zafiyetidir. Tam adıyla **XML External Entity Injection**, saldırganın XML verisi içine özel tanımlar ekleyerek sistemdeki dosyalara erişmesine veya dış sistemlerle etkileşime girmesine olanak tanır.


### 🧩 Temel mantık

XML’de “entity” (varlık) denilen yapılar vardır. Normalde bunlar metin tekrarını kolaylaştırmak için kullanılır. Ancak bazı XML parser’lar, **harici (external) entity** tanımlarını da işler.

Saldırgan bu özelliği kötüye kullanarak XML içine şuna benzer bir şey ekleyebilir:

```xml
<!DOCTYPE foo [
  <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
```

Ve sonra:

```xml
<user>&xxe;</user>
```

Eğer uygulama savunmasızsa, bu entity çözülür ve sunucudaki dosya içeriği açığa çıkar.

---

### ⚠️ Neler yapılabilir?

XXE açığı oldukça tehlikelidir çünkü:

* 📂 **Sunucu dosyaları okunabilir** (örn: `/etc/passwd`)
* 🌐 **İç ağ servislerine istek yapılabilir** (SSRF benzeri etki)
* 🔐 **Hassas bilgiler sızdırılabilir**
* 💥 **Denial of Service (DoS)** saldırıları yapılabilir (örn: “Billion Laughs” saldırısı)

---

### 🔍 Nerelerde görülür?

XXE genelde şu durumlarda ortaya çıkar:

* XML kullanan API’lerde
* SOAP web servislerinde
* Dosya yükleme (upload) özelliklerinde
* Eski veya yanlış yapılandırılmış XML parser’larda

---

### 🛡️ Nasıl önlenir?

Bu açığı engellemek için:

* ❌ **External entity kullanımını kapat**
* 🔒 Güvenli XML parser ayarları kullan
* 🔄 Mümkünse XML yerine JSON gibi daha güvenli formatlara geç
* 📦 Güncel kütüphaneler kullan
* 🔍 Kullanıcı girdisini doğrula

Örneğin birçok dilde şu tarz ayar yapılır:

* Java: `disallow-doctype-decl`
* Python: `defusedxml` kütüphanesi

---

### 📌 Kısaca

XXE, XML parser’ın dış kaynaklara erişebilmesini kötüye kullanan bir enjeksiyon açığıdır. Yanlış yapılandırılmış sistemlerde veri sızıntısı ve daha ciddi saldırılara yol açabilir.

---

İstersen gerçek bir XXE exploit örneğini adım adım da gösterebilirim veya bunu nasıl test edebileceğini anlatabilirim.


