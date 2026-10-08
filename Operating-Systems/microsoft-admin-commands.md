# Windows Admin Commands

In a Windows Active Directory (AD) environment, the same administrative commands used by IT professionals for daily management are frequently utilized by attackers for "Living off the Land" (LotL) enumeration.

### 1. Legacy `net` Commands (CMD)

The `net` executable is built into almost every version of Windows. While older, it rarely gets flagged by basic antivirus software, making it a reliable tool for initial domain enumeration.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `net user /domain` | Lists all users in the Active Directory domain. | Identifying target accounts, service accounts, or administrative users. |
| `net user <username> /domain` | Pulls detailed information about a specific domain user. | Group memberships, password expiration policies, and last logon times. |
| `net group "Domain Admins" /domain` | Lists members of the Domain Admins group. | High-value targets for privilege escalation. |
| `net accounts /domain` | Displays the domain's password policy. | Minimum password length and lockout thresholds (crucial before brute-forcing). |
| `net share` | Lists SMB shares on the local machine. | Misconfigured file shares hosting sensitive scripts or cleartext passwords. |
| `net localgroup administrators` | Lists local administrators on the current machine. | Verifying if the current user or compromised domain user has local admin rights. |

### 2. Active Directory PowerShell Module

The `ActiveDirectory` PowerShell module provides highly detailed querying capabilities. It is typically installed on Domain Controllers and machines with Remote Server Administration Tools (RSAT).

| Command | Purpose | Notes |
| --- | --- | --- |
| `Get-ADUser -Filter * -Properties *` | Dumps all properties for all domain users. | Look for the `Description` field; admins often leave passwords or sensitive notes here. |
| `Get-ADComputer -Filter *` | Lists all computers joined to the domain. | Finding target servers (e.g., File Servers, SQL databases) to pivot towards. |
| `Get-ADGroupMember "Domain Admins"` | Extracts the exact members of a specific group. | Can be piped to `Select-Object Name` to quickly build a target list. |
| `Get-ADDomainController -Filter *` | Identifies all Domain Controllers (DCs) in the network. | Locating the crown jewels of the network. |
| `Get-ADTrust -Filter *` | Maps out domain trusts. | Discovering if compromising this domain allows access to a parent or child domain. |

### 3. WMIC (Windows Management Instrumentation)

WMIC is a command-line interface for WMI. It is incredibly powerful for querying system data and executing processes remotely, though it is being slowly deprecated in favor of native PowerShell cmdlets.

| Command | Purpose | Notes |
| --- | --- | --- |
| `wmic useraccount get name,sid` | Retrieves usernames alongside their Security Identifiers (SIDs). | SIDs ending in `-500` indicate the built-in Administrator account. |
| `wmic qfe get Caption,Description,HotFixID,InstalledOn` | Lists installed Windows Updates (patches). | Identifying missing patches to exploit known local privilege escalation (LPE) flaws. |
| `wmic process list brief` | Lists currently running processes. | Discovering antivirus products, EDR agents, or vulnerable software running in the background. |
| `wmic product get name,version` | Lists installed software via MSI. | Finding outdated software versions vulnerable to exploitation. |

### 4. Modern PowerShell (Built-in Cmdlets)

These commands do not require the RSAT tools and run natively on modern Windows systems (Windows 10/11, Server 2016+).

| Command | Purpose | Notes |
| --- | --- | --- |
| `Get-LocalUser` | Lists all local user accounts. | Finding hidden backdoor accounts on the local machine. |
| `Get-LocalGroupMember -Group "Administrators"` | Checks who is in the local admin group. | Often includes domain groups like "Domain Admins" or specific helpdesk groups. |
| `Resolve-DnsName -Name <hostname>` | Queries DNS records directly from PowerShell. | Equivalent to `nslookup`. Useful for finding internal IP addresses for domain resources. |
| `Test-NetConnection -ComputerName <IP> -Port <Port>` | Tests if a specific port is open on a target machine. | A native, stealthy alternative to running a full Nmap scan from a compromised host. |


*Created by Wecncode Developer Community!🩶*

