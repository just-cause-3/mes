# BCY602 – Cryptography and Network Security
## Module 3 – Complete Exam Study Guide

**Module 3 syllabus:** Applications of Cryptographic Hash Functions, Two Simple Hash Functions, Key Management and Distribution (Symmetric key distribution using symmetric encryption, Symmetric key distribution using asymmetric encryption, Distribution of public keys), X.509 Certificates, Public Key Infrastructures. *(10 hours)*

---

## Table of Contents

1. [Topic & Subtopic Overview](#1-topic--subtopic-overview)
2. [Topic 1 – Applications of Cryptographic Hash Functions](#2-topic-1--applications-of-cryptographic-hash-functions)
3. [Topic 2 – Two Simple Hash Functions](#3-topic-2--two-simple-hash-functions)
4. [Topic 3 – Symmetric Key Distribution Using Symmetric Encryption](#4-topic-3--symmetric-key-distribution-using-symmetric-encryption)
5. [Topic 4 – Symmetric Key Distribution Using Asymmetric Encryption](#5-topic-4--symmetric-key-distribution-using-asymmetric-encryption)
6. [Topic 5 – Distribution of Public Keys](#6-topic-5--distribution-of-public-keys)
7. [Topic 6 – X.509 Certificates](#7-topic-6--x509-certificates)
8. [Topic 7 – Public-Key Infrastructure (PKI)](#8-topic-7--public-key-infrastructure-pki)
9. [Previous Year Question Paper Analysis](#9-previous-year-question-paper-analysis)
10. [Focused Exam Answers](#10-focused-exam-answers)

---

## 1. Topic & Subtopic Overview

**1. Applications of Cryptographic Hash Functions**
- Message Authentication (message digest, sender/receiver verification, why the hash must be protected, man-in-the-middle attack)
- Methods of using hash codes for authentication (the four methods a–d, and why "encrypt only the hash" is often preferred)
- Reasons to avoid full encryption (speed, hardware cost, initialization overhead, patents)
- Message Authentication Code (MAC)
- Digital Signatures
- Other Applications — Password Protection, Intrusion and Virus Detection

**2. Two Simple Hash Functions**
- Insecure Hash Functions (n-bit block processing)
- Simple XOR Hash Function
- Rotated XOR (RXOR) Hash Function
- Weakness of XOR and RXOR

**3. Symmetric Key Distribution Using Symmetric Encryption**
- The four ways to distribute keys
- Key Distribution Centre (KDC) — session key, master key
- Key Distribution Scenario (5-step protocol)
- Major Issues with KDC: Hierarchical Key Control, Session Key Lifetime, Transparent Key Control (SSM), Decentralized Key Control
- Controlling Key Usage: key types, 8-bit tag, Control Vector

**4. Symmetric Key Distribution Using Asymmetric Encryption**
- Simple Secret Key Distribution
- Secret Key Distribution with Confidentiality and Authentication
- A Hybrid Scheme

**5. Distribution of Public Keys**
- Public Announcement
- Publicly Available Directory
- Public-Key Authority
- Public-Key Certificates

**6. X.509 Certificates**
- Overview, certificate structure/fields, notation
- Obtaining a Certificate, CA Hierarchy
- Certificate Revocation (CRL)
- X.509 Version 3 — Certificate Extensions

**7. Public-Key Infrastructure (PKI)**
- Definition and objective
- PKIX Architectural Model components
- PKIX Management Functions
- PKIX Management Protocols (CMP, CMC)

---

## 2. Topic 1 – Applications of Cryptographic Hash Functions

A cryptographic hash function takes a message of any length and produces a fixed-size output called the **message digest**. It is one of the most versatile algorithms, providing **data integrity, authentication**, and supporting digital signatures and password protection.

### 1. Message Authentication

It verifies that a message has not been modified, inserted, deleted, or replayed, and confirms the sender's identity.

- The sender computes a hash of the message and sends both the message and its hash.
- The receiver recomputes the hash and compares it with the received hash.
- If they **match → message is authentic**; if they **mismatch → message was altered**.
- The hash value must be protected, otherwise an attacker can alter the message and recompute a new hash (man-in-the-middle attack).

**Methods of using hash codes for authentication:**

- **(a)** Encrypt **(Message + Hash)** using symmetric encryption → authentication **and** confidentiality.
- **(b)** Encrypt **only the hash code** → authentication with less computation (preferred when confidentiality not needed).
- **(c)** Use a **shared secret value S** without encryption → sender sends M with H(M‖S); receiver recomputes using the same S. The secret S is never transmitted.
- **(d)** Encrypt (Message + Hash) for **added confidentiality**.

*Method (b) is often preferred because encrypting the whole message is computationally expensive.*

### 2. Message Authentication Code (MAC)

- A MAC is a **keyed hash function** used when two parties share a secret key K.
- Function: **MAC = F(K, M)**
- Verifies both **integrity** and **authenticity**.
- An attacker cannot alter the message or MAC without knowing K.
- More efficient than full encryption algorithms.

### 3. Digital Signatures

- Similar to MAC but uses **public-key encryption**.
- The hash of the message is **encrypted with the sender's private key**.
- Anyone with the sender's **public key** can verify it.
- Provides **integrity, authentication, and non-repudiation**.
- For confidentiality, the message + signature can be encrypted with a symmetric key.

### 4. Other Applications

- **Password Protection** – Systems store the *hash* of passwords instead of actual passwords. At login, the entered password's hash is compared with the stored hash.
- **Intrusion and Virus Detection** – Store a secure hash H(F) of each file. Later, recompute and compare to detect modification; an intruder cannot change a file without changing its hash.

> **Exam tip:** Draw the message authentication block diagram (sender computes H → receiver recomputes H → compare) for easy marks.

---

## 3. Topic 2 – Two Simple Hash Functions

All hash functions process the input data as a sequence of **n-bit blocks** and produce an **n-bit hash value**. Two simple but **insecure** hash functions are the **XOR** and **Rotated XOR (RXOR)** methods.

### 1. Simple XOR Hash Function

Each bit of the hash value is the **exclusive-OR (XOR)** of the corresponding bits from all message blocks.

```
C_i = b_i1 ⊕ b_i2 ⊕ ... ⊕ b_im
```

where:
- C_i = i-th bit of the hash code
- b_ij = i-th bit of the j-th block
- m = number of blocks

- This gives a **longitudinal redundancy check (LRC)**.
- **Effective for random data** — error detection probability ≈ 2⁻ⁿ.
- **Weak for structured data** (e.g., text files where high-order bits are often 0).

### 2. Rotated XOR (RXOR) Hash Function

A **one-bit left rotation** of the hash value is performed after processing each block.

Procedure:
1. Initialize the n-bit hash to **0**.
2. For each n-bit block:
   - (a) Rotate the current hash left by **1 bit**.
   - (b) XOR the block into the hash.

- Provides **better data integrity** than simple XOR.
- But is **still not secure** for cryptographic use.

### 3. Weakness of XOR and RXOR

- When **only the hash is encrypted**, an attacker can create a new message with the same hash by adding one extra block.
- Even if the **entire message + hash is encrypted** (e.g., CBC mode), security flaws remain.
- For 64-bit blocks X₁, X₂, …, X_N:

```
h = X1 ⊕ X2 ⊕ ... ⊕ XN
```

In CBC mode: X₁ = IV ⊕ D(K, Y₁), X_i = Y_(i-1) ⊕ D(K, Y_i).

- Since **XOR is commutative**, **permuting (rearranging) the ciphertext blocks does not change the resulting hash**.
- Hence, **altered ciphertext can still appear valid** — making both methods cryptographically insecure.

**Conclusion:** Both XOR and RXOR are useful only for **error detection**, not for security.

> **Exam tip:** Write the C_i formula, the RXOR procedure, and the commutativity weakness — the marks-fetching points.

---

## 4. Topic 3 – Symmetric Key Distribution Using Symmetric Encryption

For symmetric encryption to work, both parties must **share the same secret key**, protected from others. **Key distribution** means delivering a key to two parties securely.

### Ways to Distribute Keys (for parties A and B)

1. A selects a key and **physically delivers** it to B.
2. A **third party** selects the key and physically delivers it to A and B.
3. If A and B have **recently used a key**, one party sends the new key encrypted with the old key.
4. If A and B each has an **encrypted connection to a third party C**, C delivers the key over these secure links.

*Methods 1 & 2 are simplest but do not scale. For large networks, method 4 (trusted intermediary) is the practical solution.*

### Key Distribution Centre (KDC)

- Based on a **hierarchy of keys** (at least two levels).
- **Session key** – temporary key used for one logical connection, then discarded.
- **Master key** – shared between the KDC and each user, used to encrypt session keys.

### Key Distribution Scenario (A wants to talk to B)

A shares master key Ka with KDC; B shares master key Kb with KDC.

1. **A → KDC:** Request for a session key, including IDA, IDB, and a **nonce N₁** (timestamp/counter/random number to prevent replay).
2. **KDC → A:** Message encrypted with **Ka** containing:
   - The **session key Ks**
   - The **original request + N₁**
   - Plus, encrypted with **Kb**: Ks and IDA (to forward to B)
3. **A → B:** Forwards E(Kb, [Ks ‖ IDA]). B now knows Ks, knows it is talking to A, and knows it came from the KDC.
4. **B → A:** Sends a new nonce **N₂**, encrypted with Ks.
5. **A → B:** Sends **f(N₂)** (e.g., N₂ + 1) using Ks.

*Steps 1–3 do the actual key distribution; steps 3, 4, 5 perform authentication.*

### Major Issues with KDC

**(a) Hierarchical Key Control** – For large networks, use **local KDCs** per domain and a **global KDC** to connect domains. Minimizes master key distribution effort.

**(b) Session Key Lifetime** – Shorter lifetime = more secure but more overhead. Connection-oriented: one key per connection. Connectionless: key for a fixed time or number of transactions.

**(c) Transparent Key Control Scheme** – Uses a **Session Security Module (SSM)** that performs end-to-end encryption and obtains session keys for the host (assumes TCP).

**(d) Decentralized Key Control** – Avoids reliance on a trusted KDC. Requires up to **n(n−1)/2 master keys** for n end systems. Practical only for small networks.

### Controlling Key Usage

Session keys can be defined by use: **data-encrypting key, PIN-encrypting key, file-encrypting key**.

- **8-bit key tag (DES):** Uses the 8 spare parity bits to indicate key type and encrypt/decrypt permission. *Drawbacks:* only 8 bits, and usable only at the decryption point.
- **Control Vector:** A field-based vector specifying key usage, coupled with the key at the KDC:
  - H = h(CV)
  - Key input = Km ⊕ H
  - Ciphertext = E([Km ⊕ H], Ks)
  - *Advantages:* no length restriction (complex controls), available in clear form at all stages.

> **Exam tip:** The Key Distribution Scenario diagram (5 numbered messages, labelling N₁, N₂, Ka, Kb, Ks) is the most important part.

---

## 5. Topic 4 – Symmetric Key Distribution Using Asymmetric Encryption

Public-key encryption can be used to **securely distribute secret (session) keys** for conventional symmetric encryption.

### 1. Simple Secret Key Distribution

1. **A generates** a key pair {PUₐ, PRₐ} and sends **PUₐ ‖ IDA** to B.
2. **B generates** a secret key Ks and sends **E(PUₐ, Ks)** to A.
3. **A recovers** the key: **D(PRₐ, E(PUₐ, Ks))**. Only A can decrypt it.
4. **A discards** PUₐ, PRₐ; B discards PUₐ.

**Drawback:** Simple and gives confidentiality, but vulnerable to a **man-in-the-middle attack** — a third party can substitute his own public key.

### 2. Secret Key Distribution with Confidentiality and Authentication

(Assumes A and B already have each other's public keys.)

1. **A → B:** E(PUᵦ, [N₁ ‖ IDA])
2. **B → A:** E(PUₐ, [N₁ ‖ N₂]) — returning N₁ assures A it is B.
3. **A → B:** E(PUᵦ, N₂) — assures B it is A.
4. **A → B:** M = **E(PUᵦ, E(PRₐ, Ks))**
   - B's public key → only B can read (**confidentiality**).
   - A's private key → only A could have sent (**authentication**).
5. **B recovers** the key: **D(PUₐ, D(PRᵦ, M))**.

### 3. A Hybrid Scheme

- Retains a **KDC** sharing a **master key** with each user and distributing **session keys** encrypted with the master key.
- A **public-key scheme** distributes the **master keys**.
- Provides a **secure, efficient** way of distributing master keys.

> **Exam tip:** The second scheme is most important — draw the 4-step diagram and explain why step 4 gives both confidentiality and authentication. Mention the man-in-the-middle weakness of the simple scheme.

---

## 6. Topic 5 – Distribution of Public Keys

Four categories:

### 1. Public Announcement
- Any user **broadcasts** their public key (e.g., PGP keys on email).
- **Advantage:** Simple and convenient.
- **Major weakness — Forgery:** Anyone can broadcast a fake key pretending to be A, and **masquerade as A** until discovered.

### 2. Publicly Available Directory
A **trusted public directory** with these properties:
- Maintains a **{name, public key}** entry for each participant.
- Each participant **registers** a public key.
- Keys can be **replaced** at any time.
- Accessed electronically via secure, authenticated communication.

**Vulnerability:** If an adversary obtains the **directory authority's private key**, they can distribute counterfeit keys and impersonate participants.

### 3. Public-Key Authority
Provides **tighter control**; users must know the authority's public key and interact in **real-time**. **Seven messages** are exchanged.

1. **A → Authority:** Timestamped request for B's public key.
2. **Authority → A:** Encrypted with **PRauth**, containing B's public key (PUᵦ), the original request, and the timestamp.
3. **A → B:** E(PUᵦ, [IDA ‖ N₁]).
4. **B → Authority:** Requests A's public key.
5. **Authority → B:** Sends A's public key (PUₐ) with timestamp, encrypted with PRauth.
6. **B → A:** E(PUₐ, [N₁ ‖ N₂]) — assures A it is B.
7. **A → B:** E(PUᵦ, N₂) — assures B it is A.

**Drawback:** A must contact the authority for **every** new user — a **bottleneck**.

### 4. Public-Key Certificates
- A **certificate** binds a user's **identity to their public key**, **signed by a trusted CA**.
- Anyone with the CA's public key can **verify** it — no need to contact the authority each time.

**Requirements:**
1. Any participant can read the owner's name and public key.
2. Any participant can verify the certificate is genuine.
3. Only the CA can create/update certificates.
4. Any participant can verify the certificate's currency (validity).

*The universally accepted format is the **X.509** standard.*

> **Exam tip:** Draw the Public-Key Authority (7 messages) diagram. One-line weaknesses: announcement → forgery, directory → authority's private key, authority → bottleneck.

---

## 7. Topic 6 – X.509 Certificates

**X.509** is part of the X.500 series and defines the standard format for **public-key certificates**. Each certificate contains a user's public key signed by a trusted **Certification Authority (CA)** using the CA's private key. Based on **public-key cryptography and digital signatures**; used in **S/MIME, IP Security, SSL/TLS, and SET**. First issued **1988**, revised 1993, version 3 in 1995/2000.

### Fields of an X.509 Certificate

- **Version:** Format version (v1 default; v2 if unique identifiers present; v3 if extensions present).
- **Serial Number:** Unique integer within the issuing CA.
- **Signature Algorithm Identifier:** Algorithm (with parameters) used to sign the certificate.
- **Issuer Name:** X.500 name of the CA that created and signed it.
- **Period of Validity:** "not before" and "not after" dates.
- **Subject Name:** Name of the user who owns the certificate.
- **Subject's Public-Key Information:** Subject's public key + algorithm identifier.
- **Issuer Unique Identifier:** (Optional) Identifies the CA if its name is reused.
- **Subject Unique Identifier:** (Optional) Identifies the subject if its name is reused.
- **Extensions:** (Added in v3) One or more extension fields.
- **Signature:** Hash code of all other fields, **encrypted with the CA's private key**.

**Field order memory aid:** Version → Serial number → Signature algorithm → Issuer → Period of validity → Subject → Subject public key → Unique identifiers → Extensions → Signature.

**Version logic:** v1 = base fields, v2 adds the two unique identifier fields, v3 adds extensions, and the signature field is present in all versions.

### Certificate Notation

```
CA<<A>> = CA{V, SN, AI, CA, UCA, A, UA, Ap, TA}
```

where Y<<X>> = certificate of user X issued by CA Y; V = version, SN = serial number, AI = signing algorithm, CA = name of authority, UCA = unique ID of CA, A = name of user A, UA = unique ID of A, Ap = public key of A, TA = validity period.

### Obtaining a Certificate
- Any user with the **CA's public key** can verify the certified user public key.
- Only the **CA** can modify a certificate, so they are **unforgeable** and can be placed in a public directory.

### CA Hierarchy
- Same CA → parties verify each other's certificates directly.
- Different CAs → a **chain of certifications** is needed; X.509 arranges CAs in a **hierarchy**.
- Directory stores **Forward certificates** (of X by other CAs) and **Reverse certificates** (by X for other CAs).
- Example chain: A acquires B's certificate via X<<W>>W<<V>>V<<Y>>Y<<Z>>Z<<B>>.

### Certificate Revocation
Reasons:
1. The user's **private key is compromised**.
2. The user is **no longer certified** by this CA.
3. The **CA's certificate is compromised**.

Each CA maintains a **Certificate Revocation List (CRL)** — revoked but not expired certificates, **signed by the issuer**, including issuer name, creation date, next-update date, and (serial number + revocation date) for each revoked certificate.

### X.509 Version 3 — Certificate Extensions
Three categories:
1. **Key and policy information** – about subject/issuer keys and certificate policy.
2. **Subject and issuer attributes** – alternative names/formats (email, postal address).
3. **Certification path constraints** – restrict the types of certificates a subject CA can issue.

> **Exam tip:** The X.509 certificate format diagram is the highest-priority diagram in Module 3 (appears in all papers). Always write the certificate notation and the version logic.

---

## 8. Topic 7 – Public-Key Infrastructure (PKI)

**Definition:** PKI is the set of **hardware, software, people, policies, and procedures** needed to **create, manage, store, distribute, and revoke digital certificates** based on asymmetric cryptography.

**Objective:** Enable the **secure, convenient, and efficient acquisition of public keys**.

### PKIX Architectural Model — Components
- **End Entity:** End users, devices, or any entity in the subject field of a certificate.
- **Certification Authority (CA):** Issuer of certificates and CRLs.
- **Registration Authority (RA):** *Optional* — takes over some administrative functions from the CA.
- **CRL Issuer:** *Optional* — a CA can delegate publishing of CRLs.
- **Repository:** Any method of storing certificates and CRLs for retrieval.

### PKIX Management Functions
- **Registration:** User makes itself known to a CA; begins enrollment with mutual authentication.
- **Initialization:** Installing key materials in the client (e.g., the trusted CA's public key).
- **Certification:** CA issues a certificate for the user's public key and posts it in a repository.
- **Key Pair Recovery:** Restore an encryption/decryption key pair from a backup facility.
- **Key Pair Update:** Key pairs replaced regularly; new certificates issued on expiry or revocation.
- **Revocation Request:** Authorized person notifies the CA of a situation needing revocation.
- **Cross Certification:** A cross-certificate issued by one CA to another CA.

### PKIX Management Protocols
1. **Certificate Management Protocol (CMP):** Each function identified by specific protocol exchanges; flexible.
2. **Certificate Management Messages over CMS (CMC):** Built on earlier work; used for obtaining X.509 certificates in a PKI.

> **Exam tip:** Draw the PKIX Architectural Model diagram showing End Entity, CA, RA, CRL Issuer, and Repository.

---

## 9. Previous Year Question Paper Analysis

Based on three BCY602 papers (June/July 2025, Make-Up June/July 2025, Dec 2025/Jan 2026), the Module 3 topics asked were:

| Topic | Papers asked | Priority |
|-------|--------------|----------|
| X.509 Certificates (format with diagram) | All 3 (10m, 10m, 6m) | **Highest** |
| Distribution of Public Keys (four categories) | 2 of 3 (10m, 12m) | High |
| Symmetric Key Distribution / KDC scenario | 2 of 3 (10m, 8m) | High |
| Message Authentication (hash code methods) | 1 (10m) | Medium |
| Hash Function definition + properties | 1 (8m) | Medium |
| Two Simple Hash Functions | 1 (6m) | Medium |
| Digital Signature | 1 (6m) | Medium |
| PKIX Architectural Model (block diagram) | 1 (10m) | Medium |
| Symmetric Key Distribution (Asymmetric) | 1 (8m) | Medium |
| Control Vector (encryption/decryption) | 1 (6m) | Medium |
| Hierarchical Key Control + Session Key Lifetime | 1 (6m) | Medium |

**Strategy:**
- The four "big" 10-mark rotating topics are **X.509 certificates, Distribution of public keys, KDC (symmetric distribution), and PKIX model** — these cover most Module 3 marks.
- Smaller 6-mark topics (Two simple hash functions, Digital signature, Control vector, Hierarchical key control/Session key lifetime) usually appear as combined "Discuss the following: i)… ii)…" questions — revise as short notes.
- Note: In the Dec 2025/Jan 2026 paper, module numbering is shuffled (Module 3 there is tagged CO4), but the question *content* is still Module 3 material.
- Every Module 3 topic has appeared at least once — be solid on all, with extra confidence on X.509.

---

## 10. Focused Exam Answers

### Q. "With neat diagrams describe the ways a hash code can be used to provide message authentication" (10 marks)

Structure the answer around the **four methods** (Figure 11.3):

**Method (a): Encrypt (Message + Hash) with symmetric encryption**
- Sends: **E(K, [M ‖ H(M)])**
- Provides **authentication and confidentiality**.

**Method (b): Encrypt only the hash code**
- Sends: **M ‖ E(K, H(M))**
- Provides **authentication only**; **preferred** (less computation).

**Method (c): Use a shared secret value S (no encryption)**
- Sends: **M ‖ H(M ‖ S)**
- Secret S never transmitted; provides **authentication only**.

**Method (d): Encrypt [M ‖ H(M ‖ S)] for added confidentiality**
- Sends: **E(K, [M ‖ H(M ‖ S)])**
- Provides **authentication + confidentiality**.

**Diagrams to draw:** For each method, draw Source A (left) and Destination B (right) with the message M, hash block H, encrypt/decrypt blocks E/D, and a "Compare" box at the receiver. Label the transmitted quantity in the middle:
- (a) → E(K, [M ‖ H(M)])
- (b) → M, E(K, H(M))
- (c) → M, H(M ‖ S)
- (d) → E(K, [M ‖ H(M ‖ S)])

---

### Q. "Explain the X.509 certificate format with a neat diagram" (10 marks)

See [Topic 6](#7-topic-6--x509-certificates) for the full field list, notation, and version logic.

**Diagram — X.509 certificate format (draw the fields stacked top to bottom):**

```
+------------------------------------------+
|              Version                     |  ┐
+------------------------------------------+  |
|        Certificate serial number         |  |
+------------------------------------------+  |
|      Signature algorithm identifier      |  |
+------------------------------------------+  | Version 1
|              Issuer name                 |  |
+------------------------------------------+  |
|           Period of validity             |  |
+------------------------------------------+  |
|             Subject name                 |  |
+------------------------------------------+  |
|        Subject's public-key info         |  ┘
+------------------------------------------+  ┐
|         Issuer unique identifier         |  | Version 2
+------------------------------------------+  |
|        Subject unique identifier         |  ┘
+------------------------------------------+  ┐
|              Extensions                  |  | Version 3
+------------------------------------------+  ┘
|              Signature                   |  (all versions)
+------------------------------------------+
```

- **Version 1:** Version through Subject's public-key info.
- **Version 2:** Adds Issuer + Subject unique identifiers.
- **Version 3:** Adds Extensions.
- **Signature:** Present in all versions (hash of all fields, encrypted with the CA's private key).

---

*End of Module 3 study guide — BCY602 Cryptography and Network Security.*
