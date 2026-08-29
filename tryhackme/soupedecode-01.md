# Soupedecode 01

<figure><img src="../.gitbook/assets/image (1045).png" alt=""><figcaption></figcaption></figure>



## RECON

### Port scan

```bash
(base) ┌──(kali㉿kali)-[~/Desktop/Soupedecode_01]
└─$ sudo nmap -sCV 10.49.141.38
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-08-24 23:00 -0400
Nmap scan report for 10.49.141.38
Host is up (0.13s latency).
Not shown: 988 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
53/tcp   open  domain        Simple DNS Plus
88/tcp   open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-08-25 03:00:30Z)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: SOUPEDECODE.LOCAL, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds?
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap          Microsoft Windows Active Directory LDAP (Domain: SOUPEDECODE.LOCAL, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=DC01.SOUPEDECODE.LOCAL
| Not valid before: 2026-08-24T02:57:10
|_Not valid after:  2027-02-23T02:57:10
|_ssl-date: 2026-08-25T03:01:21+00:00; -1s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: SOUPEDECODE
|   NetBIOS_Domain_Name: SOUPEDECODE
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: SOUPEDECODE.LOCAL
|   DNS_Computer_Name: DC01.SOUPEDECODE.LOCAL
|   Product_Version: 10.0.20348
|_  System_Time: 2026-08-25T03:00:41+00:00
Service Info: Host: DC01; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2026-08-25T03:00:42
|_  start_date: N/A
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required

```



thêm vào file `/etc/hosts`

```bash
10.49.141.38 SOUPEDECODE.LOCAL DC01.SOUPEDECODE.LOCAL
```

```bash
(base) ┌──(kali㉿kali)-[~/Desktop/Soupedecode_01]
└─$ getent hosts DC01.SOUPEDECODE.LOCAL
```



```bash
(base) ┌──(kali㉿kali)-[~/Desktop]
└─$ smbclient -N -L //10.49.141.38

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
        Users           Disk      
        
(base) ┌──(kali㉿kali)-[~/Desktop]
└─$ smbclient -N  //10.49.141.38/backup 
Try "help" to get a list of possible commands.
smb: \> ls
NT_STATUS_ACCESS_DENIED listing \*

```



```bash
(base) ┌──(kali㉿kali)-[~/Desktop/Soupedecode_01]
└─$ kerbrute userenum \
  -d SOUPEDECODE.LOCAL \
  --dc 10.49.141.38 \
  /usr/share/seclists/Usernames/xato-net-10-million-usernames.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 08/24/26 - Ronnie Flathers @ropnop

2026/08/24 23:39:26 >  Using KDC(s):
2026/08/24 23:39:26 >   10.49.141.38:88

2026/08/24 23:39:26 >  [+] VALID USERNAME:       admin@SOUPEDECODE.LOCAL
2026/08/24 23:39:26 >  [+] VALID USERNAME:       charlie@SOUPEDECODE.LOCAL
2026/08/24 23:39:37 >  [+] VALID USERNAME:       guest@SOUPEDECODE.LOCAL
2026/08/24 23:39:38 >  [+] VALID USERNAME:       Charlie@SOUPEDECODE.LOCAL
2026/08/24 23:39:54 >  [+] VALID USERNAME:       administrator@SOUPEDECODE.LOCAL
2026/08/24 23:39:56 >  [+] VALID USERNAME:       Admin@SOUPEDECODE.LOCAL
2026/08/24 23:42:41 >  [+] VALID USERNAME:       Guest@SOUPEDECODE.LOCAL
2026/08/24 23:42:42 >  [+] VALID USERNAME:       Administrator@SOUPEDECODE.LOCAL
2026/08/24 23:43:04 >  [+] VALID USERNAME:       CHARLIE@SOUPEDECODE.LOCAL
```



```
admin
charlie
administrator
```



