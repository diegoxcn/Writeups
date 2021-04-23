# Web Service
## Images
![[Pasted image 20210422114134.png]]
- There's extra comment from bill.png, says coffeestains, so far no idea what does it mean.
```bash
┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.png | grep -n3 Comment
3889-,u V<P!
3890-3Mqs
3891-c{'t
3892:tEXtComment
3893-coffeestains
3894-IEND

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.jpg | grep -n3 Comment

┌──(htb㉿kali)-[~/VulnHub/Breach1]
└─$ strings *.gif | grep -n3 Comment
```

## git
- .gitignore file shows some projects maybe exist, we can use git-dumper to download and study it.
```bash
git-dumper http://192.168.110.140/.gitignore git-breach
[-] Testing http://192.168.110.140/.gitignore/.git/HEAD [404]
[-] http://192.168.110.140/.gitignore//.git/HEAD responded with status code 404
```
- OK, lesson learnt, we still need .git/HEAD file to download the repo, so now we can only access site by files....
```bash
curl -s http://192.168.110.140/.gitignore | html2text                                                       1 ⨯
/.idea/ 
/.project 
/.buildpath 
/.settings/
```
- Checked by web browser, they are all non-existing folders....

## Pages
- Since we've got port 80 response from [[10 - Port scan & Enumeration]], we enumerate more.
```bash
gobuster dir -u http://192.168.110.140 -x txt,html,htm,php,jsp,asp,aspx -w /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
===============================================================
Gobuster v3.1.0
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://192.168.110.140
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /usr/share/seclists/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.1.0
[+] Extensions:              txt,html,htm,php,jsp,asp,aspx
[+] Timeout:                 10s
===============================================================
2021/04/22 11:25:55 Starting gobuster in directory enumeration mode
===============================================================
/index.html           (Status: 200) [Size: 1098]
/images               (Status: 301) [Size: 318] [--> http://192.168.110.140/images/]
/breach.html          (Status: 200) [Size: 284]
/server-status        (Status: 403) [Size: 295]

===============================================================
2021/04/22 11:39:48 Finished
===============================================================
```

- Not too much from the breach.html, a image says should look into source code, and a button click would lead to index.html.
- Then we finally have something from index.html source page
![[Pasted image 20210422125708.png]]
```bash
echo 'Y0dkcFltSnZibk02WkdGdGJtbDBabVZsYkNSbmIyOWtkRzlpWldGbllXNW5KSFJo' | base64 -d | base64 -d
pgibbons:damnitfeel$goodtobeagang$ta
```
- Is that a credential pair? initech.html is not showing from the pages scanning.
![[Pasted image 20210422130020.png]]
- This page looks like company entry, and from source code, we can see there's mailbox and user.php.
![[Pasted image 20210422130258.png]]
- Hostname: [breach.local](mailto:webmaster@breach.local)
- Possible name:  Samir Nagheenanajar


