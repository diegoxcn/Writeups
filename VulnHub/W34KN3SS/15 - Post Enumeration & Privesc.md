# Post Enumeration & Privesc
- We got in by [10 - Web Service](10%20-%20Web%20Service.md)
## Post Enumeration
### n30
```bash
n30@W34KN3SS:~$ ls -al
total 44
drwxr-xr-x 5 n30  n30  4096 Aug 14  2018 .
drwxr-xr-x 3 root root 4096 May  5  2018 ..
-rw------- 1 n30  n30    25 Aug 14  2018 .bash_history
-rw-r--r-- 1 n30  n30   220 May  5  2018 .bash_logout
-rw-r--r-- 1 n30  n30  3771 May  5  2018 .bashrc
drwx------ 2 n30  n30  4096 May  5  2018 .cache
drwxrwxr-x 3 n30  n30  4096 May  5  2018 .local
-rw-r--r-- 1 n30  n30   818 May  7  2018 .profile
drwxrwxr-x 2 n30  n30  4096 May  5  2018 .ssh
-rw-r--r-- 1 n30  n30     0 May  5  2018 .sudo_as_admin_successful
-rwxrwxr-x 1 n30  n30  1138 May  8  2018 code
-rw-rw-r-- 1 n30  n30    33 May  8  2018 user.txt
n30@W34KN3SS:~$ grep bash /etc/passwd
#root:x:0:0:root:/root:/bin/bash
root::0:0:root:/root:/bin/bash
n30:x:1000:1000:n30,,,:/home/n30:/bin/bash
n30@W34KN3SS:~$ cat user.txt
25e3cd678875b601425c9356c8039f68
n30@W34KN3SS:~$ cat code
�
^��Zc@sXddlZddlZddlZddlZdjej��GHdGHdGHdZeed��7Zeed��7Zee��7Zeed	��7Zeed
��7Zeed
       ��7Zeed
��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zeed��7Zej	eej��j
�Z
  dje
     �GHdGHdS(i����Ns[+]System Started at : {0}sG[+]This binary should generate unique hash for the hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is : {0}s[+]Done(
      tostsocketttimethashlibtformattctimetinftchrtordtsha256t	hexdigestthashf(((scode.py<module>s>



                                                                                          n30@W34KN3SS:~$ file code
code: python 2.7 byte-compiled
```


### linpeas.sh
1. n30 is sudo user.
```bash
User & Groups: uid=1000(n30) gid=1000(n30) groups=1000(n30),4(adm),24(cdrom),27(`sudo`),30(dip),46(plugdev),111(lpadmin),112(sambashare)
Sudo version 1.8.21p2
```
- we do find an exploit of sudo 1.8.21p2, but it required cc and socat which are not exist on target machine:
	- https://exploit-db.com/exploits/48052
	- tried compile on attacker box and download everything to victim box and run, but not work.
2. program ./code
- There's nothing more from linpeas.sh, so my guess is this code is something we should study with
```bash
n30@W34KN3SS:~$ ls -l
total 8
-rwxrwxr-x 1 n30 n30 1138 May  8  2018 code
-rw-rw-r-- 1 n30 n30   33 May  8  2018 user.txt

n30@W34KN3SS:~$ file code
code: python 2.7 byte-compiled
n30@W34KN3SS:~$ ./code
./code: line 1: $'\003\363\r': command not found
./code: line 2: $'^\307\361Zc\004@sX\002dd\001lZdd\001l\001Z\001dd\001l\002Z\002dd\001l\003Z\003d\002j\004e\002j\005\203\203\001GHd\003GHd\004GHd\005Z\006e\006e\ae\bd\006\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\006e\ae\bd\b\203\001\203\0017Z\006e\006e\ae\bd': command not found
./code: line 3: $'\203\001\203\0017Z\006e\006e\ae\bd\v\203\001\203\0017Z\006e\006e\ae\bd\f\203\001\203\0017Z\006e\006e\ae\bd\r\203\001\203\0017Z\006e\006e\ae\bd\016\203\001\203\0017Z\006e\006e\ae\bd\017\203\001\203\0017Z\006e\006e\ae\bd\020\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\020\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\021\203\001\203\0017Z\006e\006e\ae\bd\022\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\006e\ae\bd\a\203\001\203\0017Z\006e\003j': command not found
./code: line 4: syntax error near unexpected token `$'\025i\377\377\377\377Ns\032[+]System''
./code: line 4: `�Z
                   dje
                      �GHdGHdS(i����Ns[+]System Started at : {0}sG[+]This binary should generate unique hash for the hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is : {0}s[+]Done(
                       tostsocketttimethashlibtformattctimetinftchrtordtsha256t	hexdigestthashf(((scode.py<module>s>



               '
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 18:45:21 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04
[+]Done
```

- Since the victim box has not strings or ltrace, I copied it to my attacker box
```bash
└╼cn$ strings code
[+]System Started at : {0}sG
[+]This binary should generate unique hash for the hardcoded login infos
[+]Generating the hash ..t
[+]Your new hash is : {0}s
[+]Done(

sockett
timet
hashlibt
formatt
ctimet
inft
chrt
ordt
sha256t
hexdigestt
hashf(
code.pyt
<module>
```

- what hardcoded login info? The hash is 64 bits, is it sha256 (should be 256 bits, no?)?
```bash
n30@W34KN3SS:~$ find / -name code.pyt 2>/dev/null
n30@W34KN3SS:~$ echo 'b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04' | wc -c
65
```
![](Pasted%20image%2020210428160010.png)

- Seems like sha256 can be 64 bits
```bash
└╼cn$ hashid b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04
Analyzing 'b7059f59848845bc3eb1eb7f579891ff0a9fa9411633694ab87addda4fe27b04'
[+] Snefru-256
[+] SHA-256
[+] RIPEMD-256
[+] Haval-256
[+] GOST R 34.11-94
[+] GOST CryptoPro S-Box
[+] SHA3-256
[+] Skein-256
[+] Skein-512(256)
```

- Even it says that, it make hash by hardcoded login info, it still generate different hashes
- So, which part is hardcoded? n30 is the only login user, does the output suppose to be very same?
```bash
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:56 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : 791bf2fae7048264ee9e57eda2942527a185535eea843378431f7bc9e00684de
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:58 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : bbe70c5781b90096e91f65c10a396598d2bedcb82ae281b004c401cfe915183f
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:00:59 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : 5ff9ec2887d589ba10e561db2833f022c950b55e60522da08d0f70b7506e3682
[+]Done
n30@W34KN3SS:~$ python code
[+]System Started at : Wed Apr 28 19:01:01 2021
[+]This binary should generate unique hash for the hardcoded login info
[+]Generating the hash ..
[+]Your new hash is : f474ca12d1378a6b393ae26b1af0cbe4e110b317e54888267c436df054f412f7
[+]Done
```

- OK, recall that when strings code, we saw time and ctime function, so maybe the system time is salt in this hash.
- I'm look at the cat code or ./code output, and found there's some interesting strings:
	-	`hardcoded login infos[+]Generating the hash ..ttnt3t0t:tdtMtAtStDtNtBt!t#s[+]Your new hash is `
	-	does it looks like there's n30 with other stuff split by 't'?
	-	remove all the ts, we got this:
		-	n30:dMASDNB!#
	- then I searched dMASDNB from google, and what I saw are all writeups of this vulnhub box, this is part of n30 password!!!!

- So this is hardcoded login info. it's just I may not have the whole password.
```bash
30@W34KN3SS:~$ sudo -l
[sudo] password for n30: `dMASDNB!#`
Sorry, try again.
[sudo] password for n30:
sudo: 1 incorrect password attempt
```
- Of course I have the whole password from google already, just wonder how I can get it.
- Decompyle the code, but my system only have pip for python3.9, but uncompyle6 need 2.6 to 3.8
- Install pip for python2.7, and only extension pyc is working for uncompyle6.....
```bash
└╼cn$ sudo python2.7 -m ensurepip --default-pip                                                  1 ⨯
Password:
The directory '/Users/cn/Library/Caches/pip/http' or its parent directory is not owned by the current user and the cache has been disabled. Please check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
The directory '/Users/cn/Library/Caches/pip' or its parent directory is not owned by the current user and caching wheels has been disabled. check the permissions and owner of that directory. If executing pip with sudo, you may want sudo's -H flag.
Looking in links: /tmp/tmpzWPMga
Requirement already satisfied: setuptools in /System/Library/Frameworks/Python.framework/Versions/2.7/Extras/lib/python (41.0.1)
Collecting pip
Installing collected packages: pip
Successfully installed pip-18.1

└╼cn$ pip -V
pip 18.1 from /Library/Python/2.7/site-packages/pip (python 2.7)

╼cn$ pip3 -V
pip 21.1 from /usr/local/lib/python3.9/site-packages/pip (python 3.9)

└╼cn$ pip install uncompyle6

└╼cn$ uncompyle6 code
# file code
# path code must point to a Python source that can be compiled, or Python bytecode (.pyc, .pyo)

└╼cn$ mv code code.pyc

└╼cn$ uncompyle6 code.pyc
# uncompyle6 version 3.7.4
# Python bytecode 2.7 (62211)
# Decompiled from: Python 2.7.16 (default, Feb 28 2021, 12:34:25)
# [GCC Apple LLVM 12.0.5 (clang-1205.0.19.59.6) [+internal-os, ptrauth-isa=deploy
# Embedded file name: code.py
# Compiled at: 2018-05-08 23:50:54
import os, socket, time, hashlib
print ('[+]System Started at : {0}').format(time.ctime())
print '[+]This binary should generate unique hash for the hardcoded login info'
print '[+]Generating the hash ..'
inf = ''
inf += chr(ord('n'))
inf += chr(ord('3'))
inf += chr(ord('0'))
inf += chr(ord(':'))
inf += chr(ord('d'))
inf += chr(ord('M'))
inf += chr(ord('A'))
inf += chr(ord('S'))
inf += chr(ord('D'))
inf += chr(ord('N'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('B'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('!'))
inf += chr(ord('#'))
inf += chr(ord('3'))
inf += chr(ord('3'))
hashf = hashlib.sha256(inf + time.ctime()).hexdigest()
print ('[+]Your new hash is : {0}').format(hashf)
print '[+]Done'
# okay decompiling code.pyc
```

- Now we have it, it's `n30:dMASDNB!!#B!#!#33`
```bash
n30@W34KN3SS:~$ sudo -l
[sudo] password for n30:
Matching Defaults entries for n30 on W34KN3SS:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User n30 may run the following commands on W34KN3SS:
    (ALL : ALL) ALL

n30@W34KN3SS:~$ sudo su -
root@W34KN3SS:~# ls -al ~
total 44
drwx------  4 root root 4096 Aug 14  2018 .
drwxr-xr-x 22 root root 4096 May  5  2018 ..
-rw-------  1 root root    6 Aug 14  2018 .bash_history
-rw-r--r--  1 root root 3106 Apr  9  2018 .bashrc
drwx------  2 root root 4096 Aug 14  2018 .cache
drwxr-xr-x  3 root root 4096 May  5  2018 .local
-rw-------  1 root root  114 May  5  2018 .mysql_history
-rw-r--r--  1 root root  148 Aug 17  2015 .profile
-rw-------  1 root root 1024 May  5  2018 .rnd
-rw-r--r--  1 root root   33 May  8  2018 root.txt
-rw-r--r--  1 root root   66 Aug 14  2018 .selected_editor
root@W34KN3SS:~# cat /root/root.txt
a1d2fab76ec6af9b651d4053171e042e
````




