# BCY602 – Cryptography and Network Security
# Module 5 – Exam Revision Notes

**Topics covered:** DomainKeys Identified Mail (DKIM) · IP Security Overview · IP Security Policy · Encapsulating Security Payload (ESP) · Combining Security Associations · Internet Key Exchange (IKE)

---

## Module 5 – Topic & Subtopic List

### 1. DomainKeys Identified Mail (DKIM)
- Introduction to DKIM (cryptographic signing, RFC 6376)
- Email Threats (RFC 4686) – attacker characteristics, capabilities, location
- DKIM Strategy (comparison with S/MIME and PGP)
- DKIM Functional Flow (signing ADMD and verifying ADMD)
- DKIM Signature header fields/tags (v, a, c, d, s, h, bh, b)
- Canonicalization (simple vs relaxed)

### 2. IP Security Overview
- Background (RFC 1636, IAB recommendations)
- Applications of IPsec
- Benefits of IPsec
- Routing applications of IPsec
- IPsec documents (Architecture, AH, ESP, IKE, Crypto algorithms)
- IPsec services
- Transport mode vs Tunnel mode

### 3. IP Security Policy
- Security Associations (SA) – three identifying parameters
- Security Association Database (SAD)
- Security Policy Database (SPD)
- IP Traffic Processing (outbound and inbound)

### 4. Encapsulating Security Payload (ESP)
- ESP services and overview
- ESP packet format
- Additional ESP components (IV/Nonce, TFC padding)
- Encryption/authentication algorithms
- Padding (reasons)
- Anti-Replay Service (sliding window)
- Transport Mode ESP vs Tunnel Mode ESP

### 5. Combining Security Associations
- Security Association bundle (transport adjacency, iterated tunneling)
- Authentication Plus Confidentiality
- Basic Combinations of SAs (Case 1–4)

### 6. Internet Key Exchange (IKE)
- Overview and key management (manual vs automated)
- ISAKMP/Oakley
- Key Determination Protocol (Diffie–Hellman)
- Features of IKE Key Determination
- IKEv2 Exchanges
- IKE Header Format
- IKE Payload Types

---

## Important Topics from Previous Question Papers

Based on three previous papers (June/July 2025 Make-Up, June/July 2025, Dec 2025/Jan 2026):

**Must prepare (asked in all/most papers):**
1. Transport Mode vs Tunnel Mode (general + ESP-specific) — **all 3 papers**
2. ESP Packet Format — **all 3 papers**
3. Combining Security Associations — **Papers 1 & 3**
4. IP Security Scenario / Overview + Benefits — **Papers 1 & 3**
5. IPsec Services + Security Associations — **Paper 3**

**Occasional:**
- DKIM Strategy (Paper 2)
- IKE Header & Payload Formats (Paper 2)
- Authentication Header (AH) with field functions (Paper 3 — needs supplementary notes; AH is deprecated in the Module 5 text)

---

# EXAM-READY ANSWERS

---

## TOPIC 1: DomainKeys Identified Mail (DKIM)

### Introduction
- DKIM is a technique used to **cryptographically sign email messages** so the receiver can verify the authenticity of the sender's domain.
- The signing domain attaches a **digital signature** to the email header.
- The receiver fetches the **public key** from the sender's **DNS records** to verify the signature.
- If the signature matches, the mail is confirmed as sent by a system holding the domain's **private key**.
- Defined as an Internet Standard in **RFC 6376**; used by Gmail, Yahoo!, ISPs, etc.
- **Purpose:** Prevent attackers from falsely claiming to send mail from a legitimate domain.

### DKIM Strategy (How it works)
- Works **quietly in the background** — no action needed from the end user.
- When a message is sent, the **administrative domain signs it using its private key**.
- The signature covers the **message body + selected RFC 5322 headers**, protecting both from tampering.
- At the receiver, the **Mail Delivery Agent (MDA)** fetches the public key from DNS and verifies the signature.
- If verification succeeds → mail is trusted. If a fake sender impersonates the domain → signature fails → mail is rejected.

### Why DKIM is better than S/MIME and PGP
- S/MIME and PGP use the **user's personal private key**; most users rarely use them.
- S/MIME protects only the **message body**, not important headers like the sender's address.
- DKIM works at the **server level (not the email client)** — needs no user effort.
- All mails from cooperating domains are **automatically signed**, strengthening trust.

### DKIM Functional Flow
- Processing divided between two entities:
  - **Signing ADMD** (Administrative Management Domain) — signs using private key from its Key Store.
  - **Verifying ADMD** — checks the signature using the public key from DNS.
- Signing can be done by MUA, MSA, or MTA; verification by MTA, MDA, or MUA.
- After verification, **reputation/signing-practice information** helps the filtering engine classify mail.

### DKIM Signature Header Fields (Tags)
The signature appears in the header beginning with **Dkim-Signature**.

| Tag | Meaning |
|-----|---------|
| **v** | DKIM version |
| **a** | Signature algorithm (e.g., rsa-sha256) |
| **c** | Canonicalization method |
| **d** | Signing domain (SDID) |
| **s** | Selector (allows multiple keys per domain) |
| **h** | List of signed header fields |
| **bh** | Hash of the canonicalized body |
| **b** | Actual signature in base64 |

### Canonicalization
- Done before signing to withstand small changes during transit (whitespace, line endings, header folding).
- Two algorithms:
  - **Simple** — expects no modifications at all.
  - **Relaxed** — allows common changes during transit.
- Default signing algorithm: **RSA with SHA-256**.

**Diagram tip:** Draw the DKIM deployment flow (MUA → MSA signs → MTA → DNS public key lookup → MDA verifies → MUA).

---

## TOPIC 2: Transport Mode and Tunnel Mode of IPsec

Both **AH** and **ESP** can operate in two modes — **Transport Mode** and **Tunnel Mode** — differing in how much of the IP packet they protect.

### Transport Mode
- Protects the **upper-layer protocols** — i.e., only the **IP payload** (TCP, UDP, ICMP).
- Mainly used for **end-to-end communication between two hosts**.
- In **IPv4**, payload is everything after the IP header.
- In **IPv6**, payload is everything after the IP header and extension headers.
- The **original IP header is NOT hidden** → traffic analysis still possible.

**Functions:**
- **ESP (transport mode):** Encrypts (and optionally authenticates) only the IP payload; IP header not encrypted.
- **AH (transport mode):** Authenticates the payload and selected fields of the IP header.

### Tunnel Mode
- Protects the **entire original IP packet**.
- The original packet (inner IP header + payload) is **encapsulated inside a new outer IP packet**.

**Procedure:**
1. AH/ESP fields are added to the original packet.
2. The whole packet becomes the **payload of a new IP packet with a new outer IP header**.
3. Routers see only the **outer header**, not the inner one.

**Advantages:**
- **Hides original source/destination addresses** (defends against traffic analysis).
- Allows multiple hosts behind a firewall to communicate securely **without running IPsec on each host**.
- Used when **at least one endpoint is a security gateway** (firewall/secure router) → basis for **VPNs**.

**Functions:**
- **ESP (tunnel mode):** Encrypts (and optionally authenticates) the entire inner IP packet.
- **AH (tunnel mode):** Authenticates the inner packet and parts of the outer header.

**Example (Tunnel Mode):** Host A → A's firewall encapsulates packet in a new IP packet → routed to B's firewall → B's firewall strips outer header → forwards inner packet to Host B.

### Comparison Table (Table 20.1)

| Feature | Transport Mode | Tunnel Mode |
|---|---|---|
| **Protects** | IP payload only | Entire inner IP packet |
| **AH** | Authenticates payload + selected header fields | Authenticates inner packet + parts of outer header |
| **ESP** | Encrypts IP payload | Encrypts entire inner packet |
| **Original IP header** | Visible | Hidden inside new outer header |
| **Used by** | Host-to-host (end-to-end) | Security gateway / VPN |
| **Traffic analysis** | Possible | Prevented |

**Diagram tip (Figure 20.8):**
- Transport mode: `[Orig IP hdr | ESP hdr | TCP | Data | ESP trlr | ESP auth]`
- Tunnel mode: `[New IP hdr | ESP hdr | Orig IP hdr | TCP | Data | ESP trlr | ESP auth]`

---

## TOPIC 3: ESP (Encapsulating Security Payload) Packet Format

### Introduction
- ESP provides **confidentiality, data origin authentication, connectionless integrity, anti-replay protection, and limited traffic flow confidentiality**.
- Supports modern **AEAD schemes like AES-GCM** (combine encryption + integrity in one operation).
- Current specification: **RFC 4303**.

### ESP Packet Format (Figure 20.5)
- **Security Parameters Index (SPI) – 32 bits:** Identifies the SA used to process the packet.
- **Sequence Number – 32 bits:** Monotonically increasing counter for **anti-replay protection**.
- **Payload Data – variable:** Transport-layer segment (transport mode) or entire IP packet (tunnel mode). **Encrypted**.
- **Padding (0–255 bytes):** For block-cipher alignment and to obscure payload length.
- **Pad Length – 8 bits:** Number of padding bytes.
- **Next Header – 8 bits:** Type of payload carried (TCP, UDP, etc.).
- **Integrity Check Value (ICV) – variable:** Integrity value over ESP header and encrypted portions (only when integrity is selected).

### Optional Fields (Figure 20.5b)
- **Initialization Vector (IV)/Nonce:** Ensures repeated plaintexts encrypt differently. Placed in encrypted region but itself **not encrypted**.
- **Traffic Flow Confidentiality (TFC) Padding:** Used only in **tunnel mode** to hide traffic patterns.

### Encryption and ICV — Key Points
- Encryption covers **Payload Data, Padding, Pad Length, and Next Header**.
- **ICV is always computed after encryption**, which:
  - Lets the receiver quickly reject replayed/malformed packets before decryption (reduces DoS).
  - Supports **parallel processing** (decryption + integrity check together).
- Since ICV is sent **unencrypted**, it must use a **keyed integrity algorithm** to prevent tampering.

### Reasons for Padding
1. To make plaintext an **exact multiple of the cipher's block size**.
2. To ensure **Pad Length and Next Header end on a 32-bit boundary**.
3. To **obscure the true payload size** → traffic-flow confidentiality.

**Diagram tip:** Show encryption coverage (Payload → Padding → Pad Length → Next Header) and ICV coverage (SPI → Sequence Number → entire encrypted block) with labelled arrows.

---

## TOPIC 4: Combining Security Associations

### Introduction
- A single SA can use **only one IPsec protocol at a time** — either **AH or ESP**, not both.
- Some traffic needs **authentication + confidentiality together**, or protection at **multiple points**.
- Multiple SAs are combined into a **Security Association Bundle**.
- Endpoints of individual SAs may be the **same or different**.

### Two Ways to Combine SAs
- **Transport Adjacency:** More than one security protocol applied to a single packet **without tunneling**. AH and ESP combined once; further nesting pointless.
- **Iterated Tunneling:** Multiple layers of IPsec protection through tunneling. Each layer can begin/end at a **different IPsec node**. Both methods can be mixed.

### Authentication Plus Confidentiality
The **order** of applying authentication and encryption matters.

**1. ESP with Authentication option:**
- ESP encrypts first, then an authentication field is appended.
- Transport mode → covers only IP payload; Tunnel mode → covers entire inner packet.
- Authentication covers **ciphertext, not plaintext**.

**2. Transport Adjacency (Authentication after Encryption):**
- Inner SA → **ESP (no auth):** encrypts only IP payload.
- Outer SA → **AH (transport):** authenticates ESP + original IP header.
- Advantage: covers source/destination IP addresses. Drawback: overhead of two SAs.

**3. Transport–Tunnel Bundle (Authentication before Encryption):**
- Inner SA → **AH (transport):** authenticates IP payload + header.
- Outer SA → **ESP (tunnel):** encrypts entire authenticated packet + adds new outer header.
- Used when authentication should apply to **plaintext**.

### Basic Combinations of SAs — Four Cases (Figure 20.10)

**Case 1: End-to-End Security Between Hosts**
- Both hosts implement IPsec and share secret keys.
- Combinations: AH (transport), ESP (transport), ESP inside AH, or any inside an outer tunnel SA.

**Case 2: Gateway-to-Gateway Security (VPN)**
- Hosts do **not** use IPsec; only gateways protect traffic.
- A **single tunnel-mode SA** is enough — basic **VPN model**.

**Case 3: Gateway Security + End-to-End Security**
- A gateway-to-gateway tunnel SA protects all traffic between networks.
- Hosts may **add end-to-end SAs** for extra security.

**Case 4: Remote Access Through Firewall**
- A remote host connects to the internal network using a **tunnel-mode SA with the firewall**.
- Like remote-access VPNs.

**Key rule:** Host-to-host SAs can use **transport or tunnel mode**, but **gateway SAs must use tunnel mode**.

---

## TOPIC 5: IP Security Scenario, Overview and Benefits

### Introduction
- In 1994, the **IAB** released **RFC 1636**, stressing the need to secure traffic using **authentication and encryption**.
- IAB recommended adding these into next-generation IP (IPv6); works with **both IPv4 and IPv6**.
- Implemented as a set of protocols called **IPsec**, operating at the **IP layer**.

### Applications of IPsec
- **Secure branch office connectivity** — VPNs over the Internet instead of costly private networks.
- **Secure remote access** — remote users connect via any ISP using IPsec.
- **Secure extranet/intranet connectivity** — secure links with partners and internal networks.
- **Strengthening electronic commerce** — adds encryption/authentication to e-commerce traffic.

### IP Security Scenario (Figure 20.1)
- Organization has **LANs at different sites**; inside each LAN normal IP traffic flows.
- When traffic goes offsite through a **public/private WAN**, IPsec protects it.
- **Routers/firewalls at the boundary** perform IPsec: encrypt outbound, decrypt inbound — **transparent** to internal systems.
- **Tunnel Mode** is used, relying on:
  - **ESP** for combined encryption and authentication.
  - A **key exchange mechanism**.
- VPN ensures: (1) only **authorized users** access it, (2) no **eavesdropper** can read data.
- Individual remote users can also connect by running IPsec on their devices.

### Benefits of IPsec
- On a **firewall/router**, protects all boundary traffic; internal traffic has no extra overhead.
- **Highly resistant to bypass** (if firewall is the only entry point).
- Works **below the transport layer (under TCP/UDP)** → **transparent to applications**.
- **Transparent to end users** — no cryptography training; no per-user key distribution.
- Can secure communication for **individual users** when needed.

**Diagram tip:** Draw Figure 20.1 — two sites with LANs, Ethernet switch, networking devices with IPsec at the boundary, public/private network, with legend for protected vs unprotected traffic and the virtual tunnel.

---

## TOPIC 6: IPsec Services and Security Associations

### IPsec Services
IPsec uses two main protocols:
- **Authentication Header (AH):** authentication and integrity.
- **Encapsulating Security Payload (ESP):** encryption and optional authentication.

As per **RFC 4301**, key services:
- **Access control** — restricts communication to authorized users/systems.
- **Connectionless integrity** — packets not altered in transit.
- **Data origin authentication** — confirms true source.
- **Anti-replay protection** — rejects duplicated/replayed packets using sequence numbers.
- **Confidentiality** — encrypts data.
- **Limited traffic flow confidentiality** — hides traffic patterns.

### Security Associations (SA)
- The **fundamental building block of IPsec**.
- A **one-way (simplex) logical connection** defining how traffic is protected.
- Two-way communication needs **two separate SAs**.

**Three identifying parameters:**
1. **Security Parameters Index (SPI)** — 32-bit value in AH/ESP header; meaning only to receiver.
2. **IP Destination Address** — endpoint receiving traffic (host, firewall, router).
3. **Security Protocol Identifier** — AH or ESP.

→ **Destination IP + SPI + Protocol** uniquely identifies the SA.

### Security Association Database (SAD)
Stores parameters to process packets for each SA. Entry includes:
- SPI, Sequence Number Counter, Sequence Counter Overflow Flag
- Anti-Replay Window
- AH Information / ESP Information (algorithms, keys, lifetimes)
- SA Lifetime, IPsec Protocol Mode (transport/tunnel/wildcard), Path MTU

### Security Policy Database (SPD)
Decides packet handling — **PROTECT, BYPASS, or DISCARD**. Uses **selectors**:
- Remote IP Address, Local IP Address
- Next Layer Protocol
- Name, Local and Remote Ports

### SPD vs SAD

| **SPD** | **SAD** |
|---|---|
| Decides **what action** (PROTECT/BYPASS/DISCARD) | Stores **how to apply** IPsec (keys, algorithms) |
| Uses **selectors** to match traffic | Uses **SPI + Dest IP + Protocol** to identify SA |

---

## BONUS TOPIC: Internet Key Exchange (IKE)

### Introduction
- IKE handles **key management** for IPsec — determines and distributes secret keys.
- Two applications typically need **four keys** (transmit + receive pair each for integrity and confidentiality).
- **Manual** key management: admin configures keys (small, static networks).
- **Automated** key management: on-demand key creation (large, dynamic networks).
- Early IPsec used **ISAKMP/Oakley**:
  - **Oakley** — key exchange based on **Diffie–Hellman**.
  - **ISAKMP** — framework and message formats.
- **IKEv2** drops the names Oakley/ISAKMP; simpler design, same purpose.

### Key Determination Protocol (Diffie–Hellman)
- IKE uses a refined **Diffie–Hellman** exchange; shared key is **never transmitted directly**.
- **Advantages:** keys generated only when needed; no prior infrastructure.
- **Weaknesses of pure D-H:**
  - No identity information.
  - Vulnerable to **man-in-the-middle attack**.
  - **Clogging attack** (forces expensive exponentiations).

### Features of IKE Key Determination (5 features)
- **Cookies** — prevent clogging attacks.
- **Group negotiation** — defines global D-H parameters.
- **Nonces** — protect against replay.
- **Secure D-H public key exchange.**
- **Authentication** of the entire exchange — prevents MITM.

### IKEv2 Exchanges
- **Initial Exchange Pair 1:** share algorithms, nonces, D-H values → creates **IKE SA**.
- **Initial Exchange Pair 2:** peers authenticate → establish first **IPsec SA** (4 messages total).
- **CREATE_CHILD_SA:** additional SAs, rekeying, key updates.
- **Informational Exchange:** management info, errors, delete notifications (no new SAs).

### IKE Header Format
- **Initiator SPI (64 bits)**, **Responder SPI (64 bits)**
- **Next Payload (8 bits)**, **Major/Minor Version (4 bits each)**
- **Exchange Type (8 bits)** — IKE_SA_INIT, IKE_AUTH, CREATE_CHILD_SA, INFORMATIONAL
- **Flags (8 bits)** — Initiator, Version, Response bits
- **Message ID (32 bits)**, **Length (32 bits)**

### IKE Payload Types
Each begins with a generic payload header (Next Payload, Payload Length, Critical Bit). Main types:
- **SA Payload** (Proposal, Transform, Attribute)
- **Key Exchange Payload**, **Identification Payload**
- **Certificate / Certificate Request Payload**
- **Authentication Payload**, **Nonce Payload**
- **Notify, Delete, Vendor ID, Traffic Selector, Encrypted, Configuration, EAP Payloads**

---

## QUICK REVISION CHECKLIST

| Topic | Key things to remember |
|---|---|
| DKIM | RFC 6376, signs body + RFC 5322 headers, public key from DNS, 8 tag fields, canonicalization (simple/relaxed) |
| Transport vs Tunnel | Transport = payload only (host-to-host); Tunnel = whole packet (gateway/VPN) |
| ESP Format | SPI, Seq No, Payload, Padding, Pad Length, Next Header, ICV; RFC 4303; AES-GCM |
| Combining SAs | Transport adjacency vs iterated tunneling; 4 cases; gateway SAs must use tunnel mode |
| IP Security Scenario | RFC 1636, IAB, Figure 20.1, firewall/router does IPsec, transparent |
| IPsec Services | Access control, integrity, data origin auth, anti-replay, confidentiality, limited TFC (RFC 4301) |
| SA | One-way; 3 params = SPI + Dest IP + Protocol; SAD vs SPD |
| IKE | Diffie–Hellman, cookies for clogging, 5 features, IKEv2 exchanges, header format |

---

*Prepared for VTU BCY602 – Cryptography and Network Security, Module 5.*
