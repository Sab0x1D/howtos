# Snort 101 & Command Cheatsheet

Snort is a powerful **Network Intrusion Detection/Prevention System (NIDS/NIPS)** that can:
- Sniff traffic in real-time
- Log packets for analysis
- Detect & prevent attacks with custom rules

---

## Global Commands
| Command | Description |
|---------|-------------|
| `snort -V` | Display version |
| `snort --version` | Display version (alt) |
| `snort -q` | Suppress version banner |
| `snort -i eth0` | Use specific interface |

---

## Sniffer Mode
| Command | Description |
|---------|-------------|
| `snort -v` | Verbose mode |
| `snort -e` | Display link-layer headers |
| `snort -d` | Display data payload |
| `snort -X` | Full packet details in HEX |
| `snort -ex` | Display all packet details |
| `snort -v -n 10` | Sniff “N” number of packets |

---

## Logger Mode
| Command | Description |
|---------|-------------|
| `snort -v -l /var/log/snort` | Log packets to default path |
| `snort -v -l ~/Desktop` | Use alternative log path |
| `snort -v -K ASCII` | Log in ASCII format |
| `snort -r snort.log` | Read snort log |
| `snort -r snort.log -n 10` | Read “N” packets |
| `snort -v -r snort.log tcp` | Filter packets with BPF (e.g., TCP) |
| `snort -v -r snort.log udp and port 53` | Filter UDP packets on port 53 |

---

## PCAP Processing
| Command | Description |
|---------|-------------|
| `snort -c /etc/snort/snort.conf -q -r file.pcap -A console` | Process a single PCAP |
| `snort -c /etc/snort/snort.conf -q -r file1.pcap file2.pcap -A console` | Process multiple PCAPs |
| `snort -c /etc/snort/snort.conf -q -r /home/pcap-folder -A console` | Process PCAPs from folder |
| `snort -c /etc/snort/snort.conf -q -r file1.pcap file2.pcap -A console --pcap-show` | Show processed PCAP names |

---

## IDS/IPS Mode
| Command | Description |
|---------|-------------|
| `snort -c /etc/snort/snort.conf` | Use configuration file |
| `snort -T -c /etc/snort/snort.conf` | Test config & rules |
| `snort -N -c /etc/snort/snort.conf` | Disable logging |
| `snort -D -c /etc/snort/snort.conf` | Run in background |

### Alert Modes:
- `-A none` → No output
- `-A console` → Output to console
- `-A console -A file` → Output to console & file

Example:
```bash
snort -c /etc/snort/snort.conf -A console
```

### Using Custom Rules:
```bash
snort -c /etc/snort/snort.conf -A console -r /etc/rules/localrules.rules
```

---

# Snort Rule Breakdown

Snort rules consist of:

### 1️⃣ Rule Header
Defines **action**, **protocol**, **source/destination IP/port**, and **direction**.

Example:
```snort
alert tcp $EXTERNAL_NET any -> $HOME_NET $HTTP_PORTS
```

### 2️⃣ Rule Options
Contains **message**, **reference**, **flow**, **content**, etc.

---

## Example Rule
```snort
alert tcp $EXTERNAL_NET any -> $HOME_NET $HTTP_PORTS (
    msg:"Directory Traversal Attempt";
    flow:established;
    nocase;
    content:"HTTP";
    content:"../";
    reference:CVE,XXX;
    sid:100001;
    rev:1;
)
```

---

## Rule Components
| Component | Keyword | Description |
|-----------|---------|-------------|
| Action | `alert` | What Snort does |
| Protocol | `tcp` | Protocol (TCP, UDP, ICMP, IP) |
| Source IP | `$EXTERNAL_NET` | Origin IP |
| Source Port | `any` | Origin Port |
| Direction | `->` | Packet direction |
| Dest IP | `$HOME_NET` | Destination |
| Dest Port | `$HTTP_PORTS` | Destination Port |

---

## Rule Options Breakdown
- **msg** → Message logged on match  
- **reference** → External reference (CVE, etc.)  
- **sid** → Unique ID  
- **rev** → Revision number  
- **flow** → Session tracking (e.g., established)  
- **nocase** → Case-insensitive search  
- **content** → Pattern to detect  
- **fast-pattern** → Improves payload search speed  
- **session** → Extracts TCP session data  

---

# Snort Command Cheatsheet (Quick Reference)

### Sniffer Mode
```bash
snort -v                # Verbose
snort -d                # Show data payload
snort -X                # Show packet HEX
snort -v -n 20          # Capture 20 packets
```

### Log & PCAP
```bash
snort -l /var/log/snort       # Log traffic
snort -r capture.pcap         # Read pcap
snort -r capture.pcap -n 5    # Read first 5 packets
```

### IDS/IPS
```bash
snort -c snort.conf -A console    # Run IDS
snort -T -c snort.conf            # Test configuration
snort -D -c snort.conf            # Run in background
```

### Custom Rules
```bash
snort -c snort.conf -A console -r local.rules
```

---

## Quick Tips
- Test new rules using:  
  ```bash
  snort -T -c /etc/snort/snort.conf
  ```
- Use BPF filters to narrow packet capture.
- Store custom rules in `local.rules`.

---
