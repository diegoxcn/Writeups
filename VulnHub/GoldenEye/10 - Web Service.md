# Web Service
- This is a very cool web page
- do I really want to check /sev-home/? 
![](Pasted%20image%2020210428205307.png)
- I prefer to check source page first
![](Pasted%20image%2020210428205500.png)
- Seems like all the cool stuff is from the terminal.js?
![](Pasted%20image%2020210428205612.png)
- possible username `boris` and his password, along with another username `natalya`
- encoded here, looks like url encode?:
`&#73;&#110;&#118;&#105;&#110;&#99;&#105;&#98;&#108;&#101;&#72;&#97;&#99;&#107;&#51;&#114;`
- Nope, it's HTML code not url, but anyway, here's the password: `InvincibleHack3r`

## /sev-home/
- this folder give us a login page directly, so this should be boris? Yes
- After we login, there's another page
![](Pasted%20image%2020210428210228.png)
- So we have to email to GNO to get further training, and there's a non-default pop3 port in very high range port
- Check the source code fo /sev-home/, there's very nasty tricky information hidden at the very bottom of the page, if you don't scroll page to the bottom, it's in very high chance that you will miss the message:
`Qualified GoldenEye Network Operator Supervisors: 
Natalya
Boris`
![](Pasted%20image%2020210428210537.png)

- First we need to find the port, but don't know why nmap scan this box with ports range super super slow, only scan one or two ports is normal speed, so I have to scan it one by one.......until I actually got the open port.
```bash
└╼cn$ for ((i=65535; i>30000; i--)); do nmap -Pn -p $i --max-retries 0 192.168.20.7; done | grep open
...[snip]...
55007/tcp open  unknown
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
55006/tcp open  unknown
...[snip]...
```
- Now we have the ports, 55006 and 55007. Confirm again, then detail scan.
```bash
└╼cn$ nmap -Pn -v -p55000-55010 --max-retries 0 192.168.20.7
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-03 10:06 CST
Initiating Parallel DNS resolution of 1 host. at 10:06
Completed Parallel DNS resolution of 1 host. at 10:06, 1.17s elapsed
Initiating Connect Scan at 10:06
Scanning 192.168.20.7 [11 ports]
Discovered open port 55006/tcp on 192.168.20.7
Discovered open port 55007/tcp on 192.168.20.7
Completed Connect Scan at 10:06, 11.01s elapsed (11 total ports)
Nmap scan report for 192.168.20.7
Host is up (1.7s latency).

PORT      STATE  SERVICE
55000/tcp closed unknown
55001/tcp closed unknown
55002/tcp closed unknown
55003/tcp closed unknown
55004/tcp closed unknown
55005/tcp closed unknown
55006/tcp open   unknown
55007/tcp open   unknown
55008/tcp closed unknown
55009/tcp closed unknown
55010/tcp closed unknown

Read data files from: /usr/local/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 12.27 seconds

└╼cn$ nmap -Pn -p55006,55007 -A 192.168.20.7                                             
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-05-03 10:10 CST
Nmap scan report for 192.168.20.7
Host is up (0.0014s latency).

PORT      STATE SERVICE  VERSION
55006/tcp open  ssl/pop3 Dovecot pop3d
|_pop3-capabilities: PIPELINING TOP UIDL CAPA SASL(PLAIN) USER RESP-CODES AUTH-RESP-CODE
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
|_ssl-date: TLS randomness does not represent time
55007/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: TOP UIDL SASL(PLAIN) PIPELINING STLS CAPA USER RESP-CODES AUTH-RESP-CODE
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
|_ssl-date: TLS randomness does not represent time

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.24 seconds
```


## SplashAdmin
- From [05 - Ports Scan & Enumeration](05%20-%20Ports%20Scan%20&%20Enumeration.md) we know from nikto there's a page called `/splashAdmin.php`, but we didn't explore it yet.
![](Pasted%20image%2020210503103751.png)
```bash
└╼cn$ curl -s http://192.168.20.7/splashAdmin.php | html2text
***** Cobalt Qube 3 has been decommissioned *****

**** We can use this page to put up team photos, discussion, etc. Natalya is
not allowed to post here though --Boris ****


===============================================================================
Here's me with my new sniper rifle.


[sniper.png]
===============================================================================
Boris why are you wearing shorts in that photo? You do realize you're stationed
above the Arctic circle, correct?

BTW your favorite pen broke, but I replaced it with a new special one.

Natalya "best coder" S.
===============================================================================
"License to Kill - Complex Grenade Launchers - No Oddjob" - Unknown"
===============================================================================
Greetings ya'll! GoldenEye Admin here.

For programming I highly prefer the Alternative to GCC, which FreeBSD uses.
It's more verbose when compiling, throwing warnings and such - this can easily
be turned off with a proper flag. I've replaced GCC with this throughout the
GolenEye systems.

Boris, no arguing about this, GCC has been removed and that's final!

Also why have you been chatting with Xenia in private Boris? She's a new
contractor that you've never met before? Are you sure you've never worked
together...?

-Admin
===============================================================================
Janus was here
===============================================================================
```
- so we might have more possbile username: admin, Xenia and Janus.
- gcc is not on this system, replaced by alternative.
- 
