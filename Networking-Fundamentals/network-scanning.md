# Network Scanning Commands

Nmap (Network Mapper) is the industry standard for network discovery, port scanning, and vulnerability enumeration.

### 1. Basic & Target Specification

| Command | Description | Best Use Case |
| --- | --- | --- |
| `nmap <IP>` | Scans the top 1,000 TCP ports. | Quick baseline check of a single host. |
| `nmap 192.168.1.0/24` | Scans an entire CIDR subnet. | Discovering live hosts on a local network. |
| `nmap -iL targets.txt` | Scans a list of IPs from a text file. | Large-scale engagements or automated pipelines. |
| `nmap -sn <IP/CIDR>` | Ping sweep (disables port scan). | Quickly mapping which IPs are online without alerting port monitors. |

### 2. Scan Techniques

| Command | Purpose | Mechanism |
| --- | --- | --- |
| `nmap -sS <IP>` | SYN "Stealth" Scan (Default as root). | Sends SYN, waits for SYN-ACK, then drops the connection. Faster and less likely to be logged. |
| `nmap -sT <IP>` | TCP Connect Scan (Default as user). | Completes the full 3-way handshake. Highly visible in logs. |
| `nmap -sU <IP>` | UDP Scan. | Sends UDP packets. Slow, but essential for finding DNS (53), SNMP (161), or TFTP (69). |

### 3. Port Specification

| Command | Description | Example |
| --- | --- | --- |
| `-p <port>` | Scan a specific port or range. | `nmap -p 22,80,443 <IP>` or `nmap -p 1-1000 <IP>` |
| `-p-` | Scan all 65,535 TCP ports. | `nmap -p- <IP>` |
| `-F` | Fast mode (scans top 100 ports instead of 1,000). | `nmap -F <IP>` |
| `--top-ports <n>` | Scans the highest ratio ports. | `nmap --top-ports 10 <IP>` |

### 4. Service, OS Detection, & Aggressive Scanning

| Command | Description | What to Look For |
| --- | --- | --- |
| `nmap -sV <IP>` | Service Version Detection. | Outdated software versions (e.g., Apache 2.4.49) vulnerable to known CVEs. |
| `nmap -O <IP>` | OS Detection. | Determining if the target is Windows, Linux, or a specific network appliance. |
| `nmap -A <IP>` | Aggressive Scan. | Combines `-sV`, `-O`, `-sC`, and `--traceroute`. Best used against a single verified target. |

### 5. Nmap Scripting Engine (NSE)

Nmap uses Lua scripts to automate vulnerability detection and exploitation. Scripts are stored in `/usr/share/nmap/scripts/`.

| Command | Description |
| --- | --- |
| `nmap -sC <IP>` | Runs the default script category. |
| `nmap --script vuln <IP>` | Runs all scripts in the "vuln" category to check for known vulnerabilities. |
| `nmap --script smb-os-discovery <IP>` | Runs a specific script (e.g., pulling SMB info from Windows). |
| `nmap --script "http-*" <IP>` | Runs all scripts starting with "http-". |

### 6. Timing & Performance

*Warning: Faster scans are noisier and more likely to drop packets, leading to inaccurate results or missed ports.*

| Command | Speed Level | Description |
| --- | --- | --- |
| `-T0` to `-T1` | Paranoid / Sneaky | Extremely slow, used to bypass Intrusion Detection Systems (IDS). |
| `-T3` | Normal | Nmap's default timing. |
| `-T4` | Aggressive | Fast scan. Highly recommended for CTFs (HackTheBox, TryHackMe) on reliable networks. |
| `--min-rate <number>` | Packet Rate | Forces Nmap to send packets no slower than `<number>` per second (e.g., `--min-rate 5000`). |

### 7. Output Formats

Always save your scan results. You cannot exploit what you forget to document.

| Command | Description | Note |
| --- | --- | --- |
| `-oN scan.txt` | Normal output | Exactly as it appears on the screen. |
| `-oG scan.grep` | Grepable output | Easy to parse with `grep`, `awk`, or `cut`. |
| `-oX scan.xml` | XML output | Required for importing into tools like Metasploit or Searchsploit. |
| `-oA scan` | All formats | **Best Practice:** Outputs Normal, Grepable, and XML simultaneously as `scan.nmap`, `scan.gnmap`, and `scan.xml`. |

*Created by Wecncode Developer Community! 🩶*
