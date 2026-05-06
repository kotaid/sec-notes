# 03 - Network Topologies: How Networks Are Structured

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## What Is a Network Topology?

Topologies are **diagrams of how devices are connected**. Every network — including wireless ones — implements a topology, just in different forms. Understanding topology is understanding the skeleton of a network.

A **network technology** is the practical application of a topology combined with other critical tools — it provides the method to get data from one computer to another on a network.

---

## Old (Legacy) Topologies

These topologies are mostly obsolete today but important to understand historically and conceptually.

### Bus
- All devices are connected with a **single wire in a straight line**
- Data is sent out on the whole bus — every device sees every transmission
- Required **termination on both sides** of the cable so data is not reflected at the end
- **Critical weakness:** if the cable broke, data would be reflected bringing the **entire network down**

### Ring
- Data is moved in a **circle** — no end to the cable, so no termination needed
- **Critical weakness:** if the cable broke, data gets lost — network fails

### Star (Hub and Spoke)
- Devices connected using a **Hub as a central box**
- Offered **fault tolerance** — if one cable broke, other devices could still communicate
- Ultimately unsuccessful because it wasn't easy to redesign existing networks and was more expensive than alternatives

### Hybrid
Any form of networking technology that combines **physical topology** with a **logical topology** (how data is signaled electronically) is a hybrid topology. Two notable hybrid types:

- **Star-Ring** — shrunk the ring into a small box. Physical topology looks like a star but lost market share
- **Star-Bus** — shrunk the bus segment into a central box. This topology **reigns as the undisputed star of wired topologies** today

### Mesh
Used primarily in wireless networks, which use radio waves to connect devices:
- Each device is connected to every other device via **one or more routes**
- Some routes require traversing through another network member
- **Partially meshed** — at least two devices have redundant connections
- **Fully meshed** — every device is connected to every other device directly

### Point-to-Point
Connects **two network nodes directly** — the simplest topology possible.

---

## Logical Topologies

Logical topologies describe how data flows through a network, regardless of the physical layout.

### 3-Tier Hierarchical Model
The most common enterprise network model, consisting of three layers:

| Layer | Role |
|-------|------|
| **Core Layer** | High-speed backbone — connects to the internet, handles massive data throughput |
| **Distribution Layer** | Connects core to access layer, handles routing, policies, and filtering |
| **Access Layer** | Where end devices connect — PCs, printers, phones |

### Collapsed Core Model
- The **core and distribution layers are collapsed into a single device**
- Used in smaller networks where a full 3-tier model is unnecessary
- More cost effective but less scalable

### Spine and Leaf Topology
Modern data center topology:
- **Reduces latency and hop count**
- Traffic flows **east to west only** — every leaf connects to every spine, no spine-to-spine or leaf-to-leaf connections
- Highly predictable and scalable

---

## Network Cabling

All cables used in network technologies fall into two main categories: **copper** and **fiber optic**.

### Copper Cabling

#### Coaxial Copper
- A center wire and braided metal shield share a **common axis (centerline)** — hence "coaxial"
- The shield protects data from **EMI (electromagnetic interference)**, which can generate electricity that NICs misinterpret
- Coax cables have a **Radio Guide (RG) rating** describing characteristic impedance
- Highly resistant to EMI and physical damage but difficult to bend
- Uses **BNC (bayonet-style) connectors** — the most popular
- Modern use: connecting cable modems to ISPs using **F-type connectors**
- Cable modems connect using **RG-56 or the newer RG-6**
- Can be split using a **coax splitter** or extended using a **barrel connector**

**Coaxial Standards:**

| Rating | Ohms | Use | Connector |
|--------|------|-----|-----------|
| RG-59 | 75 | Cable TV | F Type |
| RG-6 | 75 | Cable TV | F Type |

#### Twinaxial Cable
- Two central copper conductors wrapped in a **single shield**
- Used as a substitute for short fiber connections between equipment within a rack
- This use is called a **DAC (Direct Attached Cable)**

#### Twisted Pair Cable
The **most common type of cabling** in modern networks. Pairs of cables work as a team — transmitting and receiving data. The twisting reduces **crosstalk** between pairs.

Comes in two forms:

**STP (Shielded Twisted Pairs)** — 6 types differentiated by which part is shielded:

| Name | Description |
|------|-------------|
| F/UTP | Foil shields the entire cable; inside wires are like UTP |
| S/UTP | Braid screen shields the entire cable; inside wires are like UTP |
| SF/UTP | Braid screen and foil shield the entire cable; inside wires are like UTP |
| S/FTP | Braid screen shields entire cable; foil shields each wire pair inside |
| F/FTP | Foil screen shields entire cable; foil shields each wire pair inside |
| U/FTP | No overall shielding; each pair inside is shielded with foil screens |

**UTP (Unshielded Twisted Pairs)** — twisted pairs surrounded by a plastic jacket that doesn't prevent EMI. Acts as a slight skin only.

**Twisted Pair Categories and Speed:**

| Cat Rating | Max Frequency | Max Bandwidth | Status |
|------------|---------------|---------------|--------|
| Cat 3 | 16 MHz | 16 Mbps | Recognized |
| Cat 4 | 20 MHz | 20 Mbps | No longer recognized |
| Cat 5 | 100 MHz | 100 Mbps | No longer recognized |
| Cat 5e | 100 MHz | 1 Gbps | Recognized |
| Cat 6 | 250 MHz | 10 Gbps | Recognized |
| Cat 6a | 500 MHz | 10 Gbps | Recognized |
| Cat 7 | 600 MHz | 10+ Gbps | Not recognized |
| Cat 7a | 1000 MHz | 40-100 Gbps | Not recognized |
| Cat 8 | 2000 MHz | 25-40 Gbps | Not recognized |

> For UTPs, bandwidth equals the max data throughput per second — a 10MHz cable handles 10Mbps. Modern bandwidth-efficient encoding schemes allow Cat 5e to handle up to 1000 Mbps despite a 100 MHz frequency rating.

**Connectors:**
- **RJ-11** — telephone, supports up to 2 pairs of UTP wires
- **RJ-45 (8P8C)** — current wired networks, 4-pair 8 position 8 contact connector

### Fiber Optic Cabling
- Transmits **light rather than electricity** — suitable for long distances (10s of km) and high-EMI environments
- Structure: glass fiber core → cladding (reflects light) → buffer material → insulating jacket
- Manufacturers use a **two-number designator** for core/cladding size: 9/125μm, 50/125μm, 62.5/125μm
- Two fibers connected together create **duplex fiber-optic cabling** — one for sending, one for receiving

**Two types:**

| Type | Light Source | Wavelength | Use |
|------|-------------|------------|-----|
| **MMF (Multimode Fiber)** | LED | 850nm or 1300nm | Shorter distances, data centers |
| **SMF (Single-Mode Fiber)** | Laser | 1310nm or 1550nm | Long distances, phenomenally high speeds |

> MMF uses LEDs and suffers from **modal distortion**. SMF uses laser light and prevents this, enabling much higher speeds over longer distances.

**Fiber Optic Connectors:** SC, LC, ST, MT-RJ

**Fire Ratings for Network Cables:**

| Rating | Description |
|--------|-------------|
| **PVC** | No significant fire protection, creates lots of smoke and fumes |
| **Plenum** | Named after plenum spaces (above ceilings/below floors), creates much less smoke — the standard today |
| **Riser** | For vertical runs between floors, less protection than plenum |

**Fiber Contact Types:** PC, UPC, APC — UPC and APC are more efficient.

---

## IEEE 802 Standards

The **IEEE 802 committee** defines network technology standards, split into subcommittees:

| Standard | Description |
|----------|-------------|
| IEEE 802.1 | Higher Layer LAN Protocols (including 802.1X for port-based network access control) |
| IEEE 802.3 | Ethernet (including 802.3ae for 10-Gigabit Ethernet) |
| IEEE 802.11 | Wireless LAN (Wi-Fi) |
| IEEE 802.15 | Wireless Personal Area Network (WPAN) |
| IEEE 802.18 | Radio Regulatory Technical Advisory Group |
| IEEE 802.19 | Wireless Coexistence Working Group |
| IEEE 802.24 | Vertical Applications Technical Advisory Group |

---

## SOC Analyst Relevance

While memorizing every cable specification isn't required, having a working knowledge of network infrastructure is genuinely valuable in cybersecurity.

On the offensive side, red teamers performing physical security assessments often rely on this knowledge — from crimping custom cables to planting rogue devices on a target network. On the defensive side, as the saying goes: you can't defend what you don't understand.

As a SOC analyst working in an organization with a local network, understanding its physical topology helps you detect anomalies and identify unauthorized devices. Knowing the IP ranges, subnets, and expected devices on a network makes anything unexpected stand out immediately.

Physical layer issues can also trigger security alerts in unexpected ways — a broken cable can cause ARP floods or a switch going offline, which may look like a service degradation attack if you don't understand the underlying infrastructure.

Topology knowledge also directly aids incident response. Knowing whether a network uses a flat, star, or hierarchical model tells you instantly how traffic flows and where to look during an investigation. A segmented network behaves very differently from a flat one when you're tracing lateral movement.

While network redesign falls under security engineering, a SOC analyst who understands topology can flag segmentation weaknesses during investigations — for example noting that a compromised device has unrestricted access to critical systems due to a flat network structure. That observation goes into the incident report and becomes actionable for the right team.

Ultimately, even if you don't memorize every spec, keep these as references. The real value comes from hands-on experience — building your own lab, configuring switches, understanding your network's topology — that's where knowledge becomes intuition.

---

*Next: 04 - Ethernet Basics — how Ethernet works at a deeper level.*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
