---
hidden: true
---

# 2john check list

## 3. FILE / HASH EXTRACTION (\*2john tools)

👉 Pattern:

```
File có password → extract hash → crack
```

***

### gpg2john

```
gpg2john private.asc > hash.txt
```

***

### zip2john

```
zip2john file.zip > hash.txt
```

***

### rar2john

```
rar2john file.rar > hash.txt
```

***

### ssh2john

```
ssh2john id_rsa > hash.txt
```

***

### pdf2john

```
pdf2john file.pdf > hash.txt
```

***

### office2john

```
office2john file.docx > hash.txt
```

***

### keepass2john

```
keepass2john db.kdbx > hash.txt
```

***

## 4. PASSWORD CRACKING

***

### John the Ripper

```
john hash.txt --wordlist=rockyou.txtjohn --show hash.txt
```

***

### Hashcat

```
hashcat -m 1800 hash.txt rockyou.txt
```

***

### Common modes

| Mode | Hash         |
| ---- | ------------ |
| 0    | MD5          |
| 1000 | NTLM         |
| 1800 | SHA512-crypt |
