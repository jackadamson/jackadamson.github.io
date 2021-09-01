---
title: RPC (Remote Procedure Call)
tags:
- cheatsheet
- snippet
- recon
- rpc
---

"Your earlier nmap port scan will have shown port 111 running the service rpcbind. This is just a server that converts remote procedure call (RPC) program number into universal addresses. When an RPC service is started, it tells rpcbind the address at which it is listening and the RPC program number its prepared to serve."

Can be enumerated with
```bash
sudo nmap -sSUC -p111 10.11.1.72
```
