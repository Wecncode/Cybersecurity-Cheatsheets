# Hash Cracking Fundamentals 

Password cracking is a core component of privilege escalation and lateral movement. Hashcat and John the Ripper (JtR) are the primary tools used for offline cracking, allowing attackers to convert extracted hashes back into cleartext passwords.

### 1. Hash Identification

Before cracking a hash, you must identify its algorithm. Feeding the wrong algorithm type into Hashcat will result in an immediate failure.

| Command | Description | Notes |
| --- | --- | --- |
| `hashid <hash>` | Identifies the different types of algorithms that could have generated the hash. | Built into Kali Linux. Outputs a list of possibilities. |
| `nth --text "<hash>"` | "Name That Hash" - A modern alternative to `hashid`. | Highly accurate and directly provides the exact Hashcat module (`-m`) number. |

### 2. Hashcat: Common Hash Modes (`-m`)

Hashcat requires you to specify the exact hash type using the `-m` flag.

| Mode (`-m`) | Algorithm | Common Origin / Use Case | Example / Identifier |
| --- | --- | --- | --- |
| **0** | MD5 | Older web apps, basic CTF challenges. | `8743b52063cd84097a65d1633f5c74f5` |
| **100** | SHA1 | Legacy systems, outdated SSL certificates. | `b89eaac7e61417341b710b727768294d0e6a277b` |
| **1000** | NTLM | Windows Active Directory passwords (extracted via NTDS.dit or Mimikatz). | `b4b9b02e6f09a9bd760f388b67351e2b` |
| **1400** | SHA256 | Modern web apps, API keys. | (64 characters long) |
| **1800** | SHA512 | Modern Linux local passwords. | Found in `/etc/shadow`, starts with `$6$` |
| **3200** | bcrypt | Highly secure web applications, password managers. | Starts with `$2a$`, `$2b$`, or `$2y$` |
| **13000** | Kerberos 5 TGS-REP | Active Directory Kerberoasting. | Extracted via Impacket's `GetUserSPNs.py` |
| **18200** | Kerberos 5 AS-REP | Active Directory AS-REP Roasting. | Extracted via Impacket's `GetNPUsers.py` |

### 3. Hashcat: Attack Modes (`-a`)

The attack mode dictates how Hashcat generates guesses against the target hash.

| Mode (`-a`) | Name | Description | Example Syntax |
| --- | --- | --- | --- |
| **0** | Dictionary | Tests every single word in a provided wordlist. | `hashcat -a 0 -m 1000 hash.txt rockyou.txt` |
| **1** | Combinator | Combines words from two wordlists (e.g., `admin` + `123`). | `hashcat -a 1 -m 0 hash.txt list1.txt list2.txt` |
| **3** | Brute-force / Mask | Tries all character combinations based on a given pattern (mask). | `hashcat -a 3 -m 0 hash.txt ?a?a?a?a` |
| **6** | Hybrid (Wordlist + Mask) | Appends a mask to a wordlist (e.g., `password` + `123`). | `hashcat -a 6 -m 1000 hash.txt rockyou.txt ?d?d?d` |
| **7** | Hybrid (Mask + Wordlist) | Prepends a mask to a wordlist (e.g., `123` + `password`). | `hashcat -a 7 -m 1000 hash.txt ?d?d?d rockyou.txt` |

### 4. Hashcat: Masks & Rules

Rules mutate a wordlist (e.g., capitalizing the first letter, adding numbers to the end, substituting `@` for `a`). This significantly increases success rates against complex passwords without needing massive multi-gigabyte text files.

**Common Masks:**

* `?l` = Lowercase (`a-z`)
* `?u` = Uppercase (`A-Z`)
* `?d` = Digit (`0-9`)
* `?s` = Special character (`!@#$%`)
* `?a` = All characters (`?l?u?d?s`)

**Applying Rule Sets:**

| Command | Description |
| --- | --- |
| `hashcat -a 0 -m 1000 hash.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule` | Applies the standard `best64` rule set to `rockyou.txt` (excellent for fast, high-probability cracking). |
| `hashcat -a 0 -m 1000 hash.txt rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule` | A heavier rule set for deep, long-term offline cracking. |
| `hashcat -a 0 -m 1000 hash.txt rockyou.txt -r OneRuleToRuleThemAll.rule` | A highly popular custom rule set available on GitHub that dominates CTF challenges. |

*Developed by Wecncode Developer Community!🩶*
