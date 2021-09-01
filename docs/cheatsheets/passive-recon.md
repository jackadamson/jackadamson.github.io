---
title: Passive Recon
tags:
- cheatsheet
- snippet
- recon
---

General resource: https://osintframework.com/

## Recon-NG

Modular recon tool by Blackhills Infosec

```
marketplace search google
marketplace info recon/domains-hosts/google_site_web
marketplace install recon/domains-hosts/google_site_web
marketplace load recon/domains-hosts/google_site_web
info
options set SOURCE example.com
run
show companies
show contacts
show credentials
show ...
```

## Git Searching

- `gitrob`
- `gitleaks`

## Shodan

TODO

## theharvester

A multi tool for geting info from a company 

https://github.com/laramies/theHarvester

```
theHarvester -d example.com -b google
```
