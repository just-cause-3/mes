# BIS613D — Cloud Computing & Security
## Module 3 — Full-Marks Q&A Pack (Concept-Family Strategy)

> **Source discipline:** Every answer below is built only from your **Module 3 notes** (Textbook 1, Ch. 4) and the **official evaluation-scheme answers** (June/July 2025 scheme + Model-paper solutions). No outside material is added.

---

## 0. What to study to get full marks (the plan)

Module 3 gives you **Q5 OR Q6 — answer one full question for 20 marks.** Because the two sections overlap on data-center design (B) and platforms (D), you cannot truly study "only one section" without already covering most of the other. The optimal minimum is **four concept families (A + B + C + D)**; drop only E (six challenges, ~6% of the space).

| Family | What to study | Covers | Why |
|---|---|---|---|
| **A — Models** | Service models (IaaS/PaaS/SaaS), Public/Private/Hybrid, six design objectives, cloud ecosystem (Fig 4.4) | ~31% | Dominates Q5; near-certain to appear |
| **B — DC Design** | Warehouse vs modular, fat-tree (Fig 4.10), interconnection requirements, DC networking (Fig 4.2) | ~25% | Appears in **both** Q5 and Q6 |
| **C — DC Management** | The 8 data-center management requirements (§4.2.5) | ~13% | Appeared in **all 3** papers; cheapest insurance |
| **D — Platforms** | GAE, AWS, Azure (components + comparison) | ~25% | Appears in **both** sections; the Q6 spine |
| ~~E — Challenges~~ | *(Optional)* six open challenges | ~6% | Rarest; safe to skip if short on time |

**Cumulative coverage:** A → 31% · A+B → 56% · A+B+D → 81% · **A+B+C+D → 94%** · +E → 100%.

**Verdict:** Study **A + B + C + D**. You become section-agnostic — read both Q5 and Q6 on exam day and write whichever is friendlier. This is far safer than betting on one section, for the cost of one short list (C).

---

# FAMILY A — MODELS
*(Service models, deployment models, design objectives, ecosystem)*

---

### A1. Explain the three primary cloud service models (IaaS, PaaS, SaaS) with examples. *(6–10 marks)*

**1. Infrastructure as a Service (IaaS)**
- Provides virtualized computing resources — CPU, storage, and networking — over the internet.
- User manages the OS, applications, and storage, but **not** the underlying hardware.
- Examples: **Amazon EC2** (virtual servers/VMs), **Amazon S3** (scalable storage). Also Google Compute Engine, Azure Virtual Machines.
- Use case: startups and enterprises needing scalable infrastructure without upfront hardware investment.

**2. Platform as a Service (PaaS)**
- Provides a software-development platform with tools, runtime environments, and middleware.
- Developers build, test, and deploy applications without managing infrastructure.
- Examples: **Google App Engine** (supports Java, Python; includes auto-scaling and load balancing). Also AWS Elastic Beanstalk, Azure App Services.
- Use case: developers who want to focus on application logic, not servers.

**3. Software as a Service (SaaS)**
- Delivers application software over the web, accessed through a browser on a subscription basis.
- End users access applications without managing any infrastructure or platform.
- Examples: **Gmail, Google Docs, Salesforce CRM.** Also Microsoft 365.
- Use case: day-to-day operations like email, document editing, and CRM.

> **Diagram to draw:** Figure 4.5 — IaaS, PaaS, and SaaS shown at different service levels (Client interface → Launch/Monitor controllers → Master/Worker/Distributed file system; DaaS holds the Status DB; Billing service on the right).

---

### A2. Differentiate Public, Private, and Hybrid clouds with advantages and limitations. *(6 marks)*

**1. Public Cloud**
- Infrastructure and services offered over the internet to general users on a pay-per-use basis; owned and managed by third-party providers.
- Examples: AWS, Microsoft Azure, Google App Engine, IBM Blue Cloud.
- **Advantages:** no capital investment; highly scalable and elastic; easy, quick global access.
- **Limitations:** less control over data/infrastructure; security and compliance concerns in shared environments.

**2. Private Cloud**
- Cloud environment operated solely for a single organization, hosted on-premises or by a third-party; accessed only by the organization and its authorized users.
- Example: IBM Research Compute Cloud (RC2).
- **Advantages:** greater security, privacy, and control; customization to organizational needs.
- **Limitations:** high setup and maintenance cost; limited scalability vs public clouds.

**3. Hybrid Cloud**
- Combines public and private clouds so data/applications can be shared between them; used to handle burst workloads while keeping sensitive tasks internal.
- Examples (notes): AWS Outposts, Microsoft Azure Stack.
- **Advantages:** balance of cost-efficiency and control; flexibility to scale on public cloud while keeping sensitive data private.
- **Limitations:** complexity in integration/management; security and compliance issues in data flow between environments.

> **Diagram to draw:** Figure 4.1 — public, private, and hybrid clouds shown over the Internet (private cloud + intranet on the left; public clouds — Azure, AWS, IBM Blue Cloud, Salesforce, Google App Engine — in the centre; cloud users on the right).

---

### A3. Outline the six design objectives for cloud computing. *(6 marks)*

1. **Shift from desktop to data center** — move computing, storage, and software from personal desktops to centralized data centers over the Internet.
2. **Service provisioning and cloud economics** — services delivered through SLAs; pay-as-you-go pricing; efficient use of power and resources.
3. **Scalability in performance** — the system must support more users by scaling up performance as needed.
4. **Data privacy protection** — users must trust that providers keep their private data safe and secure.
5. **High quality of cloud services** — QoS standardized so services work smoothly across different providers.
6. **New standards and interfaces** — universal APIs and access protocols to avoid data lock-in and keep apps portable across platforms.

---

### A4. With a neat diagram, build a cloud ecosystem with a private cloud. *(8 marks)*

- A cloud ecosystem includes cloud **providers, users, and technologies** working together.
- **Public clouds** are commonly used and form the base of the ecosystem; **private and hybrid clouds** let organizations use both internal and public resources.
- Users want flexible platforms to run services such as websites and databases.
- **Cloud management** provides virtual resources over an **IaaS** platform.
- **Virtual Infrastructure (VI) management** allocates virtual machines across server clusters.
- **VM managers** handle and control VMs running on physical machines — **Xen, KVM, VMware.**
- Tools such as **OpenNebula, vSphere, Eucalyptus, and Nimbus** manage cloud systems.
- Many startups use cloud resources instead of building their own IT.
- Access interfaces: **Amazon EC2WS, Nimbus WSRF, ElasticHosts REST.**
- VI tools also support load balancing, dynamic resizing, and efficient use of server resources.

> **Diagram to draw:** Figure 4.4 — four-layer stack:
> - **(a) Cloud consumers:** Individual users · Other clouds · Platform-as-a-Service
> - **(b) Cloud management:** Cloud interfaces (Amazon EC2WS, Nimbus WSRF, ElasticHosts REST) over Amazon EC2 / Eucalyptus / Globus Nimbus
> - **(c) VI management:** OpenNebula, VMware vSphere and others
> - **(d) VM managers:** Xen · KVM · VMware

---

# FAMILY B — DATA-CENTER DESIGN & FABRIC
*(Warehouse vs modular, fat-tree topology, interconnection, DC networking)*

---

### B1. What is a warehouse-scale data center? How does it differ from modular data centers? *(8 marks)*

**Warehouse-Scale Data Center (WSDC)**
- A massive, centralized data center built in a single facility — designed like a shopping mall or factory floor — housing hundreds of thousands to millions of servers.
- Example: Google and Microsoft data centers, often about **11 times the size of a football field.**
- Offers economies of scale (cost per unit drops as size grows); efficient for centralized cloud services and large-scale storage/compute.
- Key features: centralized management; high operational efficiency; suited to large-scale, high-throughput services; lower network/storage cost due to scale.

**Modular Data Center**
- Consists of containerized units — server clusters housed inside truck-towed containers — forming a shipping-yard-style data center that can be moved as needed.
- Example: **SGI ICE Cube** container can house 46,080 processing cores or 30 PB of storage per container.
- Enables rapid deployment, mobility, and energy efficiency.
- Key features: better cooling efficiency (up to 80% less than traditional warehouses); suited to remote locations; scalable by adding containers; easier maintenance and relocation.

**Comparison Table**

| Feature | Warehouse-Scale DC | Modular DC |
|---|---|---|
| Scale | 400,000 to 1 million servers | Up to tens of thousands per container |
| Structure | Centralized building | Truck-towed containers |
| Deployment | Fixed, permanent | Rapid, flexible, mobile |
| Cooling | Raised-floor CRAC systems | Heat exchangers and chilled-water pipes |
| Cost Efficiency | Lower operational cost at massive scale | Reduced cooling and installation cost |
| Flexibility | Low mobility | High mobility and modularity |

---

### B2. Describe the fat-tree topology and explain its use in data-center network architecture. *(6 marks)*

> **Important:** The June/July paper printed this as "**fat-free** topology," but the official scheme itself describes it as "**a variant of the Fat-Tree topology**." There is no separate "fat-free" concept in your textbook — answer with **fat-tree** (Figure 4.10). Use the wording below, which matches the scheme.

- The fat-tree topology is derived from the **Fat-Tree architecture**, which is based on **Clos networks** used in telecommunications.
- Its goal is to **reduce hardware cost and power consumption** while maintaining near-optimal **bandwidth and fault tolerance**.
- It follows a **hierarchical layout — core, aggregation, and edge layers** — but with fewer redundant paths than a full fat-tree; links are reduced based on utilization for better resource efficiency.
- Used in data centers to design **cost-effective, energy-efficient, scalable** networks with fewer switches and cables.
- **Benefits:** lower deployment cost, decreased energy use, simpler management, performance comparable to complex topologies.
- **Limitations:** slightly reduced redundancy/fault tolerance; requires traffic analysis to decide which links can be safely removed.
- **Applications:** cloud data centers, enterprise networks, AI training clusters.

> **Diagram to draw:** Figure 4.10 — fat-tree with three layers: **Core** switches at top → **Aggregation** → **Edge** switches, organized across **Pods 0–3**, with servers below each pod.

---

### B3. Explain the key requirements for an efficient cloud data-center interconnection network. *(8 marks)*

An interconnection network is the communication backbone connecting thousands of servers and storage devices within and across data centers. The key requirements are:

1. **High Bandwidth** — support high-throughput links across all nodes so big-data, ML, and content-delivery workloads avoid bottlenecks.
2. **Low Latency** — low end-to-end latency for real-time, synchronization-heavy distributed applications (video conferencing, gaming, trading).
3. **Scalability** — scale from hundreds to millions of servers without performance loss, using topologies like **Fat-Tree, Clos, or Spine-Leaf.**
4. **Fault Tolerance and Reliability** — tolerate switch/link failures via path redundancy, multi-path routing (**ECMP**), and failover for high availability.
5. **Efficient Load Balancing** — distribute traffic evenly to maximize utilization and avoid congestion; dynamically reroute on uneven load or failure.
6. **Energy Efficiency** — minimize power via power-aware routing, energy-efficient switches, and traffic consolidation during low usage.
7. **Cost Effectiveness** — use commodity switches, intelligent topologies, and optimized cabling to reduce CAPEX and OPEX.
8. **Support for Virtualization and SDN** — support dynamic reconfiguration, network slicing, and multi-tenancy (Software-Defined Networking).

---

### B4. Discuss the data-center networking structure with a neat diagram. *(10 marks)*

- Cloud computing enables **dynamic resource allocation** through virtual clusters, with **gateway nodes** as access points and security controls. Unlike grids (static allocation), cloud platforms handle fluctuating workloads dynamically.
- **Data centers vs supercomputers:** data centers use large clusters of servers with integrated storage and cache; supercomputers use separate data farms. Networking differs — supercomputers use high-bandwidth custom networks, while **data centers use IP-based networks such as 10 Gbps Ethernet.**
- Private-cloud examples: NASA (climate modeling), CERN (research-resource distribution), governed by SLAs.
- Cloud services are categorized as **service-access nodes** (user interactions), **runtime supporting service nodes** (assist operations), and **independent service nodes** (specialized services).
- Performance is optimized by minimizing data movement, reducing Internet traffic, and addressing petascale I/O.

> **Diagram to draw:** Figure 4.2 — standard data-center networking:
> - **Internet** → **BR** (L3 border routers)
> - **Data center Layer 3** → **AR** (L3 access routers)
> - **Layer 2** → **LB** (load balancer) + **S** (L2 switches)
> - Bottom → **A** (racks of servers), all within a single Layer-2 domain.
> *Key:* BR = L3 border router · AR = L3 access router · S = L2 switch · LB = load balancer · A = rack of servers.

---

# FAMILY C — DATA-CENTER MANAGEMENT
*(The single most reliable Module-3 topic — appeared in all 3 papers)*

---

### C1. Identify the basic requirements for managing the resources of a data center. *(8–10 marks)*

These requirements come from real design and operational experience in IT and cloud-service industries.

1. **Making common users happy** — provide quality service (QoS) to the majority of users for at least **30 years.**
2. **Controlled information flow** — streamline information flow; sustained services and **high availability (HA)** are primary goals.
3. **Multiuser manageability** — manage all functions: traffic flow, database updating, and server maintenance.
4. **Scalability to prepare for database growth** — storage, processing, I/O, power, and cooling subsystems must be scalable as workload grows.
5. **Reliability in virtualized infrastructure** — integrate **failover, fault tolerance, and VM live migration** to recover critical applications from failures or disasters.
6. **Low cost to both users and providers** — reduce all operational costs of the cloud system built over the data centers.
7. **Security enforcement and data protection** — deploy privacy and security defenses against network attacks and system interrupts; maintain data integrity.
8. **Green information technology** — save power and improve energy efficiency in current and future data centers.

> **To stretch toward a 10-mark answer**, the Model-paper solution adds two more (use only if you need extra length):
> 9. **Service Automation** — automate routine tasks (backups, load balancing, patch updates) to improve speed/accuracy and reduce manual error.
> 10. **Monitoring and Reporting** — continuously monitor performance and usage; reports help plan upgrades and detect issues.

---

# FAMILY D — PUBLIC CLOUD PLATFORMS
*(GAE, AWS, Azure — the Q6 spine; also leaks into Q5)*

---

### D1. Organize the functional modules of GAE. *(6 marks)*

GAE is a **Platform-as-a-Service (PaaS)** for building and running web applications on Google's cloud. Its functional modules are:

1. **Runtime Environment** — runs applications in Java, Python, Go, or PHP.
2. **Datastore** — NoSQL database service for storing structured data (based on BigTable).
3. **Task Queues** — handles background tasks without blocking user requests.
4. **Memcache** — fast, in-memory caching for frequently accessed data.
5. **User Authentication** — APIs to manage user login and identity.
6. **App Versioning and Deployment** — supports multiple app versions with easy deployment and rollback.

> **If asked for "components/architecture" instead** (notes §4.4.2), use: Datastore · Application Runtime Environment · Software Development Kit (SDK) · Administration Console · GAE Web Service Infrastructure — built on **GFS, MapReduce, BigTable, and Chubby.**
>
> **Diagram to draw:** Figure 4.7 — local development (Build → Test → Deploy via App Engine SDK) → Upload → App Engine admin console (manage/traffic/version control) + User interface → Google load balance → data servers.

---

### D2. Write a note on AWS. *(4 marks)*

- **AWS uses the IaaS (Infrastructure as a Service) model** — provides virtual machines through **EC2** to run cloud applications.
- **Storage:** **S3** (object-based storage) and **EBS** (Elastic Block Store — block-level storage for regular apps).
- **Messaging:** **SQS** (stores messages even if the receiver is offline) and **SNS** (sends notifications).
- **Performance services:** **ELB** (spreads traffic across EC2 instances), **CloudWatch** (monitors CPU, memory, network), **Auto Scaling** (adds/removes instances on demand).

> **For a longer note**, the notes (§4.4.3) add: RDS (managed relational DB), Elastic MapReduce/EMR (Hadoop on EC2), AWS Import/Export, CloudFront (content distribution), FPS, FWS. Advantages: global infrastructure with multiple availability zones, broad range of services, strong security and compliance.
>
> **Diagram to draw:** Figure 4.21 — Amazon cloud infrastructure (User ↔ EC2 instances with EBS, connected via SQS, S3, and SimpleDB; Developer on the right).

---

### D3. Compare the services and target applications of GAE, AWS, and Microsoft Azure. *(6 marks)*

- **GAE (Google App Engine):** a **PaaS** offering for building, deploying, and scaling applications without managing infrastructure.
- **AWS (Amazon Web Services):** a comprehensive cloud platform providing **IaaS + PaaS + SaaS** for compute, storage, databases, AI, and more.
- **Microsoft Azure:** a cloud platform supporting **IaaS + PaaS + SaaS** — virtual computing, analytics, storage, networking, and AI — with strong hybrid integration.

| Feature / Criteria | Google App Engine (GAE) | Amazon Web Services (AWS) | Microsoft Azure |
|---|---|---|---|
| Service Type | PaaS | IaaS + PaaS + SaaS | IaaS + PaaS + SaaS |
| Primary Focus | Simplifying app dev & deployment | Full-suite cloud infra & services | Enterprise-grade, hybrid integration |
| Programming Languages | Java, Python, Go, Node.js, PHP, Ruby, .NET | Most languages (Java, Python, C#, PHP, Go, Ruby) | C#, .NET, Java, Python, Node.js |
| Compute Services | App Engine Standard & Flexible | EC2, Lambda, ECS, EKS | Azure VMs, Functions, App Services |
| Storage Services | Cloud Datastore, Cloud SQL, Cloud Storage | S3, DynamoDB, RDS, EBS | Blob Storage, Azure SQL, Cosmos DB |
| Target Applications | Web apps, mobile backends, REST APIs | Web/mobile apps, big data, ML, enterprise | Enterprise apps, hybrid cloud, .NET, DevOps |
| Scalability & Mgmt | Auto-scaling, serverless, fully managed | Auto-scaling, configurable management | Auto-scaling, integrated management tools |

---

# (Optional) FAMILY E — drop unless time allows

### E1. Summarize the six open challenges in cloud architecture development.
1. **Service Availability & Data Lock-in** — single-provider failure risk; proprietary APIs prevent migration → use multiple providers, standardize APIs.
2. **Data Privacy & Security** — open to DDoS, malware, VM hijacking; data-sovereignty laws → encryption, firewalls, access control.
3. **Unpredictable Performance & Bottlenecks** — VMs share CPU/memory well but I/O lags → improve I/O virtualization, remove bottlenecks.
4. **Distributed Storage & Software Bugs** — must scale on demand; large-scale bugs hard to reproduce → VM-based/simulated debugging.
5. **Scalability, Interoperability & Standardization** — diverse pricing; OVF for portable VMs; Intel↔AMD migration still hard.
6. **Software Licensing & Reputation Sharing** — need pay-per-use licensing; cloud-wide blacklisting; SLAs must define legal liability.

---

## Final exam-day checklist

- [ ] **A — Models:** IaaS/PaaS/SaaS · Public/Private/Hybrid · 6 design objectives · Ecosystem (Fig 4.4)
- [ ] **B — DC Design:** Warehouse vs modular (+table) · Fat-tree (Fig 4.10) · Interconnection requirements · DC networking (Fig 4.2)
- [ ] **C — DC Management:** 8 (→10) requirements
- [ ] **D — Platforms:** GAE modules (Fig 4.7) · AWS note (Fig 4.21) · GAE/AWS/Azure comparison table
- [ ] Practice the **4 diagrams** (4.4, 4.10, 4.2, 4.7/4.21) by hand
- [ ] On exam day: read **both** Q5 and Q6, then write the one with more of your prepared families

*Coverage with A+B+C+D ≈ 94% of the historical Module-3 question space.*

---

## Appendix — All Module-3 questions (raw list)

**Model Question Paper (2022 CBCS)**
- Q5a. Outline six design objectives for cloud computing.
- Q5b. With a neat diagram, build a cloud ecosystem with a private cloud.
- Q5c. Organize Functional Modules of GAE.
- Q6a. Identify basic requirements for managing the resources of a data center.
- Q6b. Summarize six open challenges in cloud architecture development.
- Q6c. Write a note on AWS.

**Makeup Exam (June/July 2025)**
- Q5a. Explain cloud computing and service models with neat diagram.
- Q5b. Discuss Data Center Networking Structure with neat diagram.
- Q6a. Explain Data-center construction requirements.
- Q6b. Explain the following: i) GAE ii) AWS.

**Main Exam (June/July 2025)**
- Q5a. Explain the three primary cloud service models IaaS, PaaS, and SaaS with examples.
- Q5b. Differentiate between Public, Private and Hybrid cloud with advantages and limitation.
- Q5c. What is a warehouse-scale data center? How does it differ from modular data centers?
- Q6a. Compare the services and target applications of GAE, AWS and Microsoft Azure.
- Q6b. Describe the fat-free topology and explain its use in data center network architecture.
- Q6c. Explain the key requirements for an efficient cloud data center interconnection network.
