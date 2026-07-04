# PortSwigger – Flawed Enforcement of Business Rules

## Konu

Bu lab, business rule kontrollerinin hatalı uygulanması nedeniyle kupon kullanım mantığının bypass edilebildiğini göstermektedir.

## Zafiyet

Uygulama aynı kuponun art arda kullanılmasını engellese de, iki farklı kupon dönüşümlü olarak kullanıldığında bu kontrol sıfırlanıyor. Böylece kuponlar sırayla (`Coupon1 → Coupon2 → Coupon1 → Coupon2 ...`) tekrar tekrar uygulanabiliyor ve her seferinde indirim kazanılıyor.

## Çözüm

* Ürün sepete eklendi.
* İki geçerli indirim kuponu belirlendi.
* Burp Repeater/Intruder kullanılarak kuponlar dönüşümlü gönderildi.
* Her başarılı istekten sonra toplam fiyat kademeli olarak düştü.
* Fiyat yeterince azaldığında satın alma işlemi tamamlandı.

## Alınan Ders

* Business rule'lar işlem bazında değil, **kupon bazında ve sipariş bazında** doğrulanmalıdır.
* Farklı kuponların birbirinin kullanım durumunu sıfırlamasına izin verilmemelidir.
* İndirim mekanizmaları sunucu tarafında bütünsel olarak doğrulanmalı ve toplam indirim limitleri uygulanmalıdır.

**Anahtar Kelimeler:** Business Logic Vulnerability, Coupon Chaining, Coupon Alternation, Business Rule Bypass.
