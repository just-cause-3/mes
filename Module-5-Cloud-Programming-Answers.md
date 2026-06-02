# Module 5 — Cloud Programming and Software Environments
### Combined Answers (June/July, Makeup & Model Papers)

> Answered in the sequence given. Answers follow the Module 5 material; a few topics (serverless, multi/hybrid cloud, IaC) are not in the module text and are answered from standard cloud-computing knowledge, flagged where they appear.

---

# Q9 — Questions (All Papers)

---

## June/July Paper — Q9

### 1. What are the benefits and challenges of using serverless computing in the cloud?

*(Not covered in the Module 5 text; answered from standard knowledge.)*

**Serverless computing** is a cloud execution model in which the provider fully manages the servers and automatically allocates resources. The developer writes and deploys functions (Function-as-a-Service, e.g., AWS Lambda, Azure Functions, Google Cloud Functions), and the platform runs them on demand in response to events, billing only for actual execution.

**Benefits**

1. **No server management** — the provider handles provisioning, patching, scaling, and availability, so developers focus only on code.
2. **Automatic, fine-grained scaling** — functions scale up and down (even to zero) with the number of incoming events, with no manual capacity planning.
3. **Pay-per-use cost model** — billing is based on actual invocations and execution time, so there is no charge for idle capacity.
4. **Faster development and deployment** — small, event-driven functions speed up release cycles and simplify microservice-style design.
5. **Built-in availability and fault tolerance** — the platform replicates and reschedules functions automatically.

**Challenges**

1. **Cold starts** — an idle function may incur start-up latency when first invoked, hurting performance for latency-sensitive apps.
2. **Vendor lock-in** — functions and their event triggers are tied to a specific provider's ecosystem, making migration hard.
3. **Limited execution time and resources** — functions have caps on runtime, memory, and statelessness, so they are unsuited to long-running or heavy tasks.
4. **Statelessness** — functions don't retain local state between invocations; external storage is needed, adding complexity.
5. **Debugging and monitoring difficulty** — distributed, ephemeral functions are harder to test, trace, and debug than traditional servers.
6. **Security surface** — many small functions and event sources widen the attack surface and complicate access control.

---

### 2. How does grid computing differ from cloud computing?

Both pool distributed resources to solve large problems, but they differ in purpose, ownership, and model. The Module 5 tables note that many traditional features (workflow, scheduling, security, virtualization, data transport) are *common* to grids and clouds, but the two differ as follows:

| Aspect | Grid Computing | Cloud Computing |
|--------|---------------|-----------------|
| **Goal** | Aggregate distributed resources (often across organizations) to solve large compute-intensive scientific problems | Deliver on-demand computing as a utility service |
| **Resource ownership** | Resources owned by many different organizations (virtual organizations) and shared | Resources owned by a single provider and rented out |
| **Model** | Resource sharing / collaborative; job-oriented | Service-oriented (IaaS, PaaS, SaaS); pay-as-you-go |
| **Virtualization** | Limited; runs on heterogeneous physical resources | Core feature; VMs give elasticity and isolation |
| **Elasticity / scaling** | Static or batch-scheduled allocation | Elastic — scale up/down on demand |
| **Pricing** | Usually no direct monetary charge (shared, academic) | Metered, utility billing |
| **Fault tolerance** | Often weak / neglected | Built-in, a key feature |
| **Typical use** | High-energy physics, simulations, e-science | Web apps, big-data analytics, enterprise services |

In short, a grid is a federation of shared resources aimed at collaborative computation, whereas a cloud is a virtualized, elastic, metered utility owned by a provider.

---

### 3. What are the key features of cloud computing platforms?

The module groups cloud platform features into capabilities, infrastructure, data, and programming/runtime features. The key features are:

1. **Physical or virtual computing platform** — virtualized environments give isolated, elastic compute for different users and applications.
2. **Massive data storage and a distributed file system** — large disk capacity with put/get service interfaces (e.g., GFS, HDFS) for huge data sets.
3. **Massive (NoSQL) database storage service** — semantic, structured storage beyond raw files (e.g., BigTable, SimpleDB, Azure Table).
4. **Massive data-processing model and programming support** — harnessing thousands of nodes without managing failures or scaling manually (e.g., MapReduce).
5. **Workflow and data-query language support** — high-level workflow and query languages analogous to SQL for expressing application logic.
6. **Programming interfaces and service deployment** — web interfaces and APIs (J2EE, PHP, ASP, Rails, Ajax) for building and deploying cloud apps.
7. **Runtime support** — transparent services such as distributed monitoring, distributed task scheduling, and distributed locking.
8. **Support services** — rich data services and data-parallel execution models like MapReduce.

Additional cross-cutting features common to clouds (and some grids) include **elasticity/virtualization**, **fault tolerance**, **security and fine-grained access control (HTTPS/SSL)**, **disaster recovery via live VM migration**, **reputation systems**, **queues/notification (publish-subscribe)**, and **scalable synchronization** (Zookeeper/Chubby).

---

## Makeup Paper — Q9

### 1. Explain programming support for Google App Engine.

**Google App Engine (GAE)** is a PaaS that lets developers build and host scalable web applications on Google's infrastructure, while Google manages scaling, security, and infrastructure. Its programming support includes:

**1. Supported languages and tools**
- **Java:** an Eclipse plug-in for local debugging, the Google Web Toolkit (GWT) for dynamic web apps, and other JVM languages (JavaScript, Ruby) via interpreters.
- **Python:** common frameworks like Django and CherryPy, plus Google's lightweight `webapp` framework.

**2. Data management — Datastore**
- A schema-less **NoSQL** entity store; each entity has key-value properties (max 1 MB), queried by filtering/sorting on property values, with strong consistency via optimistic concurrency control.
- Accessed by **JDO/JPA** (via DataNucleus) in Java and **GQL** (SQL-like) in Python.
- **Transactions** group operations atomically within entity groups, with automatic retries on conflict.
- **Memcache** gives an in-memory cache for performance; **Blobstore** stores large files (up to 2 GB) such as media.

**3. Internet and external services**
- **URL Fetch** (HTTP/HTTPS over Google's fast network), **Secure Data Connection (SDC)** to tunnel to an intranet, a **Mail service**, and the **Google Data API** for Maps, YouTube, Docs, Calendar, etc.

**4. User management and multimedia**
- **Google Accounts** integration for authentication, and an **Images API** for resize/crop/flip/rotate operations.

**5. Background processing and scheduling**
- **Cron service** for periodic scheduled tasks and **Task Queues** for asynchronous background work, offloading long-running jobs from user requests.

**6. Quotas and billing**
- Enforced **quotas** on CPU, storage, and bandwidth, with a free tier, ensuring cost control and performance isolation between apps.

---

### 2. Discuss features of cloud and grid computing.

The module presents features common to both grids and clouds, organized into four groups.

**A. Traditional features common to grids and clouds**
- **Workflow** — links cloud and non-cloud services for real applications (tools: Pegasus, Kepler, Taverna, Trident); runs on Windows and Linux.
- **Data transport** — moving data in/out of clouds (often slow/costly) using simple protocols like HTTP; high-speed links for national infrastructure; cloud data (e.g., Azure blobs) supports parallel processing.
- **Security, privacy, and availability** — HTTPS/SSL, virtual clustering for dynamic provisioning, persistent storage with fast query, fine-grained access control, disaster recovery via live VM migration, and reputation systems to block attackers.

**B. Data features and databases**
- **Program library** (VM image libraries), **Blobs and Drives** (Azure blobs / Amazon S3, attachable like drives via Azure Drive / Amazon EBS), **DPFS** (GFS, HDFS, Cosmos — compute-data affinity), **SQL/relational** ("SQL as a Service"), **Tables/NoSQL** (BigTable, SimpleDB, Azure Table, HBase), and **Queuing services** (message queues with deliver-at-least-once semantics).

**C. Programming and runtime support**
- **Worker and Web roles** (Azure), **MapReduce** (Hadoop, Dryad, Twister), **cloud programming models** (Aneka, GAE), and **Software as a Service (SaaS)**.

**D. Infrastructure features** (Table 6.2/6.3)
- Accounting, appliances, authentication/authorization, operating systems, registry, scheduling, gang scheduling, virtualization (the "elastic" feature), cluster and data management, portals/gateways, scalable parallel environments (MPI), and virtual organizations.

In summary, clouds and grids share workflow, scheduling, security, and service orientation, but clouds emphasize **virtualization, elasticity, fault tolerance, and utility data services** more strongly than grids.

---

## Model Paper — Q9

### 1. Outline important cloud platform capabilities.

The important cloud platform capabilities (Table 6.1) are:

1. **Physical or virtual computing platform** — virtual platforms provide isolated environments for different applications and users.
2. **Massive data storage service / distributed file system** — large disk capacity with put/get interfaces; the DFS offers interfaces similar to a local file system.
3. **Massive database storage service** — semantic, DBMS-like structured storage in the cloud, beyond plain files.
4. **Massive data-processing method and programming model** — lets programmers harness thousands of nodes without handling network failures or scaling manually.
5. **Workflow and data query language support** — workflow languages and SQL-like data query languages to express application logic.
6. **Programming interface and service deployment** — web interfaces/APIs (J2EE, PHP, ASP, Rails, Ajax) to access cloud data and functions.
7. **Runtime support** — transparent distributed monitoring, task scheduling, and locking services.
8. **Support services** — rich data services and data-parallel execution models such as MapReduce.

These capabilities enable cost-effective utility computing with the elasticity to scale resources up or down on demand.

---

### 2. Organize the steps involved in MapReduce.

MapReduce processes large data sets in parallel using user-defined **Map** and **Reduce** functions. The logical data flow has five processing stages over (key, value) pairs:

1. **Input** — raw data is split into chunks and read as (key, value) pairs (e.g., key = byte offset, value = line of text).
2. **Map** — the Map function is applied to each input pair and emits intermediate (key, value) pairs (e.g., for word count, `("word", 1)`).
3. **Sort (Shuffle & Sort)** — the library sorts all intermediate pairs by key so identical keys are gathered together.
4. **Group** — for each unique key, the values are grouped into a list, e.g., `(key, [v1, v2, v3, …])`.
5. **Reduce** — the Reduce function aggregates each key's value list into the final output (e.g., `("word", 3)`).

**Behind the scenes (execution steps):**
1. Data partitioning splits input into chunks.
2. Computation partitioning distributes Map and Reduce tasks to workers.
3. A **master** assigns tasks to **workers** (master–worker model).
4. Each **Map worker** reads its split, runs Map, and optionally runs a **Combiner** for local aggregation.
5. A **partitioning function** (e.g., `hash(key) % R`) routes each key to a specific Reduce worker.
6. **Synchronization** — Reduce begins only after all Map tasks finish.
7. **Communication (shuffle)** — Reduce workers fetch intermediate data from all Map workers.
8. **Sorting & grouping** by key on each reduce node.
9. **Reduce** performs final aggregation and writes the output files.

**Example (word count):** input `"cat dog cat"` → Map: `("cat",1),("dog",1),("cat",1)` → Sort/Group: `("cat",[1,1]),("dog",[1])` → Reduce: `("cat",2),("dog",1)`.

---

# Q10 — Questions (All Papers)

---

## June/July Paper — Q10

### 1. How do multi-cloud and hybrid-cloud strategies differ?

*(Not covered in the Module 5 text; answered from standard knowledge.)*

Both use more than one environment, but they combine different *types* of environments.

**Multi-cloud** uses two or more **public cloud** providers (e.g., AWS + Azure + Google Cloud) together. The aim is to avoid lock-in, pick the best service from each provider, improve resilience, and meet regional/regulatory needs. All the environments are public clouds.

**Hybrid cloud** combines a **private cloud (or on-premises data center)** with one or more **public clouds**, with orchestration that lets data and workloads move between them. The aim is to keep sensitive or steady workloads in the private environment while bursting variable or non-sensitive workloads to the public cloud ("cloud bursting").

| Aspect | Multi-cloud | Hybrid cloud |
|--------|-------------|--------------|
| Environments combined | Multiple **public** clouds | **Private + public** cloud(s) |
| Primary goal | Avoid lock-in, best-of-breed, resilience | Balance control/security with scalability |
| Integration | Providers may run independently | Tight integration so workloads move between private and public |
| Data sensitivity | Less about keeping data on-prem | Keeps sensitive data private, bursts the rest |

A deployment can be both at once (e.g., a private data center plus AWS and Azure).

---

### 2. What is Infrastructure as Code (IaC) and how is it used in the cloud?

*(Not covered in the Module 5 text; answered from standard knowledge.)*

**Infrastructure as Code (IaC)** is the practice of defining and managing computing infrastructure — servers, networks, storage, load balancers, and configurations — through **machine-readable definition files** rather than manual setup. The infrastructure is described in code, version-controlled, and provisioned automatically.

**How it is used in the cloud**
- Developers write declarative or scripted definitions (e.g., **Terraform**, **AWS CloudFormation**, **Azure Resource Manager templates**, **Ansible**) describing the desired cloud resources.
- A tool reads the file and automatically **provisions and configures** the matching cloud resources via the provider's API.
- The same file can recreate identical environments for development, testing, and production.

**Benefits**
1. **Consistency and repeatability** — eliminates manual, error-prone configuration; every environment is identical.
2. **Version control** — infrastructure changes are tracked, reviewed, and rolled back like application code.
3. **Speed and automation** — large environments are stood up or torn down quickly and automatically.
4. **Scalability** — easy to replicate infrastructure across regions or scale resources.
5. **Documentation** — the code itself documents the infrastructure.

IaC is a foundation of DevOps and automated, elastic cloud deployment.

---

### 3. What are emerging cloud environments?

Emerging cloud environments are open-source and research-oriented platforms for VM management, storage, and data processing. The module describes:

1. **Eucalyptus** — from UC Santa Barbara; emulates **Amazon EC2 and S3** (its **Walrus** component is S3-like). Supports VM image upload/management and lifecycle; available as open source and commercial.
2. **Nimbus** — an open-source **IaaS** cloud that leases remote resources via VM deployment. Includes **Cumulus** (S3-compatible storage with quotas), works with EC2 clients (Java Jets3t, boto), and offers Resource-Pool and Pilot modes; **Nimbus Web** is a Django-based interface.
3. **OpenNebula** — a modular **IaaS** platform managing the full VM lifecycle and dynamic networking; uses the **libvirt** API and cloud drivers for hybrid clouds (EC2, ElasticHosts); supports VM migration, snapshots, a capacity scheduler, and an image repository.
4. **Sector/Sphere** — for **big-data** storage and parallel processing. **Sector** is a wide-area DFS (UDP for control, UDT for data) with topology-aware replication; **Sphere** processes data with user-defined functions emphasizing data locality and fault tolerance; **Space** is a column-based table store with a limited SQL subset.
5. **OpenStack** — open-source platform started by **Rackspace and NASA**, focused on **Compute (Nova)** — message-based, shared-nothing, Python, Amazon-API compatible — and **Storage (Swift)** — proxy server, ring, and object/container/account servers with replication and failure isolation.
6. **Aneka (Manjrasoft)** — a **cloud programming/application platform** built on .NET (Linux via Mono). It supports **Build, Accelerate, Manage** capabilities; three programming models (**Thread, Task, MapReduce**); and SLA-aware dynamic provisioning across private, public, and hybrid clouds (e.g., the GoFront Maya rendering case reduced rendering from three days to three hours using 20 PCs).

---

## Makeup Paper — Q10

### 1. Discuss parallel computing and programming paradigms.

**Parallel and distributed programming** means running a program simultaneously on multiple computing systems.
- **Parallel computing:** running parts of a program at the same time on multiple processors (may or may not be networked).
- **Distributed computing:** running a program across multiple networked computers (e.g., clusters).
- **Benefits:** faster response for users; better performance and resource use for systems.
- **Challenge:** managing task coordination, data sharing, and communication is complex.

**Key concepts in parallel programming**
1. **Partitioning** — *computation partitioning* (break the program into smaller concurrent tasks) and *data partitioning* (split input data into chunks processed in parallel).
2. **Mapping** — assign tasks/data pieces to specific computing resources.
3. **Synchronization** — coordinate tasks to avoid race conditions and data dependencies.
4. **Communication** — tasks exchange data over the network when needed.
5. **Scheduling** — decide which task runs when, especially when tasks outnumber workers.

**Why use programming paradigms?**
Managing the above manually is hard, so paradigms/models hide complexity. Benefits: easier programming, faster development, better resource use, higher performance, and more abstraction and scalability.

**Popular paradigms/models**
- **MapReduce, Hadoop, Dryad** — originally for data processing, now widely used; more fault-tolerant and scalable than older models like **MPI**.
- **Twister** — iterative MapReduce that keeps data in memory and uses δ-flow for repeated computation (e.g., K-means, PageRank).
- **MPI** — fastest for tightly-coupled, fine-grained computation, but lower-level and harder to use.

The module also classifies applications into categories — SIMD, SPMD, asynchronous objects, pleasingly (embarrassingly) parallel, workflow/coarse-grained, and MapReduce++ (data-intensive) — to match each problem type to the right model and infrastructure.

---

### 2. Explain programming on Amazon AWS and Microsoft Azure.

**Amazon AWS**

- **EC2 (Elastic Compute Cloud)** — the pioneering **IaaS**: users rent virtual machines instead of hardware and pay per hour, creating/launching/terminating instances on demand (elasticity). VMs launch from **Amazon Machine Images (AMIs)** — pre-configured templates (Public, Private, Paid). Launch workflow: *create AMI → create key pair → configure firewall → launch instance*. Instance classes include Standard, Micro, High-Memory, High-CPU, and Cluster Compute, with performance measured in **EC2 Compute Units (ECUs)**.
- **S3 (Simple Storage Service)** — web-based **object storage**; each object (data + metadata + ACL) is stored in a **bucket** and accessed by a unique key via REST/SOAP. Very high durability (11 nines), geo-replication, ACL security, HTTP/BitTorrent access.
- **EBS (Elastic Block Store)** — persistent **block-level** volumes (1 GB–1 TB) attachable to EC2 like a virtual disk, with snapshots for incremental backup; data persists after the instance stops.
- **SimpleDB** — a lightweight **NoSQL** store (Domain = table, Item = row, Attribute = column, Value = cell) with eventual consistency, suited to metadata and small structured data.

**Microsoft Azure**

- **Azure Fabric and Roles** — the Fabric manages VMs, load balancing, fault tolerance, and resource allocation. **Web roles** host web apps (via IIS) and **Worker roles** run background processing; lifecycle methods are `OnStart()`, `OnStop()`, and `Run()` (no live debugging — use trace logs).
- **SQL Azure** — SQL Server as a managed, scalable, replicated relational database service.
- **Azure Storage** — **Blob storage** (Block blobs up to 200 GB for streaming media, Page blobs up to 1 TB for random access; Drives are NTFS volumes on blobs, like EBS); **Azure Tables** (NoSQL key-value store, entities with PartitionKey/RowKey, queried via ADO.NET/LINQ); and **Azure Queues** (message queuing between web and worker roles, ~8 KB messages, deliver-at-least-once).

Azure services map closely to AWS: Web role ↔ EC2 web servers, Worker role ↔ EC2/Lambda, SQL Azure ↔ Amazon RDS, Blob ↔ S3, Page Blob/Drives ↔ EBS, Tables ↔ SimpleDB, Queues ↔ SQS.

---

## Model Paper — Q10

### 1. Explain with a neat diagram how data flows in a MapReduce job at various task trackers using the Hadoop library.

In Hadoop, a MapReduce job involves the **User node**, the **JobTracker** (master), the **NameNode** (HDFS master), the **TaskTrackers** (slaves running map/reduce tasks in JVM slots), and the **DataNodes** (storing HDFS blocks).

```
        Submit job
  USER ───────────────►  JobTracker  ◄──── metadata ──── NameNode
                          │   │   │                       (knows block
              task assign │   │   │ heartbeat              locations)
              ┌───────────┘   │   └───────────┐
              ▼               ▼               ▼
       ┌────────────┐  ┌────────────┐  ┌────────────┐
       │ TaskTracker│  │ TaskTracker│  │ TaskTracker│
       │ [JVM: Map] │  │ [JVM: Map] │  │ [JVM:Reduce]│
       └─────┬──────┘  └─────┬──────┘  └─────┬──────┘
             │ access        │ access        │ access
       ┌─────▼──────┐  ┌─────▼──────┐  ┌─────▼──────┐
       │  DataNode  │  │  DataNode  │  │  DataNode  │
       │  (blocks)  │  │  (blocks)  │  │  (blocks)  │
       └────────────┘  └────────────┘  └────────────┘
```

**Data-flow steps:**
1. **Job submission** — the user submits the job to the JobTracker via `runJob(conf)`; the JobTracker gets block locations from the NameNode and splits the job into map/reduce tasks.
2. **Task assignment** — the JobTracker creates one map task per input split and assigns them to TaskTrackers considering **data locality** (map tasks scheduled near their data blocks); reduce tasks are assigned without locality.
3. **Task execution** — each TaskTracker copies the job's JAR, launches a **JVM (a slot)**, and runs the assigned Map or Reduce task. Map tasks read blocks from the local DataNode.
4. **Heartbeat** — TaskTrackers periodically send **heartbeat** messages to the JobTracker to report status, confirm they are alive, and advertise free slots.
5. **Shuffle and reduce** — intermediate map outputs are shuffled to reduce TaskTrackers, which aggregate the data and write final output to HDFS.

Thus the Hadoop library automatically handles task assignment, data locality, parallel execution across TaskTrackers, and fault recovery.

---

### 2. Explain Data Mutation Sequence in GFS with a diagram.

In the **Google File System (GFS)**, a *mutation* (write or append) follows a controlled sequence so that all chunk replicas stay consistent. One replica is designated the **primary** (holds the chunk lease); the others are **secondaries**.

```
        (1) lease + replica locations
  CLIENT ───────────────────────────►  MASTER
        ◄───────────────────────────
        (2) primary + secondary locations

        (3) push data (chained to all replicas)
  CLIENT ───────► Secondary A ───────► Primary ───────► Secondary B
        (4) write request │
  CLIENT ─────────────────►  PRIMARY
                              │ (5) forward write in serial order
                              ├──────────► Secondary A
                              └──────────► Secondary B
                              ◄────────── (6) confirm success
        (7) success / failure │
  CLIENT ◄────────────────────┘
```

**Steps:**
1. **Client → Master:** asks for the chunk lease holder and the replica locations.
2. **Master → Client:** replies with the **primary** and **secondary** chunkserver locations (client caches this).
3. **Client → all replicas:** pushes the data to all replicas (typically chained for network efficiency); the data is buffered, not yet written.
4. **Client → Primary:** sends the **write request**.
5. **Primary → Secondaries:** assigns a serial order to the mutation and forwards the write request to all secondaries in that order (ensuring consistent ordering).
6. **Secondaries → Primary:** confirm completion of the write.
7. **Primary → Client:** returns the final success or failure.

If any replica reports an error, the client retries steps 3–7 (or restarts the whole operation). GFS also provides **record append**, which guarantees data is appended **at least once** with the offset chosen by GFS, supporting atomic concurrent appends by multiple writers.

---

*End of Module 5 answers.*
