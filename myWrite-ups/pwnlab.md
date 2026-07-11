netdiscover kullanarak hedefin ip adresini buldum.
nmap kullanarak açık portlara baktım,
gobuster ile subdomainlerini arattım. -> /admin, /index.php ,/images gibi subdomainler buldum.
sqlmap ile sql injection aradım ancak herhangi bir sql enjeksiyonu bulamadım.
owasp zap ile de pek bir şey çıkmadı.
nikto ile  -h 10.0.2.23 yaptım, /config.php ve /login.php diye de dosyalar buldum ancak bunlara herhangi bir şekilde erişilemiyor şuan.
bunları bulduktan sonra aklıma file inclusion açığı olabileceği geldi. file inclusionun şöyle iyi bir yöntemi de varmış -> http://10.0.2.23/?page=php://filter/convert.base64-encode/resource=index bu kod bize açmak istediğimiz dosyanın bize geri dönüşünün base64 ile şifrelenmiş halini veriyor.
Bu yöntemi uyguladığımda çalıştı ve ben de bulduğum .php uzantılı dosyaların içeriğine erişebildim. ve dosyalarda databasenin root kullanıcısının isim ve şifresine eriştim.
bu bilgilerle de veritabanına erişim sağladım.
erişim sonucunda 3 tane isim password buldum ve bunları kullanarak login sayfasına giriş yaptım.
giriş yaptıktan sonra dosya upload etme yeri açıldı, benimde aklıma Shell çalıştırmak geldi. Kali içerisinde user/share/webshells/php kısmından hazır bir php web reverees shell denedim.
Dosya uzantısı .php olduğu için kabul etmedi bende uzantıyı .gif yaptım ve kodu açıp ilk satıra GIF98 yazdım ki gif dosyası gibi olsun. Sonra tekrar yüklemeyi denedim bu sefer yüklendi ancak revershell çalışmadı. Çünkü sistem içerisine yüklenen zararlı yazılımlardan korunmak için dosyayı direk yükleyip kaynak koduna gidince dosyayı çalıştırmıyor, biz de netcat ile cevap alamıyoruz. 
Ama artık sitenin içerisinde bir .php reverse shellimizin olduğunu biliyoruz ve bunu çalıştırmaya çalışacağız.
Bu dosyayı çalıştırmak için upload kısmına bastım ve burpsuite ile isteği yakaladım.
Yakaladığım istekdeki Cookie: PHPSESSID=85dr6ob9gc7q594vsbhm0geqb4 bkısmına lang=/../upload/aa395838895d58df4f2aea5c8be291b7.gif ekleyip forward ettim ve netcat ile bağlantıyı yakalayabildim.
Ancak www-data diye bir kullanıcıya bağlandım. benim amacım roota bağlanmak.
Önce su kodunu çalıştırmayı denedim ama bu hatayı aldım -> su: must be run from a terminal
Bunu aşmak için internette su: must be run from a terminal diye aratıp bulduğum 
$ echo "import pty; pty.spawn('/bin/bash')" > /tmp/asdf.py
$ python /tmp/asdf.py
kodlarını çalıştırdığımda bir kademe daha yukarı çıkarak artık su komutunu çalıştırabilir hale geldim. oradan kane kullanıcısına girdim ve kane kullanıcısının içerisinde msgmike adlı bir dosya buldum ancak herhangi bir şekild eeriişilemiyor. ./msgmike yaptığımda ise -> cat: /home/mike/msg.txt: No such file or directory diye bir hata aldım. Bu hata sistem bu yolu arayıp da bulamadığından dolayı meydana geliyor.