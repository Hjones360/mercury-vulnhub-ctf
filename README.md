# Mercury VulnHub CTF Walkthrough 🚀

## 🎯 Objective
Conduct a penetration test on the Mercury VM from VulnHub using Kali Linux. Discover the target on the network, identify vulnerabilities, exploit them, and escalate privileges to root.

## 🛠️ Tools & Techniques
- Kali Linux
- arp-scan
- Nmap
- Dirsearch
- SQLMap
- SSH
- Base64 decoding
- Privilege escalation via PATH hijacking

## 🔍 Summary of Steps

### 1. Network Discovery
- Used `sudo arp-scan -l` to discover devices.
- Identified IP addresses `10.0.2.2`, `10.0.2.3`, and later `10.0.2.5`.

### 2. Port Scanning
- Scanned devices using `nmap -sV -Pn`.
- Found open ports 22 and 8080 on `10.0.2.5`.

### 3. Enumeration
- Accessed `http://10.0.2.5:8080` in browser.
- Installed and ran `dirsearch` to find `/robots.txt`.
- Identified new path: `/mercuryfacts`.

### 4. SQL Injection
- Navigated `/mercuryfacts/1/` and saw potential SQL injectable parameters.
- Used `sqlmap` to enumerate databases and dump the `mercury` DB.
- Recovered usernames and passwords.

### 5. Gained SSH Access
- Tried credentials via SSH.
- Successfully logged in as `webmaster`.
- Captured the first user flag.

### 6. Privilege Escalation
- Found `notes.txt` with a base64-encoded password.
- Switched to user `linuxmaster`.
- Discovered `check_syslog.sh` executable with root privileges.
- Created a symbolic link to hijack the PATH.
- Executed `check_syslog.sh` and popped a root shell.

### 7. Final Capture
- Accessed `/root` and captured `rootflag.txt`.

## 📝 Lessons Learned
- Importance of proper VM networking (NAT + internal).
- Manual enumeration when tools fail.
- Basics of SQL injection and database dumping.
- Privilege escalation using SUID binaries and PATH hijacking.

## 🔗 Resources
- [Mercury VulnHub VM](https://download.vulnhub.com/theplanets/Mercury.ova)
- [Walkthrough Guide](https://vishnushivalalp.medium.com/the-planets-mercury-vulnhub-ctf-walkthrough-c5f15bdc24b7)

## 📁 Full Walkthrough
See `docs/full-walkthrough.md` for detailed step-by-step documentation.
