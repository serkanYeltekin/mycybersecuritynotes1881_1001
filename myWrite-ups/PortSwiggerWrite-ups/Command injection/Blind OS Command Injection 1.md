# Blind OS command injection with time delays

bu blind injection olduğu için komutun çalıştığının çıktısını bize vermiyor bu yüzden komut enjeksiyonunun mümkün olduğunu göstermek için time delay kulanacağız ki bir gecikme olsun ve komutun çalıştığı kanıtlansın.

İlk olarak sumbit feedback kısmından bilgileri doldurup burpsuite ile isteği yakaladım. Şuan enjeksiyonun name,email,subject ve message kısımlarının hangisinde olduğunu bilmiyorum. O yüzden hepsini tek tek denedim.

Bu laboratuvar için verilen çözümün mantığı şu şekildedir:

```text
email=x||ping+-c+10+127.0.0.1||
```

Burada:

* `email=` → Enjeksiyon yapılan parametre.
* `x` → Mevcut komutun beklediği normal girdi.
* `||` → Shell'de "önceki komut başarısız olursa sonraki komutu çalıştır" operatörü. Bu operatör, laboratuvarda komut satırının oluşturulma şekline göre enjeksiyonu mümkün kılıyor.
* `ping -c 10 127.0.0.1` → Yaklaşık 10 saniyelik gecikme oluşturuyor (`-c 10`, 10 ICMP paketi gönderir).
* Son sondaki `||` → Laboratuvarın oluşturduğu komut satırının geri kalan kısmıyla sözdizimi uyumunu korumaya yardımcı oluyor.

`+` işaretleri ise `application/x-www-form-urlencoded` formatında **boşluk** anlamına gelir. Yani sunucu bunu şu şekilde görür:

```bash
x||ping -c 10 127.0.0.1||
```

Bunun senin önceki denemenden farkı şudur:

* Sen `&sleep%2010...` yazarak **yeni bir HTTP parametresi** ekliyordun.
* Çözümde ise komut, **`email` parametresinin değerinin içine** yerleştiriliyor. OS command injection'ın çalışabilmesi için de gereken tam olarak budur.

Bu laboratuvarın öğretmek istediği temel fikir, HTTP parametreleri ile shell komutlarının birbirinden farklı ayrıştırıldığıdır: `&` HTTP seviyesinde parametre ayırırken, `||`, `;`, `&&` gibi karakterler ancak bir parametre değeri güvenli olmayan şekilde shell'e aktarılırsa anlam kazanır.
