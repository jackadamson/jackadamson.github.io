---
title: Rustscan
tags:
- cheatsheet
- snippet
- recon
- tool
- enumeration
---

Like NMAP (actually semi wraps nmap) but very fast because rust.

## Syntax

```bash
rustscan 10.1.1.100 -- <nmapargs>
```

This will do the initial port scan with rust, and then call nmap with `-Pn -p PORTS` where ports are the ports that are open
