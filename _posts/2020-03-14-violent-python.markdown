---
layout: post
title:  "Violent Python"
postid: blog
categories:
---


###### tags: `Python`, `WebApp`, `VMware`, `TryHackMe!`

These past 2 weeks began with a focus on reading through Violent Python, developing a further understanding of Web App vulnerabilities (got The Web Application Hacker's Handbook), doing some penetration testing labs to get up to speed with utilizing kali and tools, and watching and gathering information from knowledgeable bug hunters.

While these past two weeks have been everywhere, I wanted to rundown on few things I learned:

#### Python

Violent Python chapter 1 focused on building general knowledge on python, what different variables, libraries, and data structures there are. The book introduces you to each component while crafting a port scanning python script as it goes through new concepts. I have provided my summarized notes below and the code that I came up with at the end of reading through Chapter 1.

#### Web App Vulnerabilities

I was everywhere in this topic, from watching BugCrowd's YouTube channel for general understanding on how to begin doing a bug bounty, subdomain enumeration, testing web app vulnerabilities with burp, and starting to use bWAPP to test simple web app vulnerabilities (XSS, CSRF, and SQL Injection).

#### Configurations on Vmware

Considering I had installed OWASP BWA on my main computer, since it was on NAT/Host-only, it could not communicate with my Bridge VMs (Kali and Mint), I then tried to install vmware player on my kali with failure. Then to realize I can install a new virtual NIC on the Kali and have it communicate with the NAT network. In the attempt to clean up my kali's installation of vmware, I uninstalled a virtual network related package, with broke my virtual instance of kali, forcing me to reinstall kali and loosing 2 weeks of notes. While this was disappointing I enjoyed going through my "Baby Steps" blog and retracing my steps of installation. 

I now *got* a copy of VMware Workstation Pro and created snapshots of my fully build Kali, in case something like this happens again. I also configured Syncthing between Kali and Mint to sync all notes.

I also finished creating my official alias on Bugcrowd and HackerOne, defendergb. I will be moving future blogs to Git pages under https://github.com/defendergb . Stay tuned for updates!
 
## Violent Python Notes

Chapter 1 Setup Development Environment

### Python Language
#### Libraries
Book suggested library installation through easy_install or wget but pip handles installation better.

```javascript
pip install python-nmap pyPdf pygeoip mechanize beautifulsoup4 PyBluez PyOBEX
```

#### Variables
Pointer to data stored in a memory location. Memory location can store different values such as Integers, real numbers, Booleans, strings, or more complex data such as lists or dictionaries

Example: port stores an "integer" and "banner" stores a string

#### Strings
Text that can be used with different methods to manipulate the text. 

Example: upper(), lower(), replace(), find(), etc

#### Lists (Data Structure)
Stores arrays of objects. Can contain any data type

#### Dictionaries (Data Structure)
Provide hash table that can store any number of python objects. It consists of pairs of items that contain a key and value.

#### Networking
Socket module provides library for making network connections using Python.

#### Selection
Conditional select statements. The IF statement evaluates a logical expression in order to make a decision based on the results of the evaluation.

#### Exception Handling
Python allows the handle of errors within context of the running program

#### Functions
Functions provide organized blocks of reusable code, it allows code to perform a single, related action. These are user-defined functions instead of just using built-in functions.

#### Iterations
You can iterate through a list, numbers, or elements.

#### File I/O
Instead of creating huge IF statements, python allows iterating through a file using .readlines()

#### Sys Module
Built-in sys module provides access to objects used by python interpreter. Including flags, version, max sizes of integers, available modules, path hooks, location of standard error/in/out, and command line arguments called by interpreter. 

Example: sys.argv[1] would parse out txt file that we implemented on script.

#### OS Module
Built-in OS modules provide OS routines for Mac, NT, or Posix OS. Module allows the program to independently interact with OS env, file-system, user database, and permissions.

#### Script created
Port Scanner - (Link Pending)

UNIX password Cracker
{%gist DefenderGB/c322fc7cbc8c294d3f854a8ae98be09b}

Zip-File Password Cracker - (Link Pending)



## TryHackMe! Basic Penetration Testing Lab Notes
(Lost due to Kali breaking) 

Followed YouTube video below, very easy lab.


# References
[TryHackMe! Basic Penetration Testing Lab](https://www.youtube.com/watch?v=xl2Xx5YOKcI&list=PLu38WVf2qAwR0GqLwSZ0r5lSIAj46b-Vq&index=61&t=0s)

[Bugcrowd University - Introduction + Becoming a Bug Bounty Hunter](https://www.youtube.com/watch?v=EZzAl-bfu7Q&list=PLu38WVf2qAwR0GqLwSZ0r5lSIAj46b-Vq&index=50&t=0s%20%20https://www.youtube.com/watch?v=dlx0_63CjC8&list=PLu38WVf2qAwR0GqLwSZ0r5lSIAj46b-Vq&index=72&t=0s)

[Bugcrowd University - Cross-Site Scripting (XSS)](https://www.youtube.com/watch?v=gkMl1suyj3M&t=1259s)

[@tomnomnom suggestions on bug bounty](https://www.youtube.com/watch?v=l8iXMgk2nnY&list=PLu38WVf2qAwR0GqLwSZ0r5lSIAj46b-Vq&index=26&t=0s)
