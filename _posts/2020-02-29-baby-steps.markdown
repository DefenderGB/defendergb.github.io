---
layout: post
title:  "Baby Steps"
postid: blog
categories: Mint OWASP-BWA Kali
---

<br />

## Hello World
On Feb 28, I migrated my Kali, Mint, and my OWASP BWA OS to VMware as they were gathering dust on VirtualBox. After research and final decision, I have decided that VMware was the most optimized to run my VirtualBox, where VirtualBox just wasn't able to compare. I am still running a Ubuntu server on VirtualBox that I set up months ago for creating a encrypting private network.

<br />

### Mint

For my Mint, I just used VirtualBox's feature "Export Appliance" and then opened it and stored it under VMware. Easy peacy, didn't even know that was a feature. Learned it from [HowToGeek blog][1].

<br />

### OWASP BWA
For OWASP BWA, I followed instructions found on [GitHub][2] and downloaded BWA from [SourceForge][3].
NOTE:
1. Make sure to install on HOST-ONLY.
2. Username root with the password owaspbwa.
3. Make sure to change your hosts file to make it easier to browse.

More info on set up see [greenjam's blog][4]

<br />

### Kali

To install Kali 2020.1, I downloaded the VMware version from [Offensive Security website][5]. For hardening I followed [Null's Blog][6] and [Alpha Cyber Security's blog][7]

To summarize some steps I took to harden my Kali:
1. Configuring bash alias
2. Configuring Root account back (was removed on 2020.1 version)
3. Changed name of Kali default user
4. Created a unprivileged toruser for tor browsing
5. Changed default SSH keys
6. Installed Git, Tor, Tilix, Atom, Sublime,
7. Changed UID and GID of users
8. Installed kali-linux-all metapackage
9. Update and upgraded

Next up, I plan on going through [Violent Python book by TJ O'Connor][8] and playing around with SDR dongle and getting radio waves following [OZHack's blog][9].

<br />

### Backlog

As I am passionate to learn and understand Web Security more, I was recommended PortSwigger's course ["Introducing the Web Security Academy"][10].

[1]: https://www.howtogeek.com/125640/how-to-convert-virtual-machines-between-virtualbox-and-vmware/
[2]: https://github.com/chuckfw/owaspbwa
[3]: https://sourceforge.net/projects/owaspbwa/
[4]: https://greenjam94.github.io/Installing-Broken-Web-App/
[5]: https://www.offensive-security.com/kali-linux-vm-vmware-virtualbox-image-download/
[6]: https://null-byte.wonderhowto.com/how-to/top-10-things-do-after-installing-kali-linux-0186450/
[7]: https://alphacybersecurity.tech/how-to-secure-your-kali-linux-machine/
[8]: http://shop.oreilly.com/product/9781597499576.do
[9]: https://ozhack.com/blogs/blog/getting-started-with-the-rtl-sdr-part-1-finding-stuff
[10]: https://portswigger.net/blog/introducing-the-web-security-academy
