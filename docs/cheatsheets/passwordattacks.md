---
title: Password Attacks
tags:
- cheatsheet
- snippet
- passwordattack
---
## Web Based Attacks

### Hydra
```bash
TARGET_USER=target 
TARGET_IP=
hydra -l $TARGET_USER -P /usr/share/wordlists/rockyou.txt 
```

Useful Flags

| Flag | Description                        |
| ---- | ---------------------------------- |
| -f   | Stop attack once combination found |
