# Port Spoof
## SSH
- Since the credential from [[15 - Web Service]] seems to be the only thing that we have currently, I go trying ssh.
```bash
ssh pgibbons@192.168.110.140
kex_exchange_identification: read: Connection reset by peer
Connection reset by 192.168.110.140 port 22
```

```bash
ssh -v pgibbons@192.168.110.140                                                                           255 ⨯
OpenSSH_8.4p1 Debian-5, OpenSSL 1.1.1k  25 Mar 2021
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: include /etc/ssh/ssh_config.d/*.conf matched no files
debug1: /etc/ssh/ssh_config line 21: Applying options for *
debug1: Connecting to 192.168.110.140 [192.168.110.140] port 22.
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
kex_exchange_identification: read: Connection reset by peer
Connection reset by 192.168.110.140 port 22
```

- But nc and telnet connection did show some interesting info.
```bash
nc -v 192.168.110.140 22
192.168.110.140: inverse host lookup failed: Unknown host
(UNKNOWN) [192.168.110.140] 22 (ssh) open
550 12345 0f8008c008fff8000000000000780000007f800087708000800ff00

telnet 192.168.110.140 22                                                                                 255 ⨯
Trying 192.168.110.140...
Connected to 192.168.110.140.
Escape character is '^]'.
550 12345 0f8008c008fff8000000000000780000007f800087708000800ff00
Connection closed by foreign host.
```

- Google the info get one software named portspoof
![[Pasted image 20210422124449.png]]
![[Pasted image 20210422124655.png]]
- This is exactly the one cause our problem.

