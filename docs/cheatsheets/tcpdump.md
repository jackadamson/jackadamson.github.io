---
title: TCPDump
tags:
- cheatsheet
- snippet
- linux
---

CLI Packet Capture tool, uses eBPF under the hood which is pretty cool

| Flag           | Description                        |
| -------------- | ---------------------------------- |
| -n             | Skip DNS resolution                |
| -r *file.pcap* | Read PCAP                          |
| -i *eno1*      | Listen on interface                |
| -X             | Print packet data in ASCII and HEX |

## Examples

Only traffic from **172.16.40.10**
```bash
sudo tcpdump -n src host 172.16.40.10
```

Only port 81 traffic
```bash
sudo tcpdump -n port 81
```

Only show TCP data packets (PSH, ACK)
```bash
sudo tcpdump -n 'tcp[13] = 24'
```
