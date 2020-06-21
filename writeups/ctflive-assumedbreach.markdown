---
layout: post
title:  "CTFLive - Assumed Breach"
postid: blog
categories: CTFLive
---

<br />

# Assumed Breach
2/7 flags achieved

My Kali IP: 192.106.163.2
Target IP: 
192.106.163.3
192.106.163.4
192.106.163.5
192.106.163.6

## Info from hints
User:sansa 
Password: welcome@123
Flags: 7
Machines: 4

<br />

# Nmap Scan
nmap -sC -sV 192.106.163.3
22 ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.8

nmap -sC -sV 192.106.163.4
22 ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.8

nmap -sC -sV 192.106.163.5
21 ftp ProFTPD 1.3.5a
22 ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.8

nmap -sC -sV 192.106.163.6
21 ftp ProFTPD 1.3.5a
22 ssh OpenSSH 7.2p2 Ubuntu 4ubuntu2.8

<br />

# 192.106.163.3
## SSH
ssh sansa@192.106.163.3
[Using Sansa's U/Pw]
[Flag and id_rsa found on Sansa's Directoy]

[Looked over .bash_history. New user: kate]
shows ```ssh -i id_rsa kate@192.106.163.3```

[From Kali, scp the id_rsa to my directory]
scp sansa@192.106.163.3:/home/sansa/id_rsa /root/

[Used kate's id_rsa to try to enter other machines]

<br />

# 192.106.163.4
[ssh using id_rsa with kate]
ssh -i id_rsa kate@192.106.163.4

[Flag found under kate's directory]

<br />

**Not able to pivot anywhere else.**