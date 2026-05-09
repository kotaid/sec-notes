# 05 - TCP/IP Basics: The Protocol Suite That Runs the Internet

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## The Rise of TCP/IP

Different parties developing networking software worked competitively for years — each major company had its own protocol suite handling networking jobs in its own way. After many years of competition, **TCP/IP replaced every other protocol suite** and became the industry standard.

---

## Network Layer Protocols

### IP — Internet Protocol

IP comes in two versions: **IPv4** (dominant) and **IPv6** (newer, growing).

#### IPv4 Header — Key Fields

| Field | Description |
|-------|-------------|
| **Version** | Defines the IP address type — 4 or 6 |
| **TTL (Time To Live)** | A counter starting between 128-255, decreasing every time the packet passes through a router — prevents packets from spinning indefinitely through the internet |
| **Total Length** | Size of the packet including the header — 16-bit field, limiting packets to a maximum of **65KB** |
| **Protocol** | Identifies what is encapsulated in the packet — either TCP or UDP |

### ICMP — Internet Control Messaging Protocol

- Plays a role in **IP reporting and diagnostics**
- Mostly runs automatically by software
- **Ping** uses ICMP — sending echo requests and receiving echo replies
- Operates at the network layer alongside IP

---

## Transport Layer Protocols

Data is moved in either a **connection-oriented** way using TCP, or a **connectionless** way using UDP. Application developers choose whichever suits their needs.

### TCP — Transmission Control Protocol

Requires both machines to acknowledge each other's presence and readiness before exchanging data. This is the **three-way handshake**:

1. **SYN** — sender initiates connection
2. **SYN-ACK** — receiver acknowledges and responds
3. **ACK** — sender confirms, connection established

#### TCP Header Fields

| Field | Description |
|-------|-------------|
| **Sequence Number** | Keeps track of data order between the two machines |
| **Acknowledgment Number** | Confirms receipt of data from the other side |
| **Flags** | Gives detailed information about the state of the connection (SYN, ACK, FIN, RST, etc.) |
| **Checksum** | Used by the recipient to check the TCP header for errors — lost or flipped bits |

### UDP — User Datagram Protocol

- Faster than TCP — perfect for when a bit of data loss is acceptable
- **DHCP and DNS** both use UDP
- Datagrams don't get chopped at the network layer — they just get a header
- UDP headers don't process the extra fields TCP uses to ensure data arrives intact

---

## Application Layer Protocols

Applications use the TCP/IP protocol suite to move data back and forth between machines. Every application has different needs and headers — HTTP, DNS, FTP, SMTP all live here, each built on top of either TCP or UDP depending on their reliability requirements.

---

## IP and Ethernet — How They Work Together

### ARP — Address Resolution Protocol

For a computer to know another computer's MAC address:

1. It sends an **ARP request** to the universal broadcast MAC address: `FF:FF:FF:FF:FF:FF`
2. The target machine sends an **ARP response** back through the switch
3. Once they recognize each other, they start sending **unicast Ethernet frames** directly

### IP Addressing in LANs

In a LAN, computers have two unique addresses — an **IP address** and a **MAC address**:
- IP addresses are **not fixed to the NIC** — they can be changed to suit network needs, unlike MACs
- IP allows us to divide networks into **logical groups**
- IPv4 uses **dotted decimal notation** — a shorthand for 4 dotted 8-bit numbers
- IP addresses must have **8 digits per octet** — add leading zeros to the left if needed

### IP Addressing in WANs

A WAN is a group of **interconnected LANs**. For machines to connect across LANs and WANs, IP needs to:

**1. Give every LAN a unique Network ID:**
- The network ID is the part of the IP address shared among all hosts in a LAN
- No computer can have an IP ending in **0** — reserved for network IDs
- Example: Network ID `202.120.10.0` — hosts are `202.120.10.x`
- Larger LANs use IDs with more zeros: `12.0.0.0` (supports ~16.7 million hosts)
- Before sending data, a machine compares its network ID to the destination's — match = same LAN, no match = WAN connection

**2. Connect routers with a way to send data to the right network:**
- A router that routes traffic to other networks is called the **default gateway**
- The default gateway has an IP in the same network ID as other hosts — by convention assigned the lowest or highest address
- The ISP assigns an IP for the router to connect to the internet
- The **routing table** built into the router holds instructions on what to do with data

**3. Use a subnet mask so machines recognize LAN vs WAN traffic:**
- A subnet mask is a **32-bit string of ones followed by zeros**: `255.255.255.0`
- The ones portion aligns with the network ID, the zeros portion aligns with the host ID
- The machine uses the subnet mask to divide the IP into network ID and host ID, then compares to the receiver's IP
- If they match → same LAN (direct communication)
- If they don't match → different LAN (send to default gateway)

**CIDR notation** provides a shorthand for subnet masks:
- `192.168.1.0/24` means the first 24 bits are the network ID

---

## IP Address Classes and RIRs

There are **5 RIRs (Regional Internet Registries)** that parcel IP addresses to ISPs and corporations. Users get their IP addresses from their ISPs.

Although the class system has been replaced by CIDR, techs still use it as a shorthand for private address ranges:

| Class | Range | Default Subnet Mask | Hosts |
|-------|-------|---------------------|-------|
| A | 1.0.0.0 – 126.255.255.255 | 255.0.0.0 | ~16.7 million |
| B | 128.0.0.0 – 191.255.255.255 | 255.255.0.0 | ~65,000 |
| C | 192.0.0.0 – 223.255.255.255 | 255.255.255.0 | 254 |
| D | 224.0.0.0 – 239.255.255.255 | Multicast | — |
| E | 240.0.0.0 – 255.255.255.255 | Experimental | — |

### Four Ways to Send Data

| Type | Description |
|------|-------------|
| **Broadcast** | Every computer on the LAN hears the message |
| **Unicast** | One computer sends a message directly to another |
| **Anycast** | Multiple computers share a single address — router directs to the closest one |
| **Multicast** | A single computer sends a message to a group of interested computers |

### Special IP Addresses

- `127.0.0.1` — **Loopback address** — used with ping to test a computer's network stack
- **Private IP ranges** (not routable on the internet):
  - `10.0.0.0` – `10.255.255.255` (1 Class A block)
  - `172.16.0.0` – `172.31.255.255` (16 Class B blocks)
  - `192.168.0.0` – `192.168.255.255` (256 Class C blocks)

---

## CIDR and Subnetting

**CIDR (Classless Inter-Domain Routing)** replaced the class system and allows more flexible allocation of IP addresses.

**Subnetting** allows us to take full advantage of IP addresses by chopping them into smaller logical groups (subnets).

### Why Subnet?
- **Security** — separate public computers from private ones
- **Bandwidth control** — isolate heavily used LANs from lightly used ones
- **Organization** — logical grouping of devices by function or department

### How Subnetting Works

- Always convert the subnet mask to **binary** before subnetting
- Subnet masks can go beyond standard dotted notation — `/26` and `/27` are valid
- **Formula for hosts per subnet:** `2^x − 2` where x = number of zeros in the subnet mask
- **Formula for number of subnets:** `2^y` where y = number of bits added to the subnet mask
- Subnets are always created in **powers of 2**

### Example
Network: `192.168.4.0/24` — adding 2 bits creates 4 subnets (`2^2 = 4`), each with 62 hosts (`2^6 − 2 = 62`).

---

## DHCP — Dynamic Host Configuration Protocol

DHCP is a much easier method for assigning IP addresses — it automatically assigns a unique IP to each host when it connects to the network.

### The DORA Process (Four-Way Handshake)

1. **Discover** — DHCP client broadcasts a request to any available DHCP server
2. **Offer** — Server sends an offer containing IP address, subnet mask, and gateway
3. **Request** — Client sends a request accepting the offer
4. **Acknowledge** — Server sends an ACK and records the client's IP and MAC in a database

### DHCP Server Requirements
- A **DHCP scope** — a pool of legitimate IP addresses to assign
- The **subnet mask** for the network
- The **default gateway** IP address

### DHCP Relay
When it's impractical to place a DHCP server in the same LAN as clients:
- A router accepts DHCP broadcasts and uses **UDP forwarding** to send them via unicast to the DHCP server
- Requires providing the relay device the IP address of the real DHCP server (**IP helper / UDP helper address**)
- By default, routers block broadcasts coming from outside the LAN

### IP Reservations
Devices like cameras and servers need permanent IP addresses. Two approaches:

| Approach | Description |
|----------|-------------|
| Reduce pool size | Exclude the fixed devices range from assignable addresses |
| IP exclusion range | Knock out specific addresses inside the pool |
| MAC reservation | Server leases the same address to the same device each time based on MAC |

### APIPA
If a DHCP client doesn't receive a response to a Discover message, it automatically generates an **APIPA (Automatic Private IP Addressing)** address in the `169.254.x.x` range. If you see an APIPA address, try to reestablish the lease manually.

### DHCP Redundancy
To prevent a single DHCP server from failing the network:
- Use **two independent servers** each with a range, on the same subnet and gateway
- Use **DHCP failover** — a primary and secondary server pair where one picks up if the other fails

---

## SOC Analyst Relevance

TCP/IP is the backbone of modern networking — and understanding it deeply is one of the most valuable skills a SOC analyst can have. Almost every attack leaves traces at this layer.

Anomalies in packet size and timing can be strong indicators of compromise. Unusually large ICMP packets may indicate a Ping of Death attempt. Abnormal DNS packet sizes or regular beaconing intervals in network traffic can signal DNS tunneling or C2 communication. You don't need to memorize every attack signature — but knowing what normal looks like at the protocol level makes the abnormal immediately visible.

TCP and UDP knowledge is essential for analyzing network scans. Understanding the three-way handshake allows you to recognize different Nmap scan types — a SYN scan never completes the handshake, a NULL scan sends packets with no flags set, a UDP scan probes open UDP ports. These patterns appear in network logs and packet captures constantly, and misreading them leads directly to false positives or missed detections.

Maintaining an inventory of MAC addresses and network assets pays dividends during incident response. ARP remains a common attack surface — ARP flooding, ARP spoofing, and ARP poisoning all exploit the protocol's lack of authentication. When you know what MAC addresses belong on your network, unexpected ones stand out immediately.

Reading subnets correctly is a core analytical skill. A connection from a public-facing subnet suddenly attempting to reach a critical internal subnet is a significant red flag — potentially indicating lateral movement or an advanced compromise. Attackers may also attempt to create hidden subnets to operate undetected, so keeping an accurate map of your network's subnet structure is essential for meaningful traffic analysis.

Routers and their routing tables are sometimes targeted through routing protocol poisoning — an attacker injecting false routing information into protocols like OSPF or RIP to redirect traffic through a path they control. At the local network scale this is a sophisticated but real threat, and understanding how routing tables work helps you recognize when traffic is flowing somewhere it shouldn't.

Knowing the four data transmission types — unicast, broadcast, multicast, and anycast — helps you identify atypical behavior. An attacker operating as a MITM may generate unusual traffic patterns that don't match the expected casting type for that part of the network. Unexpected multicast or broadcast traffic in the wrong context is worth investigating.

DHCP is almost universally used for IP assignment, which makes it an attractive target. DHCP spoofing allows an attacker to obtain a trusted IP address as a defense evasion technique. A rogue DHCP server can be deployed to perform man-in-the-middle attacks by directing victims to use an attacker-controlled gateway. Monitoring DHCP lease activity and flagging unexpected servers on the network is a practical defensive measure.

Finally, knowing IP classes and private address ranges helps you instantly differentiate between legitimate internal traffic and suspicious external connections. A private IP range appearing as a source in traffic that should only come from public addresses — or vice versa — is a quick and reliable anomaly indicator that every analyst should recognize on sight.

---

*Next: 06 - Routing — how data finds its way across networks.*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
