# Module 4 — Cloud Security
### Combined Answers (June/July, Makeup & Model Papers)

> Answered in the sequence given. Where a topic repeats across papers, the answer is restated in its position for completeness.

---

# Q7 — Questions (All Papers)

---

## June/July Paper — Q7

### 1. What is a trusted hypervisor? Explain the security challenges faced by mobile devices.

**Trusted Hypervisor**

The hypervisor (Virtual Machine Monitor, VMM) is the single most security-critical component of a cloud, because it sits below every guest VM and controls all access to the physical hardware. A **trusted hypervisor** is a VMM designed to act as the **Trusted Computing Base (TCB)** of the system — i.e., the small, verified, tamper-resistant core that all security guarantees depend on.

Key ideas:

- It enforces **strong isolation** between co-resident VMs so that one tenant cannot read or alter another tenant's memory, CPU state, or storage.
- It keeps the **trusted code base small**, so it can be verified and is harder to attack. A large management OS (e.g., Dom0 in Xen) inflates the TCB and is a liability.
- **Terra** is the classic example of a trusted VMM. Terra can run an application either in an ordinary **open-box VM** (a normal, general-purpose VM) or in a **closed-box VM** — a sealed, tamper-proof container whose contents cannot be inspected or modified, even by the platform owner.
- A trusted hypervisor supports **attestation** (proving to a remote party what software is actually running) and **sealed storage** (data only released to the correct software state).

In short, a trusted hypervisor provides isolation + a minimal verifiable TCB + attestation, giving tenants confidence that the virtualization layer itself is not compromised.

**Security Challenges Faced by Mobile Devices**

Mobile devices increasingly act as clients to cloud services, but they introduce specific security weaknesses:

1. **Limited resources** — restricted CPU, memory, and battery mean strong cryptography and heavy security software are hard to run locally, forcing computation to be offloaded to the cloud.
2. **Loss or theft** — devices are small and portable, so physical loss directly exposes locally stored data and cached credentials.
3. **Weak / unattended authentication** — short PINs or stored "remember me" tokens make impersonation easy if the device is captured.
4. **Insecure communication channels** — data travels over public Wi-Fi and cellular links and can be intercepted (man-in-the-middle) if not properly encrypted.
5. **Malware and malicious apps** — third-party apps may exfiltrate data or escalate privileges.
6. **Data residency on the device** — sensitive data cached for offline use can be recovered from the device.
7. **Untrusted environment for offloading** — when work is offloaded to the cloud, the device must trust the remote infrastructure, raising confidentiality and integrity concerns.

---

### 2. Discuss the security risks posed by shared images and management OS.

**Risks posed by Shared Images**

In IaaS clouds, users commonly start a VM from a pre-built image (e.g., an Amazon Machine Image, AMI) shared by the provider or by other users. Studies that audited thousands of public AMIs found that shared images are a major source of vulnerabilities:

- **Backdoors and leftover credentials** — images sometimes retain the creator's SSH public keys, allowing the original publisher to log into every VM launched from that image.
- **Malware** — an image may contain trojans, rootkits, or other malicious software.
- **Unsolicited connections** — software in the image may "phone home" or open outbound connections.
- **Recovery of leftover private data** — deleted files, private keys, browser history, shell histories, and database credentials left behind by the creator can often be recovered, leaking the publisher's own secrets.
- **Outdated / unpatched software** — old images carry known, exploitable vulnerabilities.

The root cause is that the user **inherits an environment configured by someone else** and implicitly trusts it.

**Risks posed by the Management OS**

In a Xen-style platform, the privileged management OS (**Dom0**) creates, configures, migrates, and destroys guest VMs and hosts the device drivers. This makes it powerful — and dangerous:

- **Huge attack surface** — the management OS is a full commodity OS (millions of lines of code, drivers, libraries), so it has many exploitable bugs.
- **Single point of compromise** — if the management OS is breached, the attacker controls **all** guest VMs on that host: their memory, disks, and network traffic.
- **Excess privilege** — all management functions run in one monolithic, highly privileged domain rather than being split by least privilege.
- **No fault isolation** — a crash or compromise in any driver/component can bring down the whole node.

(Systems such as **Xoar** address this by disaggregating Dom0 into small, single-purpose, least-privilege components.)

---

### 3. Describe the hidden risks in cloud computing.

Beyond obvious technical threats, cloud computing carries **hidden / non-obvious risks** that arise from its very nature (outsourcing, multi-tenancy, opacity):

1. **Loss of control over data** — data is stored and processed on infrastructure the user does not own or administer; the user must trust the provider's controls.
2. **Multi-tenancy / shared resources** — many customers share the same physical hardware; weak isolation can leak data across tenants (e.g., side-channel and cross-VM attacks).
3. **Lack of transparency** — the customer cannot see exactly how, where, or by whom their data is handled, making auditing and assurance difficult.
4. **Data location and jurisdiction** — data may be replicated across data centers in different countries, subjecting it to foreign laws and complicating legal compliance.
5. **Vendor lock-in** — proprietary APIs and data formats make it hard and costly to move to another provider or back in-house.
6. **Malicious insiders** — privileged provider staff have broad access and are difficult for the customer to monitor.
7. **Availability dependence** — outages, DDoS, or the provider going out of business can make data and services unreachable; a single point of failure.
8. **Compliance and audit gaps** — meeting regulatory requirements (privacy, retention, breach notification) is harder when data control is shared.
9. **Reputation fate-sharing** — a co-tenant's malicious activity (spam, attacks) can get a shared IP/range blacklisted, hurting innocent tenants.

---

## Makeup Paper — Q7

### 1. Explain cloud security risks and discuss types of attacks in a cloud computing environment.

**Cloud Security Risks**

Cloud security risks fall into three broad categories:

1. **Traditional security threats** — the same threats faced by any Internet-connected system (network attacks, malware, phishing, SQL injection, cross-site scripting), but **amplified** because the cloud's shared, large-scale, multi-tenant infrastructure presents a much bigger attack surface and a richer target.
2. **Threats to availability of cloud services** — the customer becomes fully dependent on the provider. Risks include system failures, power outages, distributed denial-of-service (DDoS), and the provider ceasing operations. The cloud can become a **single point of failure**.
3. **Threats from third-party control of data** — lack of transparency, vendor lock-in, inability to fully audit the provider, malicious insiders, and data spread across multiple jurisdictions. The user loses direct control over confidentiality and integrity.

**Types of Attacks in a Cloud Environment**

Attacks can be framed around the **three actors** (user, service, cloud infrastructure) and also by common technique:

- **DDoS attacks** — flooding a service to exhaust resources and deny availability.
- **Cross-VM / side-channel attacks** — a malicious co-tenant infers data (keys, activity) from shared CPU cache, timing, or other side channels.
- **SQL injection / cross-site scripting** — exploiting web-facing service interfaces.
- **Privilege escalation and buffer overflow** — gaining higher rights than authorized.
- **Phishing and SSL/certificate spoofing** — tricking users into revealing credentials or trusting a fake service.
- **Malicious insider attacks** — abuse of privileged access by provider staff.
- **Authentication / account hijacking** — stealing tokens or credentials to impersonate a tenant.
- **Attacks via shared images** — malware or backdoors inherited from a shared VM image.

These are described more formally as the **six surfaces of attack** between the three actors (see Model Paper Q7.1).

---

### 2. Explain Privacy Impact Assessment (PIA) in cloud computing.

A **Privacy Impact Assessment (PIA)** is a systematic, **proactive** process used to identify and evaluate the privacy risks of a system or application **before** it is deployed, and to recommend controls that reduce those risks. In the cloud context, a PIA tool itself can be offered as a service.

**Proposed PIA tool (as discussed in the module):**

- It is delivered as a **SaaS application hosted by a trusted third party**, so organizations can run assessments without building their own tooling.
- The user supplies information about the project by completing a **structured questionnaire** describing the data collected, its purpose, flows, storage, and retention.
- The tool maintains a **Knowledge Base (KB)** built from relevant privacy legislation, regulations, and best practices.
- An **inference / rule-based engine** processes the questionnaire answers against the KB.
- It produces a **PIA report** that highlights privacy risks, flags non-compliance, and recommends mitigation (templates and decision support).

**Benefits:** privacy risks are caught **early** (cheaper to fix), compliance with privacy law is improved, accountability is documented, and stakeholders gain confidence that personal data is handled responsibly. The expert privacy knowledge is centralized in the KB and reused across many assessments.

---

## Model Paper — Q7

### 1. Demonstrate surfaces of attacks in a cloud computing environment with a neat diagram.

There are **three actors** in a cloud environment — the **User**, the **Service** (the application/service requested), and the **Cloud** infrastructure. Each pair of actors can attack each other in **both directions**, giving **six surfaces (vectors) of attack**.

```
                 user-to-service  /  service-to-user
        +-----------+ <-------------------------------> +-----------+
        |           |                                   |           |
        |   USER    |                                   |  SERVICE  |
        |           |                                   |           |
        +-----------+                                   +-----------+
              ^   \                                       /   ^
              |    \  user-to-cloud                      /    |
              |     \   cloud-to-user      service-to-cloud   |
              |      \                       cloud-to-service |
              v       \                          /           v
            +-------------------------------------------------+
            |                    CLOUD                         |
            |              (infrastructure / VMM)              |
            +-------------------------------------------------+
```

**The six surfaces of attack:**

1. **Service → User** — the service attacks the user (e.g., SSL/certificate spoofing, attacks on browser caches, phishing).
2. **User → Service** — the user attacks the service (e.g., buffer overflow, SQL injection, privilege escalation).
3. **Cloud → Service** — the cloud (a compromised/ malicious provider or VMM) attacks the hosted service (e.g., reading/altering the service's memory or data).
4. **Service → Cloud** — a malicious service attacks the cloud infrastructure (e.g., resource exhaustion, attempts to break out of its VM).
5. **Cloud → User** — the cloud attacks the user (e.g., misusing user data, DoS on the user's access).
6. **User → Cloud** — the user attacks the cloud (e.g., DDoS, abuse of resources, attempts to subvert the infrastructure).

Defending the cloud therefore means securing **all six** of these directional surfaces, not just the user-facing interface.

---

### 2. List out the top cloud security threats of CSA 2016.

The Cloud Security Alliance (CSA) published the **"Treacherous 12" — Cloud Computing Top Threats in 2016**:

1. **Data Breaches**
2. **Weak Identity, Credential, and Access Management**
3. **Insecure Interfaces and APIs**
4. **System and Application Vulnerabilities**
5. **Account Hijacking**
6. **Malicious Insiders**
7. **Advanced Persistent Threats (APTs)**
8. **Data Loss**
9. **Insufficient Due Diligence**
10. **Abuse and Nefarious Use of Cloud Services**
11. **Denial of Service (DoS / DDoS)**
12. **Shared Technology Vulnerabilities**

---

### 3. Select four widely accepted Fair Information Practices.

The widely accepted **Fair Information Practices (FIPs)** govern how organizations should handle personal data. Four of them are:

1. **Notice / Awareness** — individuals must be informed that their personal data is being collected, and for what purpose, *before* collection.
2. **Choice / Consent** — individuals must be given a choice over how their data is used, especially for purposes beyond the original collection (opt-in/opt-out).
3. **Access / Participation** — individuals should be able to access their data and check, correct, or contest its accuracy and completeness.
4. **Integrity / Security** — data must be accurate and protected by reasonable security safeguards against loss, misuse, or unauthorized access.

*(A fifth principle, **Enforcement / Redress**, provides mechanisms to ensure compliance and remedy violations.)*

---

# Q8 — Questions (All Papers)

---

## June/July Paper — Q8

### 1. Explain the top 5 cloud security best practices.

1. **Strong identity and access management** — enforce strong authentication (preferably multi-factor), the principle of least privilege, and role-based access so that each user/service has only the rights it needs. Manage and rotate credentials carefully.
2. **Encrypt data in transit and at rest** — protect data on the network (TLS/SSL) and in storage so that interception or a storage breach does not expose plaintext; control the encryption keys carefully.
3. **Backup, redundancy, and disaster recovery** — keep regular backups and a tested recovery plan so that an outage, deletion, or ransomware event does not cause permanent data loss; avoid single points of failure.
4. **Patch management and secure configuration** — keep operating systems, hypervisors, and applications up to date, harden configurations, and use trusted/verified VM images to close known vulnerabilities.
5. **Continuous monitoring, logging, and auditing** — log access and activity, monitor for anomalies and intrusions, and audit the provider against the service-level agreement and compliance requirements so incidents are detected and traced quickly.

---

### 2. What are the most important advantages of cloud technology for social networks?

1. **Elastic scalability** — social networks face unpredictable, "viral" spikes in users and content. The cloud scales resources up and down on demand, handling sudden growth without pre-provisioning hardware.
2. **Cost-effectiveness (pay-as-you-go)** — no large upfront capital investment in data centers; the network pays only for resources used, which is ideal for startups and fluctuating loads.
3. **Massive storage for user-generated content** — virtually unlimited, on-demand storage for the huge volumes of photos, video, and messages users continuously upload.
4. **Global reach and high availability** — content delivery networks and geographically distributed data centers give users worldwide fast, reliable access.
5. **Big-data analytics** — the cloud's large-scale processing (e.g., MapReduce-style platforms) lets social networks mine huge datasets for recommendations, trends, and targeted advertising.
6. **Rapid development and deployment** — managed platform services let developers build and roll out new features quickly.

---

### 3. Describe the key features of Google.

Google's cloud is built on a stack of large-scale technologies that support its services:

1. **Google App Engine (GAE)** — a **PaaS** offering on which developers build and run web applications without managing the underlying servers; it provides automatic scaling and load balancing.
2. **Google File System (GFS)** — a distributed, fault-tolerant file system that stores very large files across thousands of commodity machines with replication for reliability.
3. **BigTable** — a distributed storage system for managing large amounts of **structured** data across many servers.
4. **MapReduce** — a programming model and runtime for processing and generating very large data sets in parallel across a cluster.
5. **Chubby** — a distributed **lock service** providing coordination and consistency for the distributed components.
6. **Rich application services** — large-scale user-facing services (search, Gmail, Google Docs, Maps) delivered over this infrastructure.

Together these give Google **scalability, fault tolerance, and efficient large-scale data processing**.

---

## Makeup Paper — Q8

### 1. Discuss security risks posed by shared images and management OS.

*(Same topic as June/July Q7.2 — restated here in sequence.)*

**Shared Images**

Starting a VM from a pre-built shared image (e.g., an Amazon Machine Image) means inheriting an environment configured by someone else, which can contain:

- **Backdoors / leftover SSH keys** that let the image's creator log into your VM.
- **Malware** (trojans, rootkits).
- **Recoverable leftover data** — the publisher's deleted files, private keys, credentials, browser/shell history.
- **Outdated, unpatched software** with known vulnerabilities.
- **Unsolicited outbound connections** ("phone home" behavior).

**Management OS**

The privileged management OS (Dom0 in Xen) controls all guest VMs and hosts device drivers, so it poses these risks:

- **Large attack surface** — a full commodity OS with many exploitable bugs.
- **Single point of compromise** — breaching it gives the attacker control of every VM on the host.
- **Excess privilege** — all management functions run in one monolithic privileged domain.
- **No fault isolation** — a driver or component failure can take down the whole node.

*(Disaggregating the management OS into small least-privilege components, as in Xoar, reduces these risks.)*

---

### 2. Explain data and software protection techniques.

To protect data and software in the cloud, lightweight cryptographic marking schemes are used in addition to conventional access control and encryption.

**Data Protection — Data Coloring**

- **Data coloring** attaches unique, cryptographically generated marks ("colors" / color drops) to each data object at the data owner's side.
- A user can access an object only if their assigned color **matches** the object's color, providing **confidentiality, integrity, and traceability** (the owner can detect tampering and track ownership).
- Colors are **added** when data is stored and **removed/matched** on legitimate access. Because only small marks are processed (rather than encrypting/decrypting bulk data), data coloring is **computationally cheaper** than full bulk encryption for very large data sets.
- It works together with **trust management / trust negotiation**, so data of different sensitivity levels is matched to users of the appropriate trust/clearance level.

**Software Protection — Software Watermarking**

- **Software watermarking** embeds hidden, uniquely identifying information into software code.
- It is used to **assert ownership of intellectual property** and to **detect piracy or unauthorized copies** of the software running in the cloud.

Combined, data coloring (for data) and watermarking (for software), backed by trust management, protect both the customer's data and the provider's/owner's software in a multi-tenant cloud.

---

## Model Paper — Q8

### 1. Summarize the design goals of Xoar.

**Xoar** redesigns the Xen control VM (the monolithic, highly privileged Dom0) by **breaking it up into small, single-purpose, least-privilege components**, shrinking the Trusted Computing Base. Its design goals are:

1. **Maintain existing functionality and be transparent to guests** — the redesign must not break current features, and guest VMs should require no modification.
2. **Reduce the size of the TCB / improve isolation** — partition the monolithic control VM into several isolated single-purpose service VMs so that compromising one does not compromise the whole system.
3. **Enforce least privilege (tight control of privileges)** — each component gets only the privileges it needs, and some components are **short-lived** (created for a task, then destroyed) to limit exposure.
4. **Maintain performance** — the disaggregation must add only **negligible overhead** compared with the original Xen.

*(Xoar splits Dom0 into nine components such as Bootstrapper, PCIBack, XenStore-Logic/State, Toolstack, Builder, Console, BlkBack, and NetBack.)*

---

### 2. Explain mobile devices and cloud security.

**Why mobile devices use the cloud:** mobile devices are resource-constrained (limited CPU, memory, storage, and especially battery). They therefore **offload** heavy computation and large storage to the cloud, which extends their effective capability and battery life.

**Security implications and challenges:**

1. **Insecure communication** — data travels over public wireless/cellular links and must be protected against interception (man-in-the-middle); strong but **lightweight** encryption is needed because of limited resources.
2. **Loss or theft of the device** — physical loss exposes cached data and stored credentials/tokens.
3. **Weak authentication** — short PINs and stored login tokens make impersonation easy.
4. **Malware / malicious apps** — third-party apps may steal data or escalate privileges.
5. **Trust in the offload target** — when computation/data is offloaded, the device must trust the cloud infrastructure for confidentiality and integrity.
6. **Limited capacity for heavy security software** — resource limits restrict on-device defenses, increasing reliance on the cloud and the network.

Thus mobile + cloud improves capability but **widens the attack surface** (device, channel, and cloud), and security must span all three.

---

### 3. Model an overview of reputation system design options.

A **reputation system** collects, aggregates, and distributes feedback about the past behavior of participants so that users can judge whom/what to trust. The design space has the following main options:

**1. Where reputation is managed (architecture):**
- **Centralized reputation system** — a single authority collects all feedback and computes a global reputation score. Simple and consistent, but a single point of failure and trust.
- **Distributed (decentralized) reputation system** — peers store and exchange reputation information among themselves (no central authority). More scalable and robust, but harder to aggregate and more vulnerable to collusion.

**2. What the reputation is about (target):**
- **User (entity) reputation** — reputation attached to a person/agent (e.g., a seller or service provider).
- **Object / resource reputation** — reputation attached to an item or resource (e.g., a file, a product, a service).

**3. How reputation is computed:**
- The scope can be **global** (one score visible to all) or **local/personalized** (each user's view based on their own and trusted peers' experiences), and the scoring scheme can range from simple averages to weighted, time-decayed, or trust-propagated computations.

These options combine (e.g., a *centralized, user-based, global* system vs. a *distributed, object-based, local* system) to fit the application's scalability, trust, and robustness needs.

---

# 📌 Exam Importance Index

**Ranking logic:** The **June/July** paper is treated as the most important (most recent / actual exam), so its questions rank highest. A topic that **repeats across more than one paper** is boosted further, since recurrence signals it is likely to appear again. Makeup-paper topics rank above Model-paper-only topics.

> Priority key: ★★★★★ = study first · ★ = lowest priority

| Rank | Topic | Priority | Appears in | Why it ranks here |
|------|-------|----------|------------|-------------------|
| 1 | Security risks of **shared images & management OS** | ★★★★★ | June/July Q7.2 **+** Makeup Q8.1 | In June/July **and** repeats |
| 2 | **Trusted hypervisor** + mobile device security challenges | ★★★★★ | June/July Q7.1 (mobile theme also Model Q8.2) | June/July; mobile theme recurs |
| 3 | **Hidden risks** in cloud computing | ★★★★ | June/July Q7.3 | June/July |
| 4 | **Top 5 cloud security best practices** | ★★★★ | June/July Q8.1 | June/July |
| 5 | Cloud **advantages for social networks** | ★★★★ | June/July Q8.2 | June/July |
| 6 | **Key features of Google** | ★★★★ | June/July Q8.3 | June/July |
| 7 | **Surfaces / types of attacks** (+ cloud security risks) | ★★★ | Makeup Q7.1 **+** Model Q7.1 | Repeats across two papers |
| 8 | **Mobile devices & cloud security** | ★★★ | Model Q8.2 (links to June/July Q7.1) | Recurs; tied to a June/July topic |
| 9 | **Privacy Impact Assessment (PIA)** | ★★ | Makeup Q7.2 | Makeup paper |
| 10 | **Data & software protection** techniques | ★★ | Makeup Q8.2 | Makeup paper |
| 11 | Top **CSA 2016** cloud threats (Treacherous 12) | ★ | Model Q7.2 | Model paper only |
| 12 | **Fair Information Practices** | ★ | Model Q7.3 | Model paper only |
| 13 | Design goals of **Xoar** | ★ | Model Q8.1 | Model paper only |
| 14 | **Reputation system** design options | ★ | Model Q8.3 | Model paper only |

**If you are short on time:** cover ranks **1–6** (everything from the June/July paper) first, then **7–8** (the recurring attack/mobile themes), and treat **9–14** as the lower-priority tail.

---

*End of Module 4 answers.*
