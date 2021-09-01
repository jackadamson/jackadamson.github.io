---
title: Aliases
description: Useful zsh aliases and functions
tags:
- cheatsheet
- snippet
- aliases
- linux
---

Unless specified, aliases are zsh compatible

## Showsploit

`searchsploit` is useful for getting exploits, use `showsploit` to cat the file

### Usage

```bash
showsploit linux/remote/19503.txt
```

### Definition
```bash
function showsploit {
  exploit_path="/usr/share/exploitdb/exploits/${1}"
  echo "Showing: ${exploit_path}"
  echo "=============="
  cat $exploit_path
}
```
