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

