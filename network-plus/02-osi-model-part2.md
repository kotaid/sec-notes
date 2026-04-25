# 02 - OSI Model Part 2: From Network to Application Layer

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## Quick Recap from Part 1

In Part 1 we established that:
- The OSI model divides networking into **7 modular layers**
- L1 handles **physical transmission** — cables, signals, binary
- L2 handles **local delivery** — NICs, MAC addresses, Ethernet frames, switches

One thing worth adding before we go up the stack:

### Data Link Sublayers (L2)
L2 is actually split into two sublayers:

| Sublayer | Name | Role |
|----------|------|------|
| **LLC** | Logical Link Control | The NIC talks to the OS via drivers — provides multiple network protocol support and flow control |
| **MAC** | Media Access Control | Creates the MAC address, attaches it to the frame along with the receiver address, checks FCS, and ensures frames are sent along the network cabling |

---

## Why MACs Aren't Enough — Enter IP Addressing

MACs work fine for **small local networks**. But as networks grow larger, broadcasting MAC addresses becomes a serious problem — it would flood and block the entire network.

For larger networks we use a **logical addressing method** that ignores hardware and allows us to break large networks into **subnets**.

This requires a **network protocol software** that:
- Creates unique identifiers for each system
- Creates rules and regulations for how data is transmitted

The most famous implementation of this is **TCP/IP (Transmission Control Protocol / Internet Protocol)**.

---

## L3 — Network Layer

### IP Addressing
- **IP** ensures a packet of data goes where it's supposed to go — it assigns an IP address to every device on the network
- IP addresses are **unique across the network**, based on **4 x 8-bit numbers separated by dots**
- Example: `192.168.4.232`
- IP addresses are stored in the system's software (unlike MACs which are burnt into hardware)

### Routing
- **Routers** use IP addresses to forward data and connect each subnet
- IP enables networks to connect across data lines that don't use Ethernet
- Each network type uses a **unique frame format**

### Frames vs Packets
At L3, we introduce a new PDU — the **Packet**:
- **Frames** encapsulate data and contain inner **Packets**
- Packets contain the information the router needs to route that data

```
[ FRAME ]
  └── Preamble | SFD | Destination | Source | Type | [ PACKET ] | FCS
                                                        └── IP Header | Data
```

### How Routing Actually Works
Each router that receives a frame:
1. **Strips the frame** off
2. Reads the packet to determine where to send the data
3. **Creates a new frame** appropriate for the next network technology
4. Forwards it to the next router

This keeps happening until the data reaches the destination subnet. At that point:
- The router strips the frame
- Creates a new frame with the **correct destination MAC address**
- The NIC strips the Ethernet frame
- Passes the remaining packet up to the OS via the driver

> This is why routers operate at L3 — they work with IP addresses, not MACs.

### Subnetting
For larger networks, IP addresses are divided into **subnets** — logical groupings of devices. Subnetting allows us to:
- Break a large network into smaller, manageable pieces
- Reduce unnecessary broadcast traffic
- Improve security by isolating network segments

> Subnetting is a deep topic on its own — dedicated writeup coming soon.

---

## L4 — Transport Layer

This is where things get really interesting from a security perspective.

### Segmentation
Data coming from upper layers is often **bigger than a frame can carry** (remember, Ethernet max is 1500 bytes). L4 handles this by **chopping data into segments** — this is called **segmentation**.

The receiving side then **reassembles the segments** in the correct order based on sequencing information in the packet headers, and verifies the data is in good shape.

L4 also **initializes requests for packets** that were not received in good order.

### TCP vs UDP

There are two main L4 protocols, and choosing between them is one of the most fundamental decisions in networking:

**TCP — Transmission Control Protocol (Connection-Oriented)**
- Before sending data, the two systems **verify they have a good connection** (the TCP handshake)
- Guarantees delivery and correct ordering
- Used where reliability matters: web browsing, file transfers, email

TCP Segment structure:
```
[ Source Port | Destination Port | Sequence Number | Acknowledgment Number | (flags & more) | Data ]
```

**UDP — User Datagram Protocol (Connectionless)**
- Connection is started **without first verifying** the other part is there
- Faster but no guarantee of delivery
- Used where speed matters more than reliability: VoIP, video streaming, DNS

UDP Datagram structure:
```
[ Source Port | Destination Port | Length | Checksum | Data ]
```

> Notice UDP has no sequence number or acknowledgment — it simply fires and forgets.

### Ports
A **port** is a logical number between **1 and 65,536** assigned to specific apps and services. The OS uses port numbers to know where to put every incoming segment from the network.

Think of it like an apartment building — the IP address gets you to the building, the port number tells you which apartment.

| Range | Type |
|-------|------|
| 0 - 1,023 | Well-known ports (HTTP: 80, HTTPS: 443, SSH: 22) |
| 1,024 - 49,151 | Registered ports |
| 49,152 - 65,535 | Dynamic / ephemeral ports |

---

## L5 — Session Layer

The Session layer manages **communication sessions** between applications on different systems.

- A **session software** handles this layer — it initiates sessions, accepts incoming sessions, and opens and closes existing sessions between applications
- Example: when you send a print job, a session is created between your system and the printer to verify it's available for that job
- **Useful command:** `netstat -a` lists all existing sessions on Windows — great for SOC investigation work

---

## L6 — Presentation Layer

The Presentation layer handles **data translation**:

- Data gets **translated** from lower layer formats into something applications can understand, and vice versa
- Handles **encoding and decoding** to present transmitted data in a readable format
- Manages **encryption and decryption** — though this layer is a bit blurry since encryption can be initiated at a lower layer and function at another

> The Presentation layer is one of the least clearly defined in practice — many of its functions overlap with L7.

---

## L7 — Application Layer

Often misunderstood — L7 doesn't refer to the applications themselves (like your browser or email client). It refers to the **OS built-in code APIs** that make applications network-aware.

- Provides additional functions such as **encryption, authentication, and tools to control data presentation**
- Acts as the interface between the network stack and the applications that use it

---

## The Complete OSI Stack with Hardware & Functions

```
Layer 7 - Application     → APIs
Layer 6 - Presentation    → Data conversion / encryption
Layer 5 - Session         → Session tracking / naming
Layer 4 - Transport       → Segmentation / reassembly (TCP/UDP)
Layer 3 - Network         → Router | IP addressing / routing
Layer 2 - Data Link       → Switch + NIC | MAC addressing / frames
Layer 1 - Physical        → Hub | Cabling / signal transmission
```

---

## Encapsulation & Decapsulation — The Full Picture

**Encapsulation** is the entire process of preparing data to travel across the network:

As data passes **down** through the OSI layers on the sending side, each layer adds its own header (and sometimes trailer) wrapping the data from the layer above.

```
Application data
    ↓ + L7/L6/L5 headers
Segment (L4 - TCP/UDP header added)
    ↓ + L4 header
Packet (L3 - IP header added)
    ↓ + L3 header
Frame (L2 - MAC addresses + FCS added)
    ↓
Bits (L1 - converted to electrical/light/radio signals)
```

**Decapsulation** is the reverse — the receiving side strips headers as data travels **up** the stack, until the original application data is delivered clean to the receiving application.

> Every layer only reads its own header and passes the rest up — this is what makes the OSI model modular and elegant.

---

## SOC Analyst Relevance

The upper layers of OSI are where most attacks actually happen:

- **L3 — IP Spoofing**: Attackers forge source IP addresses to disguise origin or bypass filters. Understanding IP addressing helps you spot inconsistencies in logs.
- **L4 — Port Scanning**: Tools like Nmap scan ports to find open services. As an analyst, unusual port activity is a key indicator of reconnaissance.
- **L4 — TCP SYN Flood (DoS)**: Attacker sends massive SYN requests without completing the handshake, exhausting server resources. Knowing TCP handshake flow helps you identify this in packet captures.
- **L4 — UDP Floods**: Similar DoS concept using UDP's connectionless nature to overwhelm targets.
- **L5 — Session Hijacking**: Attacker takes over an authenticated session. Understanding session management helps you recognize anomalous session behavior.
- **L7 — Application Layer Attacks**: SQL injection, XSS, and API abuse all live here. Your developer background gives you a serious edge in understanding these.

---

## Key Takeaways

- MACs work locally — **IP addresses** enable communication across large networks and the internet
- **Routers** strip and rebuild frames at each hop — they only care about IP addresses
- **TCP** is reliable and connection-oriented; **UDP** is fast and connectionless
- **Ports** tell the OS which application should receive incoming data
- **Encapsulation** wraps data going down the stack; **decapsulation** unwraps it going up
- Most real-world attacks target L3 and above — understanding the stack is fundamental to SOC work

---

*Next: 03 - Network Topologies — how networks are physically and logically structured.*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
