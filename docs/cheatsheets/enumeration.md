---
title: Enumeration
tags:
- cheatsheet
- snippet
- recon
- enumeration
---
## Network Based Enumeration

If Windows (dropping pings) add `-Pn` to scans

Basic scan
```bash
nmap $TARGET_IP
```

Service version detection
```bash
nmap -sV $TARGET_IP
```

List available nmap scripts
```bash
ls /usr/share/nmap/scripts
```

### SMB Enumeration

```bash
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse $TARGET_IP
```

### HTTP Enumeration

Gobuster
```bash
gobuster dir -u http://$TARGET_IP/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt
```
Nikto - TODO

Check:

- sitemap.xml
- robots.txt

### SMTP Enumeration

```bash
smtp-user-enum -M VRFY -U users.txt -t 10.1.1.1
smtp-user-enum -M VRFY -u bob -t 10.1.1.1
```

## Linux Enumeration

### Automated

My favourite: LinPEAS  
https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS

LinEnum
```bash
wget https://github.com/rebootuser/LinEnum/raw/master/LinEnum.sh
chmod +x LinEnum.sh
./LinEnum.sh
```

SUID3NUM - find Suid files
```bash
wget https://github.com/Anon-Exploiter/SUID3NUM/raw/master/suid3num.py
python suid3num
```

## Windows

TODO

## Subdomain Enumeration

- https://searchdns.netcraft.com
- https://dnsdumpster.com/
