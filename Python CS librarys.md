# Python CS library

## subprocess

-  subprocess modülü, Python içinde başka komutları veya programları çalıştırmayı sağlar.

## Scapy

Scapy, Python tabanlı çok güçlü ve esnek bir ağ paketi işleme (packet manipulation) kütüphanesidir.


**Temel Özellikleri ve Kullanım Alanları:**

- Paket Üretimi ve Manipülasyonu: İsteğe bağlı ağ paketleri oluşturabilir, değiştirebilir ve ağa gönderebilirsiniz.

- Ağ İzleme (Sniffing): Ağ trafiğini dinleyerek geçen paketleri yakalayabilir ve analiz edebilirsiniz.

- Sızma Testleri ve Güvenlik: Ağ taraması (scanning), iz sürme (tracerouting), saldırı simülasyonları ve ağ keşfi gibi siber güvenlik görevlerinde yaygın olarak kullanılır.

- Geniş Protokol Desteği: TCP, UDP, ICMP, ARP, DNS ve daha birçok protokolü destekler.

## optparse

optparse, Python’da komut satırı argümanlarını (CLI arguments) almak için kullanılan eski bir modüldür.
Günümüzde yerini büyük ölçüde argparse almıştır ama hâlâ eski projelerde görülebilir.

**Amaç :** Kullanıcıdan terminal üzerinden parametre almak
Yer: Python standard library (built-in)
Durum: Deprecated (önerilen değil)
### ⚙️ Temel Kullanım
```python
from optparse import OptionParser

parser = OptionParser()

parser.add_option("-f", "--file", dest="filename",
                  help="dosya ismini belirtir")

parser.add_option("-v", "--verbose",
                  action="store_true", dest="verbose",
                  help="detaylı çıktı verir")

(options, args) = parser.parse_args()

print(options.filename)
print(options.verbose)
```
#### ▶️ Çalıştırma Örneği
    python script.py -f test.txt -v
#### 🧠 Kısa Özet
- -f, --file → parametre alma
- dest → değişken adı
- action="store_true" → flag (True/False)
- parse_args() → argümanları işler
#### 🚨 Not
- optparse eski ve geliştirme almıyor
yeni projelerde: **argparse** kullan

## socket

Python’da **socket kütüphanesi**, ağ üzerinden iki bilgisayarın (veya süreçlerin) haberleşmesini sağlayan düşük seviyeli bir modüldür. TCP ve UDP gibi protokollerle doğrudan bağlantı kurmayı mümkün kılar.


Python içindeki `socket` modülü, internet veya yerel ağ üzerinden veri alışverişi yapmak için kullanılır. Sunucu (server) ve istemci (client) yapıları oluşturmanın temel aracıdır.

### Temel Özellikler

* TCP (bağlantılı) ve UDP (bağlantısız) iletişim desteği
* IP adresi ve port üzerinden bağlantı kurma
* Ham (raw) veri gönderip alma
* Sunucu–istemci mimarisi kurma

### Basit Kullanım Örneği

```python
import socket

# Socket oluşturma
s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# Bir sunucuya bağlanma
s.connect(("example.com", 80)) # buradaki 80 porttur.

# Veri gönderme
s.send(b"GET / HTTP/1.1\r\nHost: example.com\r\n\r\n")

# Veri alma
response = s.recv(4096)
print(response)
```

### Nerelerde Kullanılır?

* Web sunucuları
* Chat uygulamaları
* Oyunlar
* IoT cihaz haberleşmesi
* Ağ test araçları
