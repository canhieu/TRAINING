---
hidden: true
---

# Silentium

<figure><img src="../../.gitbook/assets/image (105).png" alt=""><figcaption></figcaption></figure>

## Recon

### Scan port

Target IP Address

![pulsing dot](https://app.hackthebox.com/images/dots/green-dot.svg)10.129.33.249

```bash
  PORT     STATE      SERVICE                       
  ──────── ────────── ──────────────────────────────
  22/tcp   open       ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 (Ubuntu Linux; protocol 2.0)
  80/tcp   open       http nginx 1.24.0 (Ubuntu)    
```

<figure><img src="../../.gitbook/assets/image (106).png" alt=""><figcaption></figcaption></figure>



```bash
--- Nmap SSH Scripts ---
Starting Nmap 7.98 ( https://nmap.org ) at 2026-04-25 09:51 -0400
Nmap scan report for 10.129.33.249
Host is up (0.30s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.6p1 Ubuntu 3ubuntu13.15 (Ubuntu Linux; protocol 2.0)
| ssh2-enum-algos: 
|   kex_algorithms: (12)
|       sntrup761x25519-sha512@openssh.com
|       curve25519-sha256
|       curve25519-sha256@libssh.org
|       ecdh-sha2-nistp256
|       ecdh-sha2-nistp384
|       ecdh-sha2-nistp521
|       diffie-hellman-group-exchange-sha256
|       diffie-hellman-group16-sha512
|       diffie-hellman-group18-sha512
|       diffie-hellman-group14-sha256
|       ext-info-s
|       kex-strict-s-v00@openssh.com
|   server_host_key_algorithms: (4)
|       rsa-sha2-512
|       rsa-sha2-256
|       ecdsa-sha2-nistp256
|       ssh-ed25519
|   encryption_algorithms: (6)
|       chacha20-poly1305@openssh.com
|       aes128-ctr
|       aes192-ctr
|       aes256-ctr
|       aes128-gcm@openssh.com
|       aes256-gcm@openssh.com
|   mac_algorithms: (10)
|       umac-64-etm@openssh.com
|       umac-128-etm@openssh.com
|       hmac-sha2-256-etm@openssh.com
|       hmac-sha2-512-etm@openssh.com
|       hmac-sha1-etm@openssh.com
|       umac-64@openssh.com
|       umac-128@openssh.com
|       hmac-sha2-256
|       hmac-sha2-512
|       hmac-sha1
|   compression_algorithms: (2)
|       none
|_      zlib@openssh.com
| ssh-hostkey: 
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
|_  256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

```



<figure><img src="../../.gitbook/assets/image (107).png" alt=""><figcaption></figcaption></figure>



```bash
ffuf -u http://silentium.htb/FUZZ -w big.txt -fs 8753
```

<figure><img src="../../.gitbook/assets/image (108).png" alt=""><figcaption></figcaption></figure>



```
ffuf -u http://silentium.htb/ \
     -H "Host: FUZZ.silentium.htb" \
     -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt \
     -fs 178
```

<figure><img src="../../.gitbook/assets/image (110).png" alt=""><figcaption></figcaption></figure>









<figure><img src="../../.gitbook/assets/image (109).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (111).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (112).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (114).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (113).png" alt=""><figcaption></figcaption></figure>







```bash
(base) ┌──(kali㉿kali)-[~/Documents/sielen/CVE-2025-58434-59528]
└─$ python3 flowise_chain.py -t http://silentium.htb -e ben@silentium.htb
```



<figure><img src="../../.gitbook/assets/image (115).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (116).png" alt=""><figcaption></figcaption></figure>



[http://staging.silentium.htb/apikey](http://staging.silentium.htb/apikey)

<figure><img src="../../.gitbook/assets/image (117).png" alt=""><figcaption></figcaption></figure>



vào dc rồi thì quyền root nhma ở trong container của cái đáy , sau đọc `/proc/self/environ`&#x20;

để lấy dc thông tin passwd sau ssh sang thằng ben&#x20;

