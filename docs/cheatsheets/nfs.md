---
title: NFS (Network File System)
tags:
- cheatsheet
- snippet
- recon
- nfs
- linux
---

## Enumeration

Port and IP vary
```bash
nmap -p 111 --script=nfs-ls,nfs-statfs,nfs-showmount 10.10.184.192
```

## Mount

```
mount -t nfs 10.11.1.72:/home /tmp/mnt -nolock
```

## Tips

- Permissions may be checked client side, so add a user with given uid may bypass
