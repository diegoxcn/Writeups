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

