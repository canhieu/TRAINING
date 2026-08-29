---
hidden: true
---

# CozyHosting





<figure><img src="../../.gitbook/assets/image (100).png" alt=""><figcaption></figcaption></figure>

```bash
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  [⟳] PHASE 1: PORT SCANNING
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  [⟳] Engine: RustScan (timeout: 150s)...
  Command: timeout 150 rustscan -a 10.129.229.88 --ulimit 5000 -b 1500 -- -Pn
  [✔] RustScan found: 22,80
```





```bash
(base) ┌──(kali㉿kali)-[~]
└─$ sudo nmap -sC -sV -p22,80 10.129.229.88
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-27 05:23 -0400
Nmap scan report for 10.129.229.88
Host is up (0.27s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 43:56:bc:a7:f2:ec:46:dd:c1:0f:83:30:4c:2c:aa:a8 (ECDSA)
|_  256 6f:7a:6c:3f:a6:8d:e2:75:95:d4:7b:71:ac:4f:7e:42 (ED25519)
80/tcp open  http    nginx 1.18.0 (Ubuntu)
|_http-title: Did not follow redirect to http://cozyhosting.htb
|_http-server-header: nginx/1.18.0 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```



<figure><img src="../../.gitbook/assets/image (101).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (102).png" alt=""><figcaption></figcaption></figure>

{% embed url="http://cozyhosting.htb/actuator/sessions" %}

```
{"3D99579F19732FEF5D07A96E70B7FA2D":"kanderson"}
```



<figure><img src="../../.gitbook/assets/image (104).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (103).png" alt=""><figcaption></figcaption></figure>

