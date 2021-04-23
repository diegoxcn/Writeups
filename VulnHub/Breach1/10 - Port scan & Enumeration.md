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


