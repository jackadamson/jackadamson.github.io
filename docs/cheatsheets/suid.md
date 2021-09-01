---
title: SUID/SGID
tags:
- cheatsheet
- privesc
- setuid
- linux
---

SetUID (and SetGID) binaries in Linux will run as the owner user (or group) when executed instead of the current user.

These files are specified by first octet in the permissions of the file.

| Permission | On Files                                                        | On Directories                                           |
| ---------- | --------------------------------------------------------------- | -------------------------------------------------------- |
| SUID Bit   | User executes the file with permission of the *file* owner      | -                                                        |
| SGID Bit   | User executes the file with the permission of the *group* owner | Files created in directory gets the same group owner     |
| Sticky Bit | -                                                               | Users are prevented from deleting files from other users |

## Finding

```bash
find / -perm -u=s -type f 2>/dev/null
```

## Filtering

Automatic tool: https://github.com/Anon-Exploiter/SUID3NUM

As many binaries are meant to be setuid, this is a list of binaries from Kali that are suid to filter against.

```text
/usr/bin/bwrap
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/fusermount3
/usr/bin/gpasswd
/usr/bin/mount
/usr/bin/newgrp
/usr/bin/ntfs-3g
/usr/bin/passwd
/usr/bin/pkexec
/usr/bin/su
/usr/bin/sudo
/usr/bin/umount
/usr/lib/chromium/chrome-sandbox
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/xorg/Xorg.wrap
/usr/sbin/mount.cifs
/usr/sbin/mount.nfs
/usr/sbin/pppd
```

## Bash Gotcha

By default, bash will drop suid privs unless the `-p` flag is provided
