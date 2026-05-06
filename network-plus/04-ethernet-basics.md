# 04 - Ethernet Basics: How Wired Networks Actually Work

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## A Brief History of Ethernet

Ethernet was developed by **Xerox**, then later handed over to **DIX (Digital Equipment Corporation / Intel / Xerox)**, who then passed it to the **IEEE 802.3 committee** — the working group that defines wired network standards to this day.

The 802.3 committee defines wired network standards that share the same basic frame type and access method. Here's a small selection of its standards timeline:

| Standard | Description | Year |
|----------|-------------|------|
| 802.3i | 10 Mbps Ethernet using twisted pair cabling | 1990 |
| 802.3ab | Gigabit Ethernet over twisted pair | 1999 |
| 802.3by | 25 Gigabit Ethernet over fiber | 2016 |
| 802.3cm | 400 Gigabit Ethernet over multimode fiber | 2020 |
| 802.3cu | 100 Gigabit and 400 Gigabit Ethernet over single mode fiber using 100 Gbps lanes | 2021 |

---

## How Ethernet Transmits Data

Ethernet uses **data frames** that contain MAC addresses to identify computers, and **CSMA/CD** to determine which machine accesses the wire at a given time.

All network technologies use frames to transmit data. This approach solves two critical problems:
- It **prevents a single machine from monopolizing** the network
- It makes **retransmitting lost data more efficient**

### Frame Structure — Building on What We Know

Building on the frame content covered in our OSI Model articles:

- **Preamble** — a 7-byte series of alternating ones and zeroes followed by a 1-byte start frame delimiter (or an 8-byte series of alternating ones and zeroes). Sent before a frame to notify the receiving NIC that a frame is coming so it knows where it starts
- **Type field** — helps the receiving machine interpret the data at a basic level — tells it if the data is IPv4 or IPv6
- **Pad** — extra data added by the sending NIC to bring the data up to the minimum frame size of **64 bytes**. Used all the time

---

## Early Ethernet — From Bus to Hybrid Star-Bus

Early networks used a **true-bus topology** — every computer on the network connected to the same cable.

After 1990, networks moved to a **hybrid-bus topology** — a hub at the center repeats data to every connected device except the one that sent it.

### 10BASE-T — The First Major Standard (1990)
The **10BASE-T** version of Ethernet created in 1990:
- **10** — 10 Mbps speed
- **BASE** — baseband signal (cable carries only one signal at a time)
- **T** — twisted pair

| Specification | Detail |
|--------------|--------|
| Speed | 10 Mbps |
| Signal type | Baseband |
| Distance | 100 meters between hub and node |
| Node limit | No more than 1024 nodes per hub |
| Topology | Star-bus (physical star, logical bus) |
| Cable type | Cat 3 or better UTP with RJ-45 connectors |

**Cabling details:**
- Required **Cat3 or higher, 2-pair UTP** — one pair to send, one to receive
- Techs installed **4 pairs** to make it future-proof
- Used **RJ-45 connectors** — 8 pins, each connected to a specific wire using **color coding**
- Two color coding standards: **TIA/EIA 568A** and **TIA/EIA 568B**

**Duplex:**
- Older NICs ran in **half-duplex** — either send or receive at a time, not both
- Modern NICs run in **full-duplex** — send and receive simultaneously

> Installing an RJ-45 connector onto a cable is called a **crimp**. The tool used is a **crimper**.

### 10BASE-FL — Fiber Option
Introduced shortly after 10BASE-T:

| Specification | Detail |
|--------------|--------|
| Speed | 10 Mbps |
| Signal type | Baseband |
| Distance | 2000 meters between hub and node |
| Node limit | No more than 1024 nodes per hub |
| Topology | Star-bus (physical star, logical bus) |
| Cable type | Multimode 62.5/125 μm (OM1) fiber-optic with ST or SC connectors |

> 10BASE-T and 10BASE-FL are both star-bus Ethernet. A **media converter** can interconnect them.

---

## CSMA/CD — Collision Detection

On bus and hybrid star-bus networks, **CSMA/CD (Carrier Multiple Access with Collision Detection)** determines which computer uses the shared cable at a given time.

**How it works:**
- Machines had **equal access** to the wire — assigned on a first-come basis
- A **collision** happened when two machines simultaneously detected the wire was free and both sent a frame — both frames got lost
- NICs **sense the overlapping signals** and both stop transmitting
- Each generates a **short random waiting time** — the one with the shorter wait sends first, the other waits for the wire to be free

> Better technologies today — specifically switches — **eliminate collisions entirely**.

---

## Ethernet Switches

Ethernet switches look like hubs but work completely differently. Here's how they operate:

1. Initially **forward all messages to every computer** (like a hub)
2. **Register every MAC address** into a MAC table
3. Start **filtering messages** — creating point-to-point connections between specific devices

Think of a switch like a phone operator — it handles every connection between two devices as if it's the only connection on the network, running at full network speed.

**Key properties:**
- Each port on a switch acts as its own **collision domain** — two nodes can send data simultaneously without collision
- Switches can **buffer frames** — store them briefly if the destination port is busy

### Uplink Ports and Crossover Cables
To avoid single points of failure and expand the network, switches have **uplink ports** specifically for connecting to other switches. **Crossover cables** can also connect switches — or even two PCs directly without a switch. In modern switches, any normal port works without special cabling.

---

## Switching Loops and STP

**Switching loops** occur when two switches are connected in a way that creates **redundant connections** between machines — causing frames to circulate endlessly.

### STP — Spanning Tree Protocol
Early switches used **STP (Spanning Tree Protocol)** to detect and prevent loops:
- Switches send frames called **BPDUs (Bridge Protocol Data Units)** to detect potential loops before they happen
- One switch acts as the **root bridge** — the center of reference
- Other switches use it as a reference point to maintain a **loop-free topology**
- If a link goes down, STP sends a **TCN (Topology Change Notification)** — enabling switches to rework themselves around the failed interface

> STP was replaced by the faster and more reliable **RSTP (Rapid Spanning Tree Protocol)**.

---

## SOC Analyst Relevance

Ethernet basics may seem like pure networking territory, but a few concepts here directly connect to SOC work.

The most practical one is switching loops and broadcast storms. When a switching loop occurs — whether from misconfiguration or a deliberate attack — it generates a massive flood of broadcast traffic that can overwhelm the network. In your SIEM, this shows up as a sudden spike in traffic that looks remarkably similar to a DoS attack. Knowing the difference between a broadcast storm and an actual attack saves you from a false positive investigation.

Understanding how switches build and maintain their MAC tables also remains relevant — as covered in our OSI Model article, MAC flooding exploits exactly this mechanism, forcing a switch to behave like a hub and exposing traffic to all ports.

Finally, STP manipulation is worth being aware of as an advanced attack vector. An attacker sending rogue BPDU frames can force themselves into the root bridge position, intercepting traffic across the network. You won't encounter this daily as a Tier 1 analyst, but recognizing it in a threat intelligence report or an incident escalated from Tier 2 requires knowing what STP is and how it works.

As always — the deeper your understanding of how the network operates normally, the faster you recognize when something is wrong.

---

*Next: 05 - Ethernet Standards — deeper into the standards that define modern networking.*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
