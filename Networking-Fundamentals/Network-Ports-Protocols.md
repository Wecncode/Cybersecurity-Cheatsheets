# Network Ports Protocols

Memorizing common ports and their associated protocols is critical for rapid network enumeration and identifying potential attack vectors during an engagement.

### Remote Management & Access

These ports are used for remote administration. If exposed to the internet, they are prime targets for brute-force attacks and credential stuffing.

| Port | Protocol | Service | Common Attack Vectors & Notes |
| --- | --- | --- | --- |
| **22** | TCP | SSH (Secure Shell) | Brute-forcing (`Hydra`), weak private keys (Debian PRNG), or outdated vulnerable versions. |
| **23** | TCP | Telnet | Transmits data in plaintext. Can be easily sniffed with Wireshark to capture credentials. |
| **3389** | TCP | RDP (Remote Desktop) | BlueKeep (CVE-2019-0708), brute-forcing, or credential theft. |
| **5985** | TCP | WinRM (Windows Remote) | HTTP transport. Used for lateral movement (e.g., Evil-WinRM) with valid credentials or Pass-The-Hash. |
| **5986** | TCP | WinRM (Secure) | HTTPS transport. Same attack paths as 5985 but encrypted. |

### File Transfer & Network Sharing

Services designed to share files across a network. Misconfigurations here often lead to anonymous access or remote code execution (RCE).

| Port | Protocol | Service | Common Attack Vectors & Notes |
| --- | --- | --- | --- |
| **20/21** | TCP | FTP (File Transfer) | Anonymous login enabled, plaintext credentials, ProFTPD/vsftpd exploits. |
| **69** | UDP | TFTP (Trivial FTP) | No authentication required. Often used to download router configs or upload malicious payloads. |
| **139** | TCP | NetBIOS / SMB | Used for older Windows file sharing. Enumeration with `enum4linux`. |
| **445** | TCP | SMB (Server Message Block) | EternalBlue (MS17-010), anonymous share access, Relay attacks, or Pass-The-Hash. |
| **2049** | TCP/UDP | NFS (Network File System) | Misconfigured exports (`showmount -e`). Can lead to mounting sensitive filesystems on the attacker's machine. |

### Web, Email, & Communications

The backbone of web and mail infrastructure. Vulnerabilities here often reside in the application layer rather than the protocol itself.

| Port | Protocol | Service | Common Attack Vectors & Notes |
| --- | --- | --- | --- |
| **80** | TCP | HTTP | Web app vulnerabilities (SQLi, XSS, LFI). Directory brute-forcing (`Gobuster`, `ffuf`). |
| **443** | TCP | HTTPS | Same as HTTP, plus SSL/TLS misconfigurations (Heartbleed). |
| **25** | TCP | SMTP (Email Routing) | Open relays, user enumeration via `VRFY` or `EXPN` commands. |
| **110** | TCP | POP3 (Email Retrieval) | Plaintext credentials. Replaced largely by IMAP and webmail. |
| **143** | TCP | IMAP (Email Sync) | Plaintext credentials, brute-forcing. |

### Infrastructure & Databases

Core services that keep domains running and store critical data.

| Port | Protocol | Service | Common Attack Vectors & Notes |
| --- | --- | --- | --- |
| **53** | TCP/UDP | DNS (Domain Name Sys) | Zone transfers (`dig axfr`), DNS cache poisoning. TCP is used for zone transfers; UDP for standard queries. |
| **161/162** | UDP | SNMP (Network Mgmt) | Default community strings ("public" or "private"). Leaks extensive system and routing data (`snmpwalk`). |
| **389** | TCP | LDAP (Directory Access) | Null sessions, anonymous binds. Used heavily for Active Directory enumeration. |
| **636** | TCP | LDAPS (Secure LDAP) | Encrypted LDAP. Same enumeration paths if credentials are obtained. |
| **1433** | TCP | MSSQL | Default `sa` credentials, command execution via `xp_cmdshell`. |
| **3306** | TCP | MySQL | Brute-forcing, weak credentials. |
| **6379** | TCP | Redis | Unauthenticated access leading to SSH key manipulation or RCE. |

*Created by Wecncode Developer Community! 🩶*
