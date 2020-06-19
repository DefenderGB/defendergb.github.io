---
layout: post
title:  "Learning basics"
postid: blog
categories: CTfs VirCon SANS-HackFest Git
---

<br />

### Update
These past 2 months I was able to: 
* Participate on four CTFs (ctf.live, ctf.virseccon.com, BootUpCTF, and Jupiter Rockets)
* Bought the domain name defendergb.org for my blog and cleaned up my blog's code
* Learned the basics of using git and branches
* Practiced variant XSS within OWASP-BWA and in bug bounties
* Began reading through "The Web Application Hacker's Handbook"
* Started a Udemy Course - [Practical Ethical Hacking - The Complete Course](https://www.udemy.com/course/practical-ethical-hacking/) [Recommended - check CyberMentor Twitter for discounts]
* Went through a few of Kontra's [Application Security Training](https://application.security/free-application-security-training) [HIGHLY recommended]
* Tried HackTheBox and TryHackMe rooms/challenges
* Using CherryTree as my primary source of note taking.
* Started a Gitbook to publish my notes: [https://defender-gb.gitbook.io/](https://defender-gb.gitbook.io/)

<br />

### Backlog
While I was able to learn plenty of things these past months there is a VAST amount of things within my backlog that I wish to address first. The following are courses/tasks that are in my backlog that I have not been able to get to:
* LeetCode course - [Principle of Recursion](https://leetcode.com/explore/featured/card/recursion-i/250/principle-of-recursion/1439/)
* Udemy course - [Windows Privilege Escalation for Beginners](https://www.udemy.com/course/windows-privilege-escalation-for-beginners/)
* Udemy course - [Penetration Testing with KALI and More: All You Need to Know](https://www.udemy.com/course/kalipentest/) [Got free coupon via LinkedIn]
* eLearnSecurity (Free) - [PTS v4 course](https://members.elearnsecurity.com/courses/penetration_testing_student_v4)
* PluralSight - [Web Security and the OWASP Top 10: The Big Picture](https://app.pluralsight.com/library/courses/web-security-owasp-top10-big-picture/table-of-contents)
* LinkedIn Learning - Several courses that in my list
* Reading various security articles that I have collected from Twitter
* Reading various Medium articles
* Looking through recommended videos that I've gathered within YouTube
* Expanding my Gitbook
* Learn SSRF
* Create YouTube videos on HTB and XSS

Always make sure not to overwhelm yourself with tasks, but weekly track the work you have done, otherwise, it feels like you have done no progress. I have found it very helpful to set weekly goals and find a fair line between exaggerated and slacking goals. The security industry is vast and you will need to allocate time to study, but mental health is also important for proactive learning. 

<br />

### CTFs and Cyber challenges
You can find my notes of ctf.virseccon under my Write-ups/CTF repo, I'm not able to provide ctf.live notes as its still on-going, and I did not take extensive notes for SANS's BootUpCTF and Jupiter Rockets. All in all, I believe CTFs can be used to grind security skills, and allows you to focus on specific sectors of security (Example: Web Sec, Crypto, Binary, Forensics, Networking, etc), it's easy to go through all beginner level challenges on each technology but you don't get to learn much. The learning comes from hours spent on a difficult challenge, that's where you learn more about specific technology/vulnerability. Other technics that will help you develop further is taking notes of the research and techniques you used.

Good resources and example of note-taking for CTFs are [Apsdehal's CTF resource matrix](https://github.com/apsdehal/awesome-ctf), [HackTrick's CTF/Pentesting Notes](https://book.hacktricks.xyz/), and [David Rook's CTF Resources](https://gitlab.com/r00k/ctf-resources)


My CTFs scores:

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/virsecon-score.png" style="width:550px;"><br/>
<i>VirSecCon score - 686th</i>
</p>

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/BootUpCTF-score.png" style="width:550px;"><br/>
<i>BootUp CTF score - 223th</i>
</p>

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/JupiterRockets-score.png" style="width:550px;"><br/>
<i>Jupiter Rockets score - 87th</i>
</p>
<br />

### HTB and TryHackMe
At this time, I hit Script Kiddie (Level 1) in HTB and aim to get Hacker (Level 2) by end of the month. I truly enjoy Hack The Box and TryHackMe as both help you create methodologies to get user level and root level on machines. TryHackMe can be very beginner level with some rooms, but the main focus of TryHackMe rooms is to help you and guide you, while HTB gives you tips but does not give you the answers. I have done two easy boxes within HackTheBox and 3 practice rooms within TryHackMe so far, below I went into more detail on what I learned.

I would suggest watching [ippsec's HTB videos](https://www.youtube.com/channel/UCa6eh7gCkpPo5XXUDfygQQA) and [CyberMentor's HTB videos](https://www.youtube.com/channel/UC0ArlFuFYMpEewyRBzdLHiw/playlists) to learn see what HTB challenges compose of and what you can learn from it. I plan on creating YouTube videos on retired HTB boxes ones I have gained enough experience.

<b>HackTheBox</b>

[Magic](https://www.hackthebox.eu/home/machines/profile/241)

* Web App login bypass with sql injection
* Image upload a hidden php command shell image
* Use php to create a python reverse shell
* Enumerate to get Database credentials and dump database to get user password
* Use CVE-2018-9310 to create a python script hidden inside a fdisk and run sysinfo to get a reverse command shell and get root level.

[Blunder](https://www.hackthebox.eu/home/machines/profile/254)

* Bruteforcing admin login
* Utilized vulnerability within Bludit CMS 3.9.2 to get RCE
* Enumerate to get user hashed password and get user level
* Find vulnerability within sudo 1.8.27 to give local Priv escalate and get root level

<b>TryHackMe</b>

[CC: Steganography](https://tryhackme.com/room/ccstego)

* Teaches steganography in images and sound using following tools:
  * Steghide (jpg)
  * zsteg (png and bmp)
  * exiftool (image metadata)
  * stegoveritas (All image types and extract data)
  * Sonic Visualizer (spectrograms - audio files) 

[RP: Nmap](https://tryhackme.com/room/rpnmap)

* How to use nmap, looking at manual, etc

[Introduction to OWASP ZAP](https://tryhackme.com/room/learnowaspzap)

* How to use OWASP ZAP, proxy setup, etc

My badges:

<script src="https://www.hackthebox.eu/badge/298459"></script>

<script src="https://tryhackme.com/badge/51291"></script>
<br />

### SDLC
While I had to learn this for work, learning how to use git benefit me while coding for my blog. At first, I edited straight from GitHub which meant I didn't have a dev environment to test my code before publishing to production (Live changes to my blog). All this ties back to the Software Development Life-cycle (SDLC), while this is not directly tied to hacking, I think it's important to learn and understand. Proper applications and tools created by developers follow a SDLC model. SDLC model composes of planning, defining, designing, building, testing, deployment. While these are just terms, the general idea is after develop/testing/deploying there will be a cycle to continue development and testing.

For example, you are doing a penetration test and review a company's main infrastructure and web app, you find no vulnerabilities, although you find a external facing forum hosted in company's network that uses a deprecated apache version. You can start deriving that 1) the company has a branch/team that does is not properly maintaining this applications and 2) allows you to shift your scope attack small sectors within the company and use them to pivot.

<br />

### Blog
Before starting to code for my blog, I have never used git for development. My extended knowledge with git composed of pulling git repositories of tools or scripts.

This [video](https://www.youtube.com/watch?v=4SD6rWt9wUQ) had a good explanation of the bases of using git for development. Below are notes on my most used commands:
```bash
# Initial setup
# Configure Git with SSH to stop asking for email and password of git
# Great first time user guide: https://kbroman.org/github_tutorial/pages/first_time.html
# Github guide: https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account 
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
ssh-add ~/.ssh/id_rsa
## Add the SSH key to your GitHub account. https://help.github.com/en/github/authenticating-to-github/adding-a-new-ssh-key-to-your-github-account
git config --global user.email "your_email@example.com"


## Local git repository
$ mkdir git-repo-name && cd git-repo-name
$ git init
$ # Write some code.
$ git add 
$ git commit -m "New repo"

## Pushing local git repo to github
$ # Create repository on Github
$ git remote add origin git@github.com:username/new_repo
$ git push -u origin master

## Creating repo on github an pulling locall
$ # Create repository on Github
$ git pull git@github.com:username/new_repo

# Ones I make modifications to existing repository I use these commands every time:
$ git add .
$ git commit -m "Added stuff"
$ git push
```

<br />

### Practical Ethical Hacking Course
I am 40% through this Udemy course, I would highly recommend anyone who is new to the cyber security industry to take this course. It goes through Networking, Linux basic, Python basic, Ethical Hacker Methodology, Information Gathering (Recon), Scanning and Enumeration, Exploitation, Hack The Box walkthroughs, and more.

All my notes on the course so far are hosted on my Notes repository within on CherryTree, I will do an extensive review ones I am done with the course.

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/0619-PEH-Udemy.png" style="width:250px;"><br/>
<i>Cherrytree Practical Ethical Hacking course notes</i>
</p>
<br />

### Kontra's Application Security Training
I found Kontra's Application Security Training site by coincidence through a Twitter post, I have shared this with my friends and colleagues. Kontra's AppSec training is a great way to learn web application vulnerabilities through an interactive web app. I have only done the TikTok XSS but so far they have 21 trainings and continue to expand! Examples of OWASP Top 10 that are covered, Command Injection, SSRF, Directory Traversal. SQL injection, and more!

[https://application.security/free-application-security-training/](https://application.security/free-application-security-training/)

<br />

### CherryTree and Gitbook
Through the practical ethical hacking course, CyberMentor emphasizes note-taking. Because of his recommendations I started using CherryTree. I use it both on my Parrot OS laptop and my personal Windows machine. In order for them to sync I followed [MrChrisWeinert's CherryTreeGitSync](https://github.com/MrChrisWeinert/CherryTreeGitSync), which essentially allows you to create a Git repository to store the cherrytree file of your notes, and every time you open or exit cherrytree the script syncs with the git repository.

Although I have enjoyed using CherryTree and will continue to use it, my main form of sharing my hacking notes will be moved to my [Defender Notes Gitbook](https://defender-gb.gitbook.io/defender-notes/). 

<p style="text-align: center;">
<img src="https://raw.githubusercontent.com/DefenderGB/defendergb.github.io/master/_images/0619-CherryTreeNotes.png" style="width:250px;"><br/>
<i>Cherrytree notes</i>
</p>