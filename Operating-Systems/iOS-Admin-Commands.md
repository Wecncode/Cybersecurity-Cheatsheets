# iOS Admin Commands

While iOS is a heavily sandboxed operating system, administrators, forensic analysts, and security researchers rely on bridging tools (like `libimobiledevice`), mobile device management (MDM) payloads, and runtime manipulation to manage devices, extract logs, and audit application security.

### 1. Device Enumeration & Management (`libimobiledevice`)

The `libimobiledevice` toolkit allows Linux, macOS, and Windows machines to communicate natively with iOS devices via USB, bypassing the need for iTunes.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `ideviceinfo` | Dumps extensive hardware and software details. | iOS version (to check for known jailbreaks/CVEs), UDID, and serial numbers. |
| `ideviceinstaller -l` | Lists all third-party apps installed on the device. | Identifying target applications for reverse engineering or auditing. |
| `idevicesyslog` | Streams the real-time system log from the device. | App crashes, hardcoded credentials printed to the console, or background errors. |
| `idevicebackup2 backup <path>` | Creates a local backup of the device. | Unencrypted backups can be parsed to extract SQLite databases containing sensitive app data. |
| `idevicecrashreport <path>` | Extracts crash reports from the device. | Useful for exploit development and identifying memory corruption bugs. |

### 2. Enterprise Administration (`cfgutil`)

`cfgutil` is the command-line interface for Apple Configurator. It is heavily used by MDM administrators for zero-touch deployment and provisioning.

| Command | Purpose | Notes |
| --- | --- | --- |
| `cfgutil list` | Lists all connected iOS/iPadOS devices. | Quick verification of device connectivity and state. |
| `cfgutil get profile` | Retrieves installed configuration profiles. | Malicious or rogue profiles can route traffic through attacker-controlled proxies or install unauthorized root certificates. |
| `cfgutil install-profile <file>` | Pushes a `.mobileconfig` payload to the device. | Used to quickly provision VPNs, Wi-Fi certs, or proxy settings for Burp Suite interception. |
| `cfgutil syslog` | Captures device logs locally via macOS. | A native alternative to `idevicesyslog` for macOS users. |

### 3. On-Device Enumeration (Jailbroken via SSH)

Once an iOS device is jailbroken and SSH is enabled (typically via OpenSSH on port 22 or via USB proxy on port 2222), these commands map the internal filesystem.

| Command | Purpose | What to Look For |
| --- | --- | --- |
| `dpkg -l` | Lists installed APT packages. | Security tweaks, SSL pinning bypasses (like SSL Kill Switch), or developer tools. |
| `find /var/containers/Bundle/Application/` | Locates the installation directories of third-party apps. | The `.app` directory contains the compiled binary and static resources (Info.plist). |
| `find /var/mobile/Containers/Data/` | Locates the data directories for third-party apps. | SQLite databases (`.sqlite`), UserDefaults (`.plist`), and cached files storing cleartext data. |
| `keychain_dumper` | Dumps the iOS Keychain. | Wi-Fi passwords, saved app credentials, and authentication tokens (requires root). |

### 4. Dynamic Analysis & Runtime Testing

For web application and mobile security testers, these tools are standard for bypassing iOS security controls during an assessment.

| Command | Purpose | Notes |
| --- | --- | --- |
| `frida-ps -U` | Lists all running processes on the connected USB device. | Finds the exact bundle identifier (e.g., `com.apple.Preferences`) needed to hook an app. |
| `frida -U -f <bundle_id> -l script.js` | Injects a custom JavaScript payload into the app at runtime. | Bypassing jailbreak detection, biometric prompts (FaceID), or logging crypto functions. |
| `objection explore` | Drops into a runtime mobile exploration shell. | A wrapper for Frida that allows you to read local storage, bypass SSL pinning, and manipulate the keychain without writing custom scripts. |
| `otool -l <binary> | grep cryptid` | Checks if an iOS binary is encrypted by FairPlay (App Store DRM). | A `cryptid 1` means the app is encrypted. It must be dumped from memory (via tools like `frida-ios-dump`) before reverse engineering. |

*Developed by Wecncode Developer Community! 🩶*
