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

