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

	
	