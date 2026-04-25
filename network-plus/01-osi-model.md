# 01 - The OSI Model: A SOC Analyst's Perspective

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## Why Do We Use Models in Networking?

In networking, we use models to **conceptualize the network** and divide it into simpler, discrete pieces — so we can understand it, troubleshoot it, and communicate about it effectively.

The model we rely on is the **OSI (Open Systems Interconnection)** model, a seven-layer framework that helps us:
- Determine **which layer a problem has occurred in**
- Give technicians and analysts a **shared vocabulary** to describe network behavior
- Design protocols and hardware that work **modularly** — each layer doing its job independently

---

## What Are Network Protocols?

Network protocols are **sets of rules and standards** that enable hardware and software to make devices and applications function properly at a particular layer. Each protocol lives at a specific OSI layer and only concerns itself with that layer's responsibilities.

---

## The Seven Layers of the OSI Model

The OSI model encourages **modular design** — each layer has as little to do with the operation of other layers as possible.

| Layer | Number | Name |
|-------|--------|------|
| L7 | Application | Application |
| L6 | Presentation | Presentation |
| L5 | Session | Session |
| L4 | Transport | Transport |
| L3 | Network | Network |
| L2 | Data Link | Data Link |
| L1 | Physical | Physical |

> **Memory tip:** *"Please Do Not Throw Sausage Pizza Away"* (Physical → Application, bottom up)

---

## L1 & L2 — Network Hardware Deep Dive

### Cables (L1)
- Data is transmitted between systems via **cables**
- The standard cabling solution is **UTP (Unshielded Twisted Pairs)**
  - Consists of a solid conductor core, twisted pairs, a ripcord, and an outer jacket
  - Twisting reduces electromagnetic interference between pairs

### Central Boxes (L1/L2)
- **Hubs** (older, mostly obsolete): Repeat a received frame and send it to **every** connected system. Every NIC sees the frame, but only the intended recipient processes it — inefficient and insecure.
- **Switches** (modern standard): Intelligent — they send the frame **only to the intended NIC**. This is why switches replaced hubs.

### NIC — Network Interface Card (L1/L2 boundary)
The NIC acts as the **interface between a PC and the network**:
- The cable connects to the NIC on one side, and to a Hub or Switch on the other
- Some authors place NICs in L1 (physical), others in L2 (data link) — both are reasonable depending on context

#### MAC Address
Each NIC stores a **MAC (Media Access Control) address** burnt into its firmware ROM:
- **48 bits long**, represented as **12 hex characters**
- Format: `00:40:05:E0:7D:A9`
- Also called **MAC48** or **EUI48 (Extended Unique Identifier)**

The MAC is split into two parts:
- **First 6 characters (OUI)** — Organizationally Unique Identifier, assigned to the **manufacturer**
- **Last 6 characters** — Assigned by the manufacturer to the **specific device**

> Every manufacturer requests a block of globally unique MAC addresses for their NICs, ensuring no two NICs share the same address.

#### How NICs Transmit Data
NICs send data as **electricity, light, or radio waves**. Binary represents these signals as power pulses — **1s and 0s**.

---

## Frames & Encapsulation (L2)

The NIC breaks data into chunks and **encapsulates** them into a **frame**.

### Key Concept: PDU (Protocol Data Unit)
Each layer specifies a data unit. At **Layer 2, the PDU is the Frame**.

### Ethernet Frame Structure

```
+------------------+----------------+------+--------+-----+
| Recipient's MAC  | Sender's MAC   | Type |  Data  | FCS |
+------------------+----------------+------+--------+-----+
```

| Field | Description |
|-------|-------------|
| **Recipient's MAC** | Destination NIC address |
| **Sender's MAC** | Source NIC address |
| **Type** | What is encapsulated in this frame |
| **Data** | The payload — the NIC transmits this without caring what it contains |
| **FCS (Frame Check Sequence)** | A CRC (Cyclic Redundancy Check) value used to verify data integrity |

#### How FCS Works
- The sending NIC calculates a **CRC value** from the data and appends it as the FCS
- The receiving NIC performs the **same math** on arrival
- If the result **matches** → data is good ✅
- If the result **doesn't match** → frame is dropped ❌

> The Ethernet frame holds **at most 1500 bytes** of data.

---

## MAC Address & ARP — How Systems Find Each Other

When systems communicate, they store MAC addresses so they know where to send data in the future.

**First time communication on a network:**
1. The sending system doesn't know the target's MAC address
2. It sends a **broadcast frame** with the destination `FF:FF:FF:FF:FF:FF`
3. Every system on the network processes this frame
4. The frame contains a **request for the MAC address** associated with a target IP
5. The target responds with its MAC address
6. Future communication goes **directly** to that MAC

> This process is **ARP (Address Resolution Protocol)** — mapping IP addresses to MAC addresses. Critical concept for SOC analysts when investigating lateral movement or ARP spoofing attacks.

---

## SOC Analyst Relevance

Understanding L1/L2 isn't just academic — it shows up directly in SOC work:

- **ARP Spoofing / ARP Poisoning** — An attacker sends fake ARP replies to associate their MAC with a legitimate IP, enabling MITM attacks. Knowing the ARP process helps you recognize anomalies in network logs.
- **MAC Flooding** — Overwhelming a switch's MAC table to force it to behave like a hub, exposing traffic to all ports.
- **Wireshark/packet analysis** — Frame-level inspection requires understanding Ethernet frame structure (MACs, Type field, FCS).
- **Asset identification** — OUI lookup on a MAC address can tell you the manufacturer of an unknown device on your network.

---

## Key Takeaways

- The OSI model gives us a **shared language** for network troubleshooting and security analysis
- L1 deals with **physical transmission** — cables, signals, binary
- L2 deals with **local delivery** — NICs, MACs, frames, switches
- The **Ethernet frame** is the fundamental unit of L2 communication
- **ARP** is how devices discover each other's MACs — and a common attack vector

---

*Next: 02 - OSI Model Part 2 — From Network to Application Layer*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
