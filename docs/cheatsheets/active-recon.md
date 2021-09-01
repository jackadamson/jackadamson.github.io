---
title: Active Recon
tags:
- cheatsheet
- snippet
- recon
- dns
- smb
---

## DNSRecon

Example:
```
dnsrecon -d domain.com -t SCANTYPE
```
Scan types:

- `std` - Look at SOA, NS, A, AAAA, MX and SRV
- `rvl` - Reverse lookup of CIDR or IP range
- `brt` - Brute force
- `axfr` - Zone transfer

## DNSEnum

Example
```
dnsenum domain.com
```

## NetBIOS

```
sudo nbtscan -r 10.1.1.0/24
```
