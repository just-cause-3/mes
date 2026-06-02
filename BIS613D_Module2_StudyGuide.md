# BIS613D – Cloud Computing and Security
## Module‑2: Virtualization — Complete Question Bank, Detailed Answers & Importance Ranking

> **Module‑2 syllabus:** Virtual Machines and Virtualization of Clusters and Data Centers — Implementation Levels of Virtualization, Virtualization Structures/Tools and Mechanisms, Virtualization of CPU/Memory and I/O devices, Virtual Clusters and Resource Management, Virtualization for Data Center Automation.
> *(Textbook 1: Chapter 3, Sections 3.1 to 3.5)*

This guide is compiled from **five documents**:

| # | Document | Type |
|---|----------|------|
| 1 | `BIS613D_Module_2.pdf` | Module‑2 notes (source for all answers) |
| 2 | `Model_Solved.pdf` | Model Question Paper **with solved answers** |
| 3 | `Makeup__1_.pdf` | PYQ – Make‑Up Exam, June/July 2025 |
| 4 | `June_July.pdf` | PYQ – Regular Exam, June/July 2025 |
| 5 | `Model_Paper.pdf` | Model Question Paper (same questions as Doc 2) |

> **Note:** Documents 2 and 5 are the **same Model Question Paper** (Doc 2 additionally contains worked solutions). So there are effectively **three distinct question sets** for Module‑2: the Model Paper, the Make‑Up exam, and the June/July regular exam.

---

# PART A — All Module‑2 Questions Extracted

## A.1 Model Question Paper (Doc 2 = Doc 5)
| Q. No | Question | Bloom | Marks |
|-------|----------|-------|-------|
| Q.03 a | Demonstrate the architecture of a computer system before and after virtualization | L2 | 10 |
| Q.03 b | Compare Physical versus Virtual Clusters | L2 | 10 |
| Q.04 a | Construct the Live‑migration process of a VM from one host to another | L3 | 10 |
| Q.04 b | Develop an architecture of *livewire* for Intrusion Detection using a dedicated VM | L3 | 10 |

## A.2 Make‑Up Exam, June/July 2025 (Doc 3)
| Q. No | Question | Bloom | Marks |
|-------|----------|-------|-------|
| Q.3 a | Discuss levels of virtualization implementation | L2 | 10 |
| Q.3 b | Discuss full virtualization and para‑virtualization | L2 | 6 |
| Q.3 c | Explain CPU and memory virtualization | L2 | 4 |
| Q.4 a | Explain virtualization structure / tools and mechanisms | L2 | 10 |
| Q.4 b | Discuss virtualization for data‑center automation | L2 | 10 |

## A.3 Regular Exam, June/July 2025 (Doc 4)
| Q. No | Question | Bloom | Marks |
|-------|----------|-------|-------|
| Q.3 a | What is Xen? Discuss its elements for virtualization | L2 | 6 |
| Q.3 b | Discuss the reference model of full virtualization | L3 | 6 |
| Q.3 c | List and discuss different types of virtualization | L2 | 8 |
| Q.4 a | How is cloud development different from traditional software development? | L2 | 6 |
| Q.4 b | Discuss the architecture of Hyper‑V. Discuss its use in cloud computing | L2 | 6 |
| Q.4 c | Discuss classification on taxonomy of virtualization of different levels | L3 | 8 |

## A.4 Consolidated Unique Topics (after merging duplicates)
1. Levels / Types / Taxonomy of Virtualization Implementation *(appears 3×)*
2. Full Virtualization & Para‑Virtualization (incl. reference model) *(appears 2×)*
3. Architecture of a computer system before & after virtualization
4. Virtualization Structures / Tools and Mechanisms
5. Xen architecture and its elements
6. CPU and Memory Virtualization
7. Live‑migration process of a VM
8. Physical vs Virtual Clusters
9. Architecture of Livewire (VM‑based IDS)
10. Virtualization for Data‑Center Automation
11. Architecture of Hyper‑V and its use in cloud computing
12. Cloud development vs Traditional software development

---

# PART B — In‑Depth Explanation & Answer for Each Question

---

## B1. Levels / Types / Taxonomy of Virtualization Implementation
**(Make‑Up Q3a · June/July Q3c · June/July Q4c — the single most repeated topic, 3 appearances)**

### Definition
**Virtualization** is the process of creating *virtual* instances of computing resources — servers, storage, networks or operating systems — on a single physical machine, allowing multiple virtual environments to run independently on the same hardware and thereby improving **resource utilization, scalability and flexibility**.

A **virtualization layer** (software) sits between the physical hardware and the operating system/applications. It converts real hardware into virtual hardware so that different OSes (e.g., Linux and Windows) can run simultaneously on one machine.

### The Five Abstraction Levels
Virtualization is implemented at **five abstract levels**, ranging from the hardware up to the application (Textbook Figure 3.2):

| Level | Where it works | Typical Tools / Examples |
|-------|----------------|--------------------------|
| 1. Instruction Set Architecture (ISA) | Between two different instruction sets | Bochs, Crusoe, QEMU, BIRD, Dynamo |
| 2. Hardware Abstraction Layer (HAL) | On the bare hardware | VMware, Virtual PC, Denali, Xen, Plex 86, User‑Mode Linux |
| 3. Operating System level | At the OS kernel layer | Jail, Virtual Environment, Ensim's VPS, FVM, OpenVZ |
| 4. Library (user‑level API) level | Between application and OS libraries | WINE, WABI, LxRun, Visual MainWin, vCUDA |
| 5. User‑Application level | On top of the OS as an application | JVM, .NET CLR, Panot |

#### 1) Instruction Set Architecture (ISA) Level
- Enables execution of instructions written for **one architecture on a host with a different architecture** through **instruction‑set emulation**.
- **Emulation** = mimicking the behaviour of one system by another, letting programs/OSes designed for one platform run on another.
- **Techniques:**
  - **Code Interpretation** — an interpreter translates source instructions into native host instructions *one‑by‑one*. Simple but very slow (one source instruction may need tens/hundreds of native instructions).
  - **Dynamic Binary Translation** — translates *blocks* of source instructions (basic blocks/traces/superblocks) and **caches** them, so repeated executions need no re‑translation. Much faster.
  - **Binary Translation & Optimization** — static translation (whole binary before execution) vs. dynamic translation (at runtime), with dynamic optimizations such as reordering, inlining, loop unrolling.
  - **Virtual ISA (V‑ISA)** — an abstraction layer (added to the compiler) that lets many source ISAs run on the same host.
- **Advantages:** legacy code support, cross‑architecture compatibility, easier hardware upgrades, platform independence.
- **Challenges:** performance overhead, implementation complexity, scalability across many ISAs.

#### 2) Hardware Abstraction Layer (HAL) Level
- Virtualization is performed **directly on the physical hardware** to create and manage VMs by abstracting CPU, memory and I/O.
- Key features: **bare‑metal (Type‑1) hypervisors** that run without a host OS, resource virtualization (each VM appears to have dedicated hardware), improved hardware utilization, and isolation.
- **Advantages:** high (near‑native) performance, scalability, fault isolation, versatility (different OSes on the same hardware).

#### 3) Operating System Level
- Works at the **OS kernel layer**, creating isolated **containers / virtual environments (VEs)** that share a **single OS kernel**.
- Containers act like independent servers (own libraries, binaries, config files) but are lightweight because no hardware is emulated.
- **Advantages:** lightweight, high performance, fast scalability (start/stop/destroy quickly), process & file‑system isolation, easy deployment.
- **Disadvantages:** single‑OS limitation (a Linux host cannot run a Windows container), weaker isolation (shared kernel compromise affects all), compatibility issues with special kernel features.

#### 4) Library Support Level
- Virtualizes the interface between an application and the OS by **intercepting / emulating API calls** (also called user‑level ABI or API emulation).
- Key features: **API hooks**, controlled communication, application‑specific virtualization.
- Examples: **WINE** (Windows apps on UNIX), **WABI** (Windows→Solaris calls), **Lxrun** (Linux apps on UNIX), **Visual MainWin**, **vCUDA** (CUDA/GPU virtualization).
- Lightweight; performance depends on the accuracy/efficiency of API call remapping.

#### 5) User‑Application Level (Process‑level Virtualization)
- Virtualizes **individual applications**, each treated as an isolated process.
- Forms: **High‑Level Language (HLL) Virtual Machines** (JVM runs Java bytecode; .NET CLR runs C#/VB.NET), **application isolation/sandboxing**, and **application streaming**.
- **Advantages:** cross‑platform compatibility, improved security, simplified deployment, resource efficiency, portability.
- **Disadvantages:** performance overhead, limited scope (no full OS / multi‑user), compatibility challenges.

### Relative Merits (Textbook Table 3.1 — more "X" = higher merit, max 5)
| Level | Higher Performance | Application Flexibility | Implementation Complexity | Application Isolation |
|-------|:---:|:---:|:---:|:---:|
| ISA | X | XXXXX | XXX | XXX |
| Hardware‑level | XXXXX | XXX | XXXXX | XXXX |
| OS‑level | XXXXX | XX | XXX | XX |
| Runtime library support | XXX | XX | XX | XX |
| User‑application level | XX | XX | XXXXX | XXXXX |

**Key takeaways:** Hardware and OS support give the **highest performance**; hardware and application levels are the **most expensive to implement**; **user isolation is hardest** to achieve; **ISA offers the best application flexibility**.

> *Note on the June/July Q3c "different types of virtualization":* this can also be answered with the **three classes of VM architecture** (Hypervisor, Para‑virtualization, Host‑based) covered in **B4** below — examiners accept either the 5‑level taxonomy or the 3 architecture classes. For the L3 "taxonomy of different levels" question, the 5‑level answer above is the expected one.

---

## B2. Full Virtualization and Para‑Virtualization (incl. the Reference Model)
**(Make‑Up Q3b · June/July Q3b "reference model of full virtualization" — 2 appearances)**

Hardware virtualization is categorized into **full virtualization** and **host‑based virtualization** by implementation technology, while **para‑virtualization** is a separate compiler‑assisted approach.

### A) Full Virtualization
- The guest OS runs **without any modification**; the VMM uses **binary translation** to handle critical instructions.
- **Noncritical instructions** run directly on hardware for efficiency; **critical (sensitive) instructions** are *trapped and emulated* by the VMM.
- VMware‑type solutions place the **VMM at Ring 0** and the **guest OS at Ring 1**, completely decoupling the guest from the hardware.
- Binary translation gives compatibility but adds **performance overhead**, especially for I/O‑intensive workloads. On x86 it typically reaches **80%–97% of native speed**.

#### Reference Model of Full Virtualization (Textbook Figure 3.6)
This is exactly what the June/July Q3b asks for:
- Four privilege rings: **Ring 3** = user apps, **Ring 1** = Guest OS, **Ring 0** = VMM, all above the **host computer system hardware**.
- **User requests** (Ring 3) execute *directly* on hardware.
- **OS requests** (sensitive/privileged instructions issued by the guest) are **trapped by the VMM and handled via binary translation** before being executed on hardware.
- So the model = "direct execution of simple user instructions + indirect (binary‑translated) execution of complex OS instructions through the VMM."

### B) Host‑Based Virtualization
- A **host OS manages the hardware**; a virtualization layer is placed **on top** of the host OS to run guest OSes.
- The host OS stays in control and provides drivers/low‑level services, which simplifies deployment.
- **Drawback:** every hardware request passes through **four layers of mapping** (guest app → guest OS → virtualization layer → host OS), causing significant slowdown; if guest and host have different ISAs, binary translation further reduces performance.
- More flexible but generally **less efficient** than full virtualization with a VMM.

### C) Para‑Virtualization (with Compiler Support)
- Reduces overhead by **modifying the guest OS** to replace privileged instructions with **hypercalls** that talk directly to the hypervisor (Textbook Figures 3.7 & 3.8).
- **Guest OS modification:** kernel is modified (user apps may also need changes).
- **Hypercalls:** instructions that would normally run at Ring 0 are replaced by hypercalls to the hypervisor.
- **Intelligent compiler:** a specialized compiler identifies and replaces non‑virtualizable instructions with hypercalls to optimize performance.
- **Improved efficiency:** execution comes **closer to native performance** than full virtualization.
- **Limitation:** the modified guest OS **cannot run directly on bare hardware** without a hypervisor.

On x86, the guest OS cannot execute at Ring 0 directly; it runs at a lower privilege level and uses hypercalls to reach the hypervisor at Ring 0. Because binary translation is inefficient, **Xen, KVM and VMware ESX** all use para‑virtualization.

- **KVM (Kernel‑based Virtual Machine):** a Linux‑based para‑virtualization system (in the Linux 2.6.20 kernel); reuses Linux's memory management and scheduling, and supports unmodified guests (Windows/Linux/Solaris/UNIX) with hardware‑assisted virtualization.

### Comparison — Full vs Para Virtualization
| Aspect | Full Virtualization | Para‑Virtualization |
|--------|--------------------|---------------------|
| Guest OS | Unmodified | Modified (kernel) |
| Mechanism | Binary translation + trap/emulate | Hypercalls + intelligent compiler |
| Performance | 80–97% of native; overhead on I/O | Closer to native (lower overhead) |
| Compatibility | High (runs any guest) | Lower (only modified guests) |
| Maintenance | Low | High (kernel must be maintained) |
| Examples | VMware Workstation | Xen, KVM, VMware ESX |

### Challenges of Para‑Virtualization
1. **Compatibility & portability** — modifying the guest OS makes unmodified OS support difficult.
2. **High maintenance cost** — kernel modifications need ongoing updates.
3. **Variable performance gains** — benefit depends on workload and architecture.


---

## B3. Architecture of a Computer System Before and After Virtualization
**(Model Paper Q3a — L2, 10 marks)**

This is a diagram‑based question (Textbook Figure 3.1).

### (a) Before Virtualization — Traditional Computer
```
 +-----------------------------+
 |        Application          |
 +-----------------------------+
 |     Host Operating System   |
 +-----------------------------+
 |          Hardware           |
 |  (CPU, RAM, Disk, NIC)      |
 +-----------------------------+
```
- Only **one operating system** runs directly on the hardware, and **all applications** run on that single OS.
- If the OS crashes, **all applications stop working**.
- **Hardware usage is low / inefficient** because resources are fixed and dedicated.
- **Cannot run multiple OSes** (e.g., Windows and Linux) on the same machine.
- **No isolation** between apps — one faulty app can affect others.
- Software testing / multiple environments is not possible; adding new apps or OS may need reboot/reinstall.
- Not suitable for cloud, data centers or multi‑user environments → **less flexible and harder to manage**.

### (b) After Virtualization
```
 +-------------------+   +-------------------+
 |   Application     |   |   Application     |
 +-------------------+   +-------------------+
 |    Guest OS       |   |    Guest OS       |   } Virtual
 +-------------------+   +-------------------+   } Machines
 |  virtual devices  |   |  virtual devices  |
 +-----------------------------------------------+
 |   Virtualization Layer (Hypervisor / VMM)     |
 +-----------------------------------------------+
 |        Hardware running the Host OS           |
 +-----------------------------------------------+
```
- A **Hypervisor / Virtual Machine Monitor (VMM)** runs as the virtualization layer.
- You can create **multiple Virtual Machines (VMs)** on one physical system, each with **its own Guest OS and apps**, running independently.
- **If one VM fails, the others are unaffected** (fault isolation).
- Hardware is **better utilized** by sharing CPU/RAM/storage across VMs.
- You can run **Windows, Linux, etc. together** on the same machine.
- VMs are **easy to move, copy and back up** → ideal for testing, cloud services and hosting.
- Server load is balanced → **more flexible, cost‑saving and easy to manage**.

### Difference Table (Traditional vs Virtualized Computer)
| Feature | Traditional Computer | Virtualized Computer |
|---------|----------------------|----------------------|
| Hardware usage | Dedicated to a single OS | Shared by multiple virtual instances |
| Operating system | One OS per machine | Multiple OSes (Windows, Linux…) |
| Resource utilization | Often underutilized | Maximized (CPU/RAM/storage shared) |
| Isolation | All apps share one OS | Each VM/container isolated |
| Scalability | Needs new physical machines | Dynamically allocate without new HW |
| Security | One crash can hit whole system | Independent VMs reduce impact |
| Deployment | Manual per machine | Quick deploy/clone of VMs |
| Maintenance | Individual updates/backups | Snapshots & templates → fast recovery |
| Cost | Higher (separate machine per OS) | Lower (many VMs on fewer machines) |
| Flexibility | One system per hardware | Different OSes/apps at once |

### Benefits of Virtualization
1. Better resource utilization, 2. Cost savings, 3. Scalability & flexibility, 4. Improved security & isolation, 5. Disaster recovery (snapshots/backups).

---

## B4. Virtualization Structures / Tools and Mechanisms
**(Make‑Up Q4a — L2, 10 marks)**

There are **three typical classes of VM architecture**, differentiated by **where the virtualization layer is placed** in the system stack. The virtualization layer converts real hardware into virtual hardware so multiple OSes can run on one machine.

### The Three Classes of VM Architecture
1. **Hypervisor Architecture (Bare‑metal / VMM)**
   - Hypervisor runs **directly on the hardware** as the virtualization layer.
   - Manages and virtualizes hardware for guest OSes; supports many OS instances efficiently.
2. **Para‑Virtualization**
   - A **modified guest OS** collaborates with the virtualization layer.
   - Better performance than traditional hypervisors by reducing virtualization overhead.
3. **Host‑Based Virtualization**
   - The virtualization layer runs as an **application on top of a host OS**.
   - Easier to implement but less efficient (extra host‑OS layer).

### Hypervisor and Its Types
The hypervisor (VMM) is an intermediate layer between physical hardware (CPU, memory, disk, NIC) and the OSes, providing virtual resources via **hypercalls**.

| Type | Description | Example | Advantage |
|------|-------------|---------|-----------|
| **Micro‑kernel hypervisor** | Includes only essential, unchanging functions (memory mgmt, CPU scheduling); device drivers kept outside | Microsoft Hyper‑V | Small code size, low complexity, easy maintenance |
| **Monolithic hypervisor** | Integrates all functions incl. device drivers inside the hypervisor | VMware ESX | Comprehensive, but larger codebase |

**Key features of a hypervisor:** virtualized hardware access via hypercalls; converts physical devices into virtual resources; performs resource management & scheduling for multiple VMs.

### Xen Architecture (summarized — see B5 for detail)
Open‑source micro‑kernel hypervisor; **Dom0** (privileged) manages hardware & other domains (Domain U); VMs form a **tree‑like execution state** enabling clone/migrate/rollback.

### Binary Translation with Full Virtualization
(See **B2A**) — unmodified guest, VMM at Ring 0, guest at Ring 1, critical instructions trapped/emulated; 80–97% native speed.

### Para‑Virtualization with Compiler Support
(See **B2C**) — modified guest OS, hypercalls, intelligent compiler; near‑native performance; used by Xen, KVM, VMware ESX.

> **Answer strategy:** for a 10‑mark "virtualization structures/tools and mechanisms" question, present the **three classes**, then the **hypervisor types (micro‑kernel vs monolithic)**, the **Xen** snapshot, and a short note on **full vs para‑virtualization** with the Ring diagram.

---

## B5. What is Xen? Discuss Its Elements for Virtualization
**(June/July Q3a — L2, 6 marks)**

### What is Xen?
**Xen** is an **open‑source micro‑kernel hypervisor** developed at **Cambridge University**. It is a virtualization layer between the hardware and the operating system, enabling **multiple guest OS instances** to run simultaneously.

### Core Elements / Components
The core of a Xen system is the **hypervisor, kernel, and applications**. Its key architectural elements (Textbook Figure 3.5):

1. **Xen Hypervisor** — sits directly on the hardware devices; the basic virtualization layer.
2. **Domain 0 (Dom0)** — a **privileged guest OS** that:
   - is the **first OS to load** and has **direct hardware access**;
   - manages **hardware access, I/O operations and resource allocation** for other domains;
   - controls the entire system — therefore its security is **critical** (if Dom0 is compromised, an attacker controls **all** VMs).
3. **Domain U (Guest domains)** — **unprivileged** guest VMs (e.g., XenoLinux, XenoWindows) that run user applications and request services through Dom0.

```
 Control/IO (Domain 0)    Guest domain        Guest domain
 [App][App][App]          [App][App][App]      [App][App][App]
 +-----------+            +-----------+        +-------------+
 |  Domain0  |            | XenoLinux |        | XenoWindows |
 +------------------------------------------------------------+
 |                   XEN (Hypervisor)                          |
 +------------------------------------------------------------+
 |                     Hardware devices                        |
 +------------------------------------------------------------+
```

### Special Behaviour
- Xen lets users **create, copy, save (snapshot), migrate and roll back** VMs flexibly.
- Unlike a traditional machine with a **linear execution timeline**, Xen VMs form a **tree‑like execution state** (multiple instances + rollbacks).
- This benefits management but creates **security risks** — a VM can revert to an earlier state and **reintroduce old vulnerabilities**.

---

## B6. CPU and Memory Virtualization
**(Make‑Up Q3c — L2, 4 marks)**

### CPU Virtualization
A VM replicates a real computer, running **most instructions directly on the host CPU (native mode)** for efficiency, but **critical instructions** must be managed carefully.

**Three types of critical instructions:**
1. **Privileged instructions** — execute only in privileged mode; trigger a trap if run in user mode.
2. **Control‑sensitive instructions** — change system resources (e.g., memory configuration).
3. **Behavior‑sensitive instructions** — behave differently based on configuration (e.g., load/store under different memory modes).

**Virtualizability:**
- **RISC CPUs** (PowerPC, SPARC) are naturally virtualizable (all sensitive instructions are privileged).
- **x86 CPUs** were *not* originally virtualizable — some sensitive instructions (e.g., **SGDT, SMSW**) are **not privileged**, so they bypass the VMM and need software techniques (binary translation).

**Para‑virtualization in CPU:** a system call triggers the **80h interrupt** in the guest OS and the **82h interrupt** in the hypervisor; the hypervisor processes the call before returning control. Benefit: runs unmodified apps; drawback: small overhead.

**Hardware‑assisted CPU virtualization:** **Intel VT‑x / AMD‑V** add a new privilege mode **Ring ‑1** — the hypervisor runs at Ring ‑1, the guest OS runs at Ring 0, and all privileged instructions are **automatically trapped** into the hypervisor. This removes the need for binary translation, allows unmodified OSes, and simplifies implementation.

### Memory Virtualization
- Traditional OS uses **one‑stage mapping**: Virtual Memory → Physical Memory (page tables).
- Virtualized systems need **two‑stage mapping**:
  1. **Guest OS:** Virtual Memory → Physical Memory (guest view)
  2. **VMM:** Physical Memory → Machine Memory (actual hardware)
- The guest OS controls virtual‑to‑physical mapping but **cannot access machine memory directly**; the **VMM** handles real allocation to prevent conflicts. MMU and TLB optimize performance.

**Mechanisms:**
- **Shadow Page Table (SPT):** maintained by the VMM, maps guest‑physical → machine memory; reduces guest overhead but raises memory/performance cost.
- **Nested Page Tables (NPT):** add a hardware translation layer (first in **AMD Barcelona, 2007**); offload translation to hardware.
- **Intel EPT (Extended Page Table):** hardware‑assisted; eliminates shadow page tables, uses **VPID** to optimize TLB, translates Guest‑Physical → Host‑Physical addresses, dramatically improving performance.


---

## B7. Construct the Live‑Migration Process of a VM from One Host to Another
**(Model Paper Q4a — L3, 10 marks)**

### VM States Before Migration
- **Active** — VM is running and performing tasks.
- **Paused** — VM is created but not currently processing.
- **Suspended** — VM data is stored to disk and is inactive.

### Virtual Cluster Management Approaches
1. **Guest‑Based Manager** — cluster manager runs inside the guest OS (e.g., OpenMosix, Sun's Oasis).
2. **Host‑Based Manager** — runs on the host OS, supervising VMs (e.g., VMware HA).
3. **Independent Manager** — both guest and host have separate managers (more complex).
4. **Integrated Cluster Management** — a unified manager controls both virtual and physical resources.

### Live‑Migration Process — Six Stages (Textbook Figure 3.20)
```
 Host A (running)                         Host B
 ┌─────────────────────────────────────────────────────────┐
 │ Stage 0: Pre-Migration                                   │
 │   Active VM on Host A; alternate host preselected;       │
 │   block devices mirrored, free resources maintained      │
 ├─────────────────────────────────────────────────────────┤
 │ Stage 1: Reservation                                     │
 │   Initialize a container (resources) on the target host  │
 ├─────────────────────────────────────────────────────────┤
 │ Stage 2: Iterative Pre-Copy   (overhead due to copying)  │
 │   Enable shadow paging; copy dirty pages in successive   │
 │   rounds while the VM keeps running                      │
 ├─────────────────────────────────────────────────────────┤
 │ Stage 3: Stop and Copy        (DOWNTIME - out of service)│
 │   Suspend VM on Host A; generate ARP to redirect traffic │
 │   to Host B; synchronize remaining VM state to Host B    │
 ├─────────────────────────────────────────────────────────┤
 │ Stage 4: Commitment                                      │
 │   VM state on Host A is released                         │
 ├─────────────────────────────────────────────────────────┤
 │ Stage 5: Activation                                      │
 │   VM starts on Host B; connects to local devices;        │
 │   resumes normal operation                               │
 └─────────────────────────────────────────────────────────┘
```

**Step‑by‑step:**
1. **Stage 0 – Pre‑Migration:** VM runs on Host A; a destination Host B is preselected; block devices are mirrored and free resources are kept ready.
2. **Stage 1 – Reservation:** a container (resource reservation) for the VM is initialized on Host B.
3. **Stage 2 – Iterative Pre‑Copy:** shadow paging is enabled; the VM's memory is copied in **multiple rounds**, with changed (**dirty**) pages re‑copied. The VM **keeps running** during this phase.
4. **Stage 3 – Stop and Copy (Downtime):** the VM on Host A is **suspended**; final memory, CPU and network state is transferred; an **ARP** is generated to redirect traffic to Host B. This brief out‑of‑service period is the **downtime**.
5. **Stage 4 – Commitment:** the VM's state on Host A is released; Host A no longer controls the VM.
6. **Stage 5 – Activation:** the VM **starts on Host B**, connects to local devices and resumes normal operation.

### Performance Effects (textbook figures)
- First memory copy: **63 s**, dropping network speed from **870 MB/s → 765 MB/s**.
- Further rounds: speed drops to **694 MB/s** over **9.8 s**.
- **Total downtime is only ~165 ms**, ensuring minimal service disruption.

### Benefits of Live Migration
Continuous service availability (cloud/HPC/grids), dynamic on‑demand resource allocation, disaster recovery with minimal overhead, and prevention of resource contention. Platforms like **VMware** and **Xen** support it (Xen uses **RDMA** + dirty bitmaps + compression for efficiency).

---

## B8. Compare Physical versus Virtual Clusters
**(Model Paper Q3b — L2, 10 marks)**

### Definitions
- A **Physical Cluster** is a group of **real physical servers** connected through a network.
- A **Virtual Cluster** consists of **VMs spread across multiple physical machines**, connected via a virtual network and managed flexibly (Textbook Figure 3.18 — four virtual clusters over three physical clusters).

### Key Characteristics of Virtual Clusters
- VMs can run on physical machines and be **assigned dynamically**.
- Each VM runs **its own OS**, which can differ from the host.
- VMs improve **server utilization** by running multiple apps on the same hardware.
- VMs can be **replicated** across servers for performance, fault tolerance and disaster recovery.
- Virtual clusters can **expand or shrink dynamically** with demand.
- If a physical machine fails, **only its VMs are affected**; others continue running.

### Comparison Table
| # | Physical Clusters | Virtual Clusters |
|---|-------------------|------------------|
| 1 | Made of real physical machines on a network | Made of VMs running on one/more physical servers |
| 2 | Each node needs separate CPU/RAM/storage | Multiple VMs share the same hardware |
| 3 | High cost (more physical equipment) | Cost‑effective (fewer physical machines) |
| 4 | Large physical space & power needed | Saves space & energy |
| 5 | Hard to scale (add hardware manually) | Easy to scale (just create a VM) |
| 6 | Setup/maintenance time‑consuming | Faster setup via virtualization tools |
| 7 | One node failure may impact whole cluster | VM failures are isolated |
| 8 | Less efficient (machines may sit idle) | High efficiency (hardware shared) |
| 9 | Moving data/apps between nodes is harder | VMs move easily between servers |
| 10 | Manual monitoring of each server | Managed via software (VMware, VirtualBox) |

### Challenges in Virtual Cluster Management
1. **Fast deployment & scheduling** — quick setup/shutdown; green‑computing energy savings; load balancing (live migration adds overhead).
2. **High‑performance virtual storage** — efficient VM‑image storage; **Copy‑on‑Write (COW)** for fast VM creation from templates; automated configuration.

---

## B9. Develop an Architecture of Livewire for Intrusion Detection Using a Dedicated VM
**(Model Paper Q4b — L3, 10 marks)**

### Background — Intrusion Detection Systems (IDS)
- **Intrusion** = unauthorized access to a system by local or network users.
- An **IDS** detects such unauthorized activity. Two traditional types:
  - **HIDS (Host‑based IDS):** runs on the monitored machine — accurate but **vulnerable** if that host is attacked.
  - **NIDS (Network‑based IDS):** monitors network traffic — but **can't detect spoofed/fake actions** well.

### VM‑Based IDS
In a virtualized system, guest VMs are **isolated**, so a compromise of one VM doesn't affect others (NIDS‑like strength), while the **VMM can audit access requests** (HIDS‑like strength). It can be implemented two ways:
1. As an **independent process inside a high‑privileged VM** on the VMM.
2. **Integrated into the VMM** with direct hardware access.

### Livewire Architecture (Garfinkel & Rosenblum — Textbook Figure 3.29)
```
        IDS                                  Monitored host
 ┌─────────────────────┐            ┌──────────────────────────┐
 │   Policy engine      │            │   [APP]      [APP]        │
 │   Policy module      │            │   ┌──────────────────┐   │
 │   ┌──────────────┐   │            │   │    Guest OS      │   │
 │   │   Policy     │   │            │   └──────────────────┘   │
 │   │  framework   │   │            │      Virtual machine     │
 │   └──────────────┘   │            │                          │
 │  OS interface library│            │                          │
 └──────────┬──────────┘    PTrace   └────────────┬─────────────┘
            │                                       │
 ┌──────────┴───────────────────────────────────────┴──────────┐
 │                Virtual Machine Monitor (VMM)                  │
 └──────────────────────────────────────────────────────────────┘
```
**Components:**
- **Policy Engine** — analyzes events and applies security rules.
- **Policy Module** — uses tools like **PTrace** to trace and enforce policies on guest VMs.
- **Policy Framework** — within the policy module.
- **OS Interface Library** — lets the IDS interpret the guest OS state.
- **Monitored Host** — the guest VM (APPs + Guest OS) being observed.
- **VMM** — sits below both; the IDS monitors the guest through it.

### Operation & Limitations
- It monitors guest VMs via a **policy framework and trace‑based security enforcement**.
- Because preventing intrusions instantly is hard, **post‑attack log analysis** matters — but logs can be **untrustworthy if the OS is compromised**.

### Honeypots & Honeynets
Besides IDS, **honeypots/honeynets** trick attackers into interacting with **fake systems**. They can be **physical or virtual**; for virtual honeypots the **host OS and VMM must be protected** so guest VMs cannot attack them.


---

## B10. Virtualization for Data‑Center Automation
**(Make‑Up Q4b — L2, 10 marks)**

Data centers (Google, Amazon, Microsoft) have grown rapidly, investing in **automation** that dynamically allocates hardware, software and database resources to millions of users while ensuring cost‑effectiveness and **Quality of Service (QoS)**. The market grew from **$1.04 B (2006) to a projected $3.2 B (2011)**.

### 1) Server Consolidation
Data centers run **heterogeneous workloads**:
- **Chatty (interactive) workloads** — fluctuating demand (e.g., web/video services).
- **Noninteractive workloads** — steady demand (e.g., HPC).

Static allocation for peak demand wastes hardware, space and power. **Virtualization‑based server consolidation** reduces physical servers and improves utilization.

**Benefits of server virtualization:** higher hardware utilization, agile VM provisioning, reduced cost (servers, power, cooling, cabling), and better availability/business continuity (a VM crash doesn't affect others).

**Optimization strategies:** efficient multi‑level **resource scheduling**, **dynamic CPU allocation**, **two‑level resource management** (local VM controllers + global server controllers), and **VM‑aware power budgeting**.

### 2) Virtual Storage Management
- Storage virtualization focuses on **VM‑image management** and application data; a key problem is **storage bottlenecks** when VMs compete for disk.
- **Parallax** — a distributed/virtual storage system providing **scalable virtual disks** over a common physical device; supports **snapshots**, reduces storage footprint, uses Xen's **block tap driver/tapdisk** and a **storage appliance VM**.

### 3) Cloud OS for Virtualized Data Centers (Textbook Table 3.6)
To act as cloud providers, data centers use **VI managers / Cloud OSes**:
| Platform | License | Hypervisor | Special Feature |
|----------|---------|------------|-----------------|
| **Nimbus** | Open‑source | Xen, KVM | Virtual networks |
| **Eucalyptus** | Open‑source | Xen, KVM | Virtual networks (EC2/S3 compatible) |
| **OpenNebula** | Open‑source | Xen, KVM | Virtual networks + **dynamic provisioning** (advance reservation) |
| **vSphere 4** | Proprietary (VMware) | ESX, ESXi | **Virtual storage**, data protection (VMFS, DRM, HA) |

- **Eucalyptus** — private IaaS cloud; managers = **Instance Manager, Group Manager, Cloud Manager**; AWS‑compatible (EC2/S3); no virtual storage.
- **vSphere 4** — commercial cloud OS; **infrastructure services** = vCompute (ESX/ESXi/DRS), vStorage (VMFS, thin provisioning), vNetwork; **application services** = availability (VMotion, HA, FT), security (vShield, VMsafe), scalability (DRS, Hot Add).

### 4) Trust Management in Virtualized Data Centers
- The **VMM** is the foundation of security (isolation + hardware access control); if compromised, the whole system is at risk.
- **VM‑based IDS** combines HIDS + NIDS strengths (see B9); **honeypots/honeynets** lure attackers.
- **EMC/VMware Trusted Zones** isolate per‑tenant virtual clusters using anti‑virus, worm containment, intrusion detection and encryption.

**Conclusion:** Virtualization improves data‑center efficiency by optimizing server and storage management, reducing cost, and enabling scalable, automated, secure resource allocation.

---

## B11. Architecture of Hyper‑V and Its Use in Cloud Computing
**(June/July Q4b — L2, 6 marks)**

> The Module‑2 notes introduce Hyper‑V as the textbook example of a **micro‑kernel hypervisor**. The answer below builds on that with the standard Hyper‑V architecture expected for this question.

### What is Hyper‑V?
Microsoft **Hyper‑V** is a **Type‑1 (bare‑metal) micro‑kernel hypervisor**. Following the micro‑kernel philosophy, the hypervisor contains **only essential, unchanging functions** — physical **memory management** and **processor scheduling** — while **device drivers and other changeable components are kept outside** the hypervisor. This gives a **smaller code size, reduced complexity and easier maintainability**.

### Architecture — Partitions
Hyper‑V organizes execution into **partitions** (isolated logical units that own resources):
- **Parent (Root) Partition** — the privileged partition (analogous to Xen's Dom0). It runs the management OS, hosts the **device drivers**, creates and manages **child partitions**, and handles hardware access on their behalf.
- **Child Partitions** — the guest VMs running guest OSes and applications; they have **no direct hardware access** and request services through the parent partition.

Supporting components:
- **VMBus** — a high‑speed, in‑memory communication channel between parent and child partitions.
- **VSP / VSC** — **Virtualization Service Providers** (in the parent) and **Virtualization Service Clients** (in the child) communicate over VMBus to service I/O requests efficiently (an "enlightened"/para‑virtualized I/O path).
- **Hypercalls** — child partitions use hypercalls to request privileged operations from the hypervisor.

```
 ┌──────────────────────┐   ┌──────────────────────┐
 │  Parent Partition     │   │   Child Partition(s)  │
 │  (Management OS,       │   │   Guest OS + Apps      │
 │   device drivers, VSP) │   │   (VSC)                │
 └───────────┬───────────┘   └───────────┬───────────┘
             │      VMBus (in-memory)      │
 ┌───────────┴─────────────────────────────┴───────────┐
 │     Hyper-V Micro-kernel Hypervisor                  │
 │  (memory management + processor scheduling only)     │
 └───────────────────────────────────────────────────────┘
 ┌───────────────────────────────────────────────────────┐
 │                 Physical Hardware                      │
 └───────────────────────────────────────────────────────┘
```

### Use in Cloud Computing
- **Server consolidation & multi‑tenancy** — runs many isolated guest VMs on one physical server, raising utilization in data centers.
- **Foundation of Microsoft Azure** — Hyper‑V is the hypervisor underlying Microsoft's public cloud, providing the IaaS compute layer.
- **Live migration & high availability** — supports moving running VMs between hosts with minimal downtime, plus failover clustering for resilience.
- **Dynamic resource allocation** — features like dynamic memory and CPU scheduling let cloud workloads scale up/down on demand.
- **Isolation & security** — strong partition isolation means one tenant's VM cannot affect another's, important for multi‑tenant clouds.

---

## B12. How Is Cloud Development Different from Traditional Software Development?
**(June/July Q4a — L2, 6 marks)**

> This question is answered from the Module‑2 themes of **traditional vs virtualized computing** plus the **benefits of virtualization** that underpin the cloud model.

| Aspect | Traditional Software Development | Cloud Development |
|--------|----------------------------------|-------------------|
| **Infrastructure** | Runs on fixed, dedicated, on‑premise hardware bought up front | Runs on **virtualized, on‑demand** resources rented from a cloud provider |
| **Hardware usage** | One OS/app per machine; often **underutilized** | Many VMs/containers **share** the same hardware → high utilization |
| **Scalability** | Add new physical machines manually | **Dynamic** scale‑up/scale‑down without new hardware (elasticity) |
| **Deployment** | Manual setup/config per machine; reboot/reinstall to add apps | Quick **deploy/clone** of VMs or containers; templates & snapshots |
| **Cost model** | High capital cost (hardware, power, space per OS) | **Pay‑as‑you‑go** operating cost; fewer physical machines |
| **Isolation & fault tolerance** | One crash can affect the whole system | Each VM isolated; a VM crash does not affect others |
| **OS flexibility** | One OS per machine | Run **multiple OSes** (Windows, Linux…) on the same hardware |
| **Maintenance / recovery** | Individual updates and backups | Snapshots, templates and **live migration** allow fast recovery |
| **Maintenance responsibility** | Developer/organization manages everything | Infrastructure maintenance is **separated** and handled by the provider; the developer focuses on the application |
| **Suitability** | Single‑user / single‑machine tasks | Multi‑user, data‑center and web‑scale services |

### Key Differences (summary)
1. Cloud development **decouples application development from infrastructure maintenance** — the provider runs the data center.
2. It relies on **virtualization** for resource sharing, isolation and rapid (elastic) provisioning.
3. It uses a **utility / pay‑per‑use** economic model rather than up‑front hardware purchase.
4. It is designed for **scalability, fault tolerance and disaster recovery** through snapshots, replication and live migration — capabilities that a single traditional machine cannot easily offer.


---

# PART C — Importance Ranking (Most → Least, by Repetition)

**Method:** Each Module‑2 topic was matched across the three distinct question sets (Model Paper = Doc 2/5, Make‑Up = Doc 3, June/July = Doc 4). Topics are ranked by **how many times they appear**. Where the count ties (1 appearance), they are ordered by a secondary weight: *whether it is an official Model‑Paper question, how foundational it is, and how much other material it overlaps with.*

### Appearance Matrix
| Topic | Model Paper | Make‑Up | June/July | **Count** |
|-------|:-----------:|:-------:|:---------:|:---------:|
| Levels / Types / Taxonomy of Virtualization | – | Q3a | Q3c, Q4c | **3** |
| Full Virtualization & Para‑Virtualization (+ reference model) | – | Q3b | Q3b | **2** |
| Architecture before & after virtualization | Q3a | – | – | 1 |
| Virtualization Structures / Tools & Mechanisms | – | Q4a | – | 1 |
| Xen architecture & elements | – | – | Q3a | 1 |
| CPU & Memory Virtualization | – | Q3c | – | 1 |
| Live‑migration process of a VM | Q4a | – | – | 1 |
| Physical vs Virtual Clusters | Q3b | – | – | 1 |
| Livewire (VM‑based IDS) architecture | Q4b | – | – | 1 |
| Virtualization for Data‑Center Automation | – | Q4b | – | 1 |
| Hyper‑V architecture & cloud use | – | – | Q4b | 1 |
| Cloud dev vs Traditional software dev | – | – | Q4a | 1 |

### Final Ranked List (study in this order)

| Rank | Topic | Times Asked | Why this rank | Answer |
|:----:|-------|:-----------:|---------------|:------:|
| **1** | **Levels / Types / Taxonomy of Virtualization Implementation** | **3** | Most repeated; asked as "levels", "types" and "taxonomy" across two papers; foundational to the whole module | B1 |
| **2** | **Full Virtualization & Para‑Virtualization** | **2** | Repeated; also reappears inside the "structures/tools" question and the Ring‑level reference model | B2 |
| **3** | **Virtualization Structures / Tools & Mechanisms** | 1 | 10‑mark question that **bundles** hypervisor types + Xen + full/para — broadest coverage, high yield | B4 |
| **4** | **Architecture before & after virtualization** | 1 | Official **Model‑Paper** Q3a, diagram‑based, very commonly set, foundational | B3 |
| **5** | **CPU & Memory Virtualization** | 1 | Core Section 3.3; overlaps hardware‑assisted (VT‑x/EPT) concepts used elsewhere | B6 |
| **6** | **Xen architecture & elements** | 1 | Appears as a standalone June/July question **and** inside structures/IDS/migration topics | B5 |
| **7** | **Live‑migration process of a VM** | 1 | Official **Model‑Paper** Q4a (L3); diagram of the 6 stages is frequently set | B7 |
| **8** | **Livewire (VM‑based IDS) architecture** | 1 | Official **Model‑Paper** Q4b (L3); the *same* Figure 3.29 is reused in security topics | B9 |
| **9** | **Physical vs Virtual Clusters** | 1 | Official **Model‑Paper** Q3b; easy 10‑mark comparison table | B8 |
| **10** | **Virtualization for Data‑Center Automation** | 1 | Whole Section 3.5; large 10‑mark scope (consolidation, storage, Cloud OS, trust) | B10 |
| **11** | **Hyper‑V architecture & cloud use** | 1 | June/July only; thinly covered in notes, so lower priority but quick to learn | B11 |
| **12** | **Cloud dev vs Traditional software dev** | 1 | June/July only; can be derived from the traditional‑vs‑virtualized table — least standalone weight | B12 |

### Quick Revision Priority (the "must‑do" five)
1. **Levels / Taxonomy of Virtualization** (B1) — guaranteed‑looking, 3× repeated.
2. **Full vs Para‑Virtualization + reference model** (B2) — 2× repeated, plus reused inside other answers.
3. **Virtualization Structures/Tools/Mechanisms** (B4) — one answer covers many sub‑questions.
4. **Architecture before/after Virtualization** (B3) — model‑paper, diagram, foundational.
5. **Live‑migration of a VM** (B7) — model‑paper diagram question.

> Studying ranks 1–5 covers the highest‑probability questions; ranks 6–9 are strong model‑paper/standalone topics; ranks 10–12 round out full module coverage.

---

*End of Module‑2 study guide.*
