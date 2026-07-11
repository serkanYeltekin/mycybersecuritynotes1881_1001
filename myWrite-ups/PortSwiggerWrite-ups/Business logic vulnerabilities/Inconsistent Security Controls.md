# PortSwigger Lab: Inconsistent Security Controls

## Amaç

Uygulamadaki güvenlik kontrollerinin her yerde aynı şekilde uygulanmadığını (inconsistent security controls) tespit ederek yetkisiz bir işlemi gerçekleştirmek.

---

## Temel Kavram

**Inconsistent Security Controls**, uygulamanın bazı endpoint'lerde yetki kontrolü yaparken, bazı endpoint'lerde aynı kontrolü yapmaması durumudur.

Örneğin:

* `/admin` → Admin kontrolü var.
* `/admin/delete` → Admin kontrolü yok.

veya

* Kullanıcı arayüzünde işlem gizlenmiştir.
* Ancak backend isteği doğrudan gönderildiğinde işlem gerçekleşmektedir.

Yani **frontend güvenliği vardır, backend güvenliği eksiktir.**

---

## Lab'da Yapılanlar

### 1. Normal kullanıcı olarak giriş yap.

Örneğin:

* wiener
* peter

---

### 2. Burp Proxy'yi aç.

Tarayıcıyı Burp üzerinden kullan.

---

### 3. Gizli endpointleri bul.

Burp:

**Target → Site map**

veya

**Target → Engagement tools → Discover content**

Content Discovery çalıştırılır.

Keşfedilen dizinlerden biri:

```
/admin
```

---

### 4. Admin panelini incele.

```
GET /admin
```

Sunucu normal kullanıcıya erişim vermeyebilir.

Örneğin:

```
403 Forbidden
```

---

### 5. Admin fonksiyonlarını incele.

HTML içinde veya Burp Site Map'te şu tarz endpointler bulunabilir:

```
/admin/delete
/admin-roles
/admin/delete?username=carlos
```

---

### 6. Doğrudan isteği gönder.

Bazı endpointler yalnızca arayüzden gizlenmiştir.

Örneğin:

```
GET /admin/delete?username=carlos
```

veya

```
POST /admin/delete
```

Sunucu burada yetki kontrolü yapmıyorsa işlem başarılı olur.

---

## Burp Araçları

Bu labda kullanılanlar:

* Proxy
* Target
* Site Map
* Discover Content
* Repeater

---

## Dikkat Edilecek Noktalar

✔ Gizli dizinleri Discover Content ile tara.

✔ 403 dönen dizinlerin altındaki endpointleri kontrol et.

✔ UI'da görünmeyen endpointleri doğrudan iste.

✔ Backend'in gerçekten yetki kontrolü yapıp yapmadığını test et.

---

## Öğrenilen Güvenlik Açığı

Bu lab şu hatayı öğretir:

> "Bir endpoint korunuyor diye aynı dizindeki tüm endpointlerin de korunduğu varsayılmamalıdır."

Her endpoint için yetkilendirme kontrolü ayrı ayrı uygulanmalıdır.

---

## Kısa Özet

```
Normal User
      │
      ▼
Discover Content
      │
      ▼
/admin bulundu
      │
      ▼
Admin paneli → 403
      │
      ▼
Alt endpointleri keşfet
      │
      ▼
Doğrudan isteği gönder
      │
      ▼
Yetki kontrolü yoksa işlem başarılı
```

---

## Sınav / Bug Bounty Notu

Bu tür zafiyetlerde özellikle şu endpointleri test etmek faydalıdır:

* /admin
* /admin/delete
* /admin/users
* /admin/settings
* /manage
* /internal
* /api/admin
* /dashboard/admin

Ayrıca aşağıdaki HTTP yöntemlerini de deneyin:

* GET
* POST
* PUT
* PATCH
* DELETE

Bazı uygulamalar yalnızca belirli HTTP metodlarında yetkilendirme kontrolünü eksik uygular.
