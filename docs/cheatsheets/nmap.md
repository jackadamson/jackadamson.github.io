---
title: Nmap Cheatsheet
tags:
- cheatsheet
- snippet
- recon
- tool
- enumeration
---

Can specify single IP, CIDR or space delimited multiple locations.

I'm lazy just go to https://www.stationx.net/nmap-cheat-sheet/

```bash
nmap --help
```

## Target Specification

| Switch    | Description             | Notes                       |
| --------- | ----------------------- | --------------------------- |
| -iL       | Scan targets from file  | newline delimited text file |
| --exclude | Exclude IP from targets |                             |

## Scan Techniques

| Switch | Description                                  |
| ------ | -------------------------------------------- |
| -sS    | TCP SYN port scan (default)                  |
| -sT    | TCP connect port scan (default without root) |
| -sU    | UDP port scan                                |
| -sA    | TCP ACK port scan                            |
| -sW    | TCP Window port scan                         |
| -sM    | TCP Mainmon port scan                        |

## Host Discovery

| Switch | Description                               | Notes                                          |
| ------ | ----------------------------------------- | ---------------------------------------------- |
| -sL    | Don't scan just list targets              |                                                |
| -sn    | No port scan, just ping/arp scan          |                                                |
| -Pn    | No host scan (don't ping first) just port | Useful against window / machines blocking ping |
| -n     | No DNS resolution                         |                                                |

## Other Useful Flags

| Switch | Description         |
| ------ | ------------------- |
| -O     | Enable OS detection |
| -b     | FTP Bounce scan     |


## Port Spec

SEE https://www.stationx.net/nmap-cheat-sheet/

## Scripts

SMB Enum

```bash
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.10.184.192
```
