# Linux Commands and Privilege escalation

Privilege escalation on Linux involves starting with a low-privileged shell (like `www-data` or a standard user) and enumerating the system to find misconfigurations, vulnerable software, or stored credentials that allow you to become `root`.

### 1. System & Environment Enumeration

Before looking for exploits, you need to understand the environment you are operating in.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `uname -a` | Prints all system and kernel information. | Outdated kernels vulnerable to known exploits (e.g., DirtyCow, PwnKit). |
| `cat /etc/issue` or `cat /etc/os-release` | Displays the OS distribution and release version. | Specific OS version vulnerabilities (e.g., Ubuntu 16.04 vs 22.04). |
| `env` or `printenv` | Lists environment variables. | API keys, hardcoded passwords, or interesting `PATH` configurations. |
| `lscpu` | Displays CPU architecture details. | Knowing if the system is x86, x64, or ARM for compiling custom exploits. |

### 2. User & Group Enumeration

Understanding your current privileges and who else uses the system.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `id` | Shows current user UID, GID, and group memberships. | Membership in privileged groups like `docker`, `lxd`, `disk`, or `sudo`. |
| `cat /etc/passwd` | Lists all users on the system. | Look for users with `/bin/bash` or `/bin/sh` (interactive users) vs service accounts. |
| `cat /etc/shadow` | Contains user password hashes (requires root/sudo). | If readable, you can exfiltrate hashes and crack them offline with Hashcat. |
| `history` or `cat ~/.bash_history` | Views previously executed commands. | Users often type passwords directly into the CLI or pass them as arguments to scripts. |

### 3. Sudo Privileges & SUID Binaries

This is the most common path to root in CTFs and entry-level exams (like the OSCP).

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `sudo -l` | Lists commands the current user can run as root. | Binaries you can run with `sudo` without supplying a password. |
| `find / -perm -4000 -type f 2>/dev/null` | Finds SUID (Set Owner User ID) binaries. | Custom scripts or standard binaries (like `find`, `vim`, `bash`) that execute with root permissions. |
| `getcap -r / 2>/dev/null` | Lists file capabilities. | Binaries with `cap_setuid+ep` which can be used to escalate privileges similar to SUID. |

> **Pro Tip:** Whenever you find an interesting binary in `sudo -l` or via a SUID search, check **GTFOBins** (gtfobins.github.io). It is a curated list of Unix binaries that can be exploited to bypass local security restrictions.

### 4. Scheduled Tasks (Cron Jobs)

Cron jobs run automatically on a schedule. If a cron job runs as root but executes a script you can modify, you can inject malicious code.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `cat /etc/crontab` | System-wide scheduled tasks. | Scripts owned by root but located in a directory where your user has write access. |
| `ls -lah /etc/cron.*` | Lists hourly, daily, weekly, and monthly cron directories. | Custom backup scripts or maintenance tasks with weak file permissions. |
| `pspy64` (Upload to target) | Unprivileged Linux process snooper. | Cron jobs that aren't listed in standard files but are triggering in the background. |

### 5. Internal Network & Services

Sometimes root privileges are hiding behind an internal service that isn't exposed to the outside network.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `ss -tulpn` or `netstat -tulpn` | Lists active network connections and listening ports. | Services listening on `127.0.0.1` (localhost) like internal databases or web apps that you couldn't see from the outside. |
| `ip a` or `ifconfig` | Lists network interfaces. | Secondary network adapters indicating the machine is attached to another internal subnet (pivoting opportunity). |

### 6. Sensitive Files & Cleartext Credentials

Administrators often leave sensitive information lying around.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `find / -name id_rsa 2>/dev/null` | Searches for SSH private keys. | Keys belonging to root or other users to gain an interactive SSH session. |
| `find / -type f -name "*.conf" -o -name "*.config"` | Searches for configuration files. | Hardcoded database credentials or API keys. |
| `cat /var/log/apache2/access.log` | Views web server logs. | Sometimes users accidentally pass passwords in URL parameters (e.g., `?password=Secret123`). |

*Created by Wecncode Developer Community!🩶*
