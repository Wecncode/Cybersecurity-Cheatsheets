# Wireshark Toolkit

Wireshark is an essential tool for packet analysis. In a sea of network noise, display filters are critical for pinpointing malicious behavior, lateral movement, and compromised credentials.

### 1. Plain-Text Credential Sniffing

Protocols lacking encryption transmit authentication data in plain text. These filters quickly isolate login attempts and captured credentials.

| Filter | Target Protocol | What to Look For |
| --- | --- | --- |
| `http.request.method == "POST" and (http.file_data contains "user" or http.file_data contains "pass")` | HTTP | Credentials submitted via unencrypted web login forms. |
| `ftp.request.command == "USER" or ftp.request.command == "PASS"` | FTP | Usernames and passwords sent during FTP authentication. |
| `telnet contains "login" or telnet contains "password"` | Telnet | Interactive unencrypted terminal sessions exposing credentials. |
| `pop.request.command == "USER" or pop.request.command == "PASS"` | POP3 | Plaintext email retrieval authentication. |
| `smtp.req.command == "AUTH" or imap.request contains "login"` | SMTP / IMAP | Unencrypted mail sending/syncing credentials. |
| `ldap.authentication == 0` | LDAP | Simple (plaintext) LDAP binds exposing directory credentials. |

### 2. Malware Analysis & C2 Beacons

Malware often communicates with Command and Control (C2) servers using predictable patterns, tunneling, or anomalous requests.

| Filter | Concept | What to Look For |
| --- | --- | --- |
| `dns.qry.type == 16` | DNS TXT Records | Abnormally large TXT records used for DNS tunneling or downloading malicious payloads. |
| `dns.qry.name.len > 50` | DNS Exfiltration | Extremely long subdomains indicating data exfiltration or Domain Generation Algorithms (DGAs). |
| `http.request.method == "GET" and http.request.uri contains ".exe"` | Payload Delivery | Unencrypted downloads of executables, DLLs, or malicious scripts (e.g., `.ps1`, `.bat`). |
| `tls.handshake.type == 1` | TLS Client Hello | Extracting JA3 fingerprints to identify specific malware families connecting via HTTPS. |
| `http.response.code == 404` | C2 Infrastructure | A high volume of 404 errors from a single host can indicate a malware DGA searching for its active C2 server. |
| `smb2.filename contains ".exe" or smb2.filename contains ".dll"` | Lateral Movement | Malicious binaries being transferred across internal Windows shares (e.g., PsExec, ransomware propagation). |

### 3. Network Reconnaissance & Scans

Identifying how an attacker is mapping the network before exploitation.

| Filter | Concept | What to Look For |
| --- | --- | --- |
| `tcp.flags.syn == 1 and tcp.flags.ack == 0` | SYN Scans | High volumes of SYN packets to different ports from a single IP indicate an Nmap scan. |
| `icmp.type == 8 or icmp.type == 0` | Ping Sweeps | Echo requests and replies used to map live hosts on a subnet. |
| `tcp.flags.reset == 1 and tcp.flags.ack == 1` | Port Rejection | High volumes of RST/ACK packets indicate an attacker is hitting closed ports during a scan. |

### 4. Noise Reduction & Triage

When opening a massive PCAP file, filtering out standard background noise is the first step to finding anomalies.

| Filter | Purpose | Example Usage |
| --- | --- | --- |
| `ip.addr == 192.168.1.50` | Isolate Host | View all traffic to and from a specific compromised or suspicious machine. |
| `!(arp or icmp or dns or mdns or ssdp)` | Drop Broadcast/Common | Strips away standard network chatter to reveal anomalous HTTP, TCP, or UDP streams. |
| `tcp.stream eq X` | Follow TCP Stream | Replace `X` with a stream number to view an entire isolated conversation (e.g., a reverse shell session). |

*Created by Wecncode Developer Community! 🩶*
