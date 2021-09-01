---
title: SMB Cheatsheet
tags:
- cheatsheet
- snippet
- recon
- smb
- windows
---

SMB used commonly by Windows for file sharing and way more.

Most common linux server is samba.

## Nmap Enumeration

SMB Enum of users and shares

```bash
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.1.2.3
```

## Explore

Interact live with
```bash
smbclient //10.1.2.3/someshare
```

Or in a file manager go to smb://10.1.2.3/
