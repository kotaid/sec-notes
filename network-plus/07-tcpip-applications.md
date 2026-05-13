# 07 - TCP/IP Applications: The Protocols Powering the Internet

> *Part of the ClearSignal Network+ series — turning structured study notes into practical security references.*
> *— Khalil Djemai | Aspiring SOC Analyst | Ex-Full Stack Developer*

---

## Sessions and the TCP/IP Suite

All aspects and protocols that make up TCP/IP are collectively called the **TCP/IP suite** or the **Internet Protocol suite**. Any single communication between two computers is called a **session** — all sessions must begin and eventually end.

### TCP Connection Lifecycle
1. Client sends a **SYN** (synchronize) packet
2. Server responds with a **SYN-ACK** (synchronize-acknowledge) packet
3. Client sends an **ACK** (acknowledge) packet — connection established
4. Connection terminated gracefully by sending a **FIN** (finish) flag

Most TCP/IP applications use TCP because connection-oriented sessions check for errors. UDP is used for applications that don't require a connection-oriented session.

---

## Protocol Quick Reference

| Protocol | Port(s) | Transport | Purpose |
|----------|---------|-----------|---------|
| DNS | 53 | UDP/TCP | Name to IP resolution |
| DHCP | 67 (server), 68 (client) | UDP | Dynamic IP assignment |
| NTP/SNTP | 123 | UDP | Clock synchronization |
| TFTP | 69 | UDP | Simple file transfer |
| ICMP | — | IP (L3) | Error reporting, diagnostics |
| IGMP | — | IP (L3) | Multicast group management |
| Telnet | 23 | TCP | Remote CLI access (unencrypted) |
| SSH | 22 | TCP | Encrypted remote access |
| SMTP | 25 | TCP | Send email |
| POP3 | 110 | TCP | Retrieve email (legacy) |
| IMAP4 | 143 | TCP | Retrieve email (modern) |
| FTP | 20, 21 | TCP | File transfer |
| HTTP | 80 | TCP | Web traffic (unencrypted) |
| HTTPS | 443 | TCP | Web traffic (TLS encrypted) |
| MySQL | 3306 | TCP | Database |
| MS SQL Server | 1433 | TCP | Database |
| Oracle SQLnet | 1521 | TCP | Database |

---

## UDP-Based Protocols

### DNS — Domain Name System (Port 53)
Enables the use of names associated with IP addresses for devices connected to IP networks — translating human-readable domain names into IP addresses across the internet and private intranets.

### DHCP — Dynamic Host Configuration Protocol (Ports 67/68)
Dynamic IP assignment using UDP. Clients use port 68, servers use port 67. Covered in depth in the TCP/IP Basics article.

### NTP / SNTP — Network Time Protocol (Port 123)
Synchronizes clocks across network devices. Operates in a **hierarchical clock strata** model — hierarchy continues until stratum 15, after which devices are not synchronized.

### TFTP — Trivial File Transfer Protocol (Port 69)
Simple file transfer with **no authentication and no data protection**. Only used for updating software and configurations on network devices where packet loss is very unlikely.

---

## Network Layer Protocols

### ICMP — Internet Control Messaging Protocol
Works at L3 to handle low-level tasks such as host unreachable messages and router advertisements. **Ping** uses ICMP echo requests and replies.

### IGMP — Internet Group Management Protocol
Manages multicast group memberships — allows hosts and routers to establish which devices are part of a specific multicast group for efficient data delivery.

---

## Remote Access

### Telnet (Port 23)
Originally used to access remote mainframes via CLI. Can be configured with username/password but offers **absolutely no encryption**. Largely obsolete.

### SSH — Secure Shell (Port 22)
Has fully replaced Telnet for serious remote access. **Every aspect of SSH is encrypted.** The industry standard for secure terminal emulation.

---

## Email Protocols

Traditional email uses Layer 7 protocols — **SMTP** to send email, and **POP3** or **IMAP4** to retrieve it.

### POP3 — Post Office Protocol v3 (Port 110)
Downloads email from server to client. On its way out — being replaced by IMAP4.

### IMAP4 — Internet Message Access Protocol v4 (Port 143)
Preferred over POP3:
- Enables synchronization across many devices
- Supports server-side folders for organizing emails
- Features not available in POP3

> To configure an email client: enter the POP3 or IMAP4 server domain name for receiving, and the SMTP server domain name for sending.

Common mail servers: **Exim** and **Postfix**. Exim can be managed via config file, GUI like cPanel, or Microsoft Exchange.

---

## File Transfer

### FTP — File Transfer Protocol (Ports 20/21)
The original internet file transfer protocol:
- **Active mode:** client opens a port, tells server where to connect for data transfer
- **Passive mode:** server opens a port, tells client where to connect
- **No authentication by default, zero encryption**
- Active FTP uses ports 21 and 20; passive FTP uses only port 21

---

## Web Protocols

### HTTP (Port 80)
Foundation of the web. **Stateless** — holds no memory of previously executed commands, which is why technologies like AJAX exist. Transmits all data without encryption.

### HTTPS (Port 443)
HTTP secured with **TLS (Transport Layer Security)** — the latest version of SSL. Sets up an encrypted private connection. Every new web server should be using HTTPS with TLS.

---

## Port Ranges

A port number is a **16-bit value between 0 and 65,535**.

| Range | Name | Description |
|-------|------|-------------|
| 0 – 1,023 | Well-known ports | Reserved for specific TCP/IP applications |
| 1,024 – 49,151 | Registered ports | Less common apps — anyone can register with IANA |
| 49,152 – 65,535 | Ephemeral ports | Temporary client endpoints — IANA recommended range |

---

## Sessions, Sockets, and Endpoints

| Term | Definition |
|------|------------|
| **Socket / Endpoint** | Session info (IP, port, L4 protocol) held in memory on a single computer |
| **Socket Pairs / Endpoints** | Session info held in memory on both computers about the same connection |
| **Session** | The entire interconnection between two endpoints |

> Use `netstat` to see connected endpoints. Key switches: `-a` (all), `-n` (numeric), `-o` (process ID), `-b` (executable name).

---

## Identifying Good vs Bad Communications

1. Memorize known ports for common TCP/IP applications
2. Learn `netstat` switches — `-a`, `-n`, `-o`, `-b`
3. Learn the ports that normally run on your OS — most have important internal processes
4. Suspect ports not normally running — search about them, remove if malicious

---

## Common Protocol Features

Although protocols differ dramatically, most address these issues:

| Feature | Description |
|---------|-------------|
| **Flow Control** | Receiver instructs sender to speed up or slow down |
| **Packet Acknowledgment** | Return message confirming receipt of data |
| **Error Detection** | Codes to verify data wasn't damaged in transit |
| **Error Correction** | Retransmission of lost or damaged data |
| **Segmentation** | Dividing long data streams into smaller pieces |
| **Data Encryption** | Cryptographic keys protecting transmitted data |
| **Data Compression** | Reducing size by eliminating redundant information |

---

## SOC Analyst Relevance

This article covers a lot of ground — and the honest truth is that every single protocol listed here has its own family of attacks, exploitation techniques, and detection signatures. Going deep on each one is beyond the scope of this article — that's what the SOC 101 series and the lab project are for. But the foundation matters: you cannot detect what you don't understand.

Many of these protocols have been around for decades, accumulating vulnerabilities and known misconfigurations along the way. Despite that, they remain widely deployed in enterprise environments — legacy systems that can't be updated, vendor dependencies, and operational constraints keep old protocols alive. As a T1 analyst, there's a very real chance you'll be defending environments running Telnet, FTP, or outdated NTP implementations. Knowing how each protocol functions at a basic level, and being aware of its historical weaknesses, directly shapes how you triage alerts related to them.

To give you a starting point, here are the key attack categories per protocol — details will come in later series:

**TCP:** SYN scans, connect scans, FIN scans — each leaves a different trace pattern in network logs, which is exactly why knowing the handshake matters for scan detection.

**DHCP:** DHCP spoofing and poisoning — an attacker obtaining a trusted IP or deploying a rogue server to redirect traffic.

**FTP:** Brute-force, password spraying, anonymous access abuse, malicious file uploads to install backdoors, filesystem enumeration, and data exfiltration. FTP is commonly used by attackers for initial access and post-exploitation due to its lack of authentication and encryption by default.

**ICMP:** ICMP floods and ping sweeps for reconnaissance and denial of service.

**SSH:** Despite its strong security record, SSH remains a target for brute-force and password spray attacks — particularly against systems with default or weak credentials.

**NTP:** Exploitable depending on version, vulnerable to brute-force, and credentials can be revealed through MITM attacks or network traffic sniffing.

**TFTP:** Old, often running vulnerable versions. If it's not needed in your environment, it should be disabled.

**HTTP/HTTPS:** Payloads over plain HTTP can be sniffed entirely. A website is often the public face of a network and a primary initial attack vector — web attacks delivered over HTTP and HTTPS are among the most common threats a SOC analyst encounters.

One concept worth internalizing: an attack's layer determines how high up the evidence trail goes. A TCP scan operates at L4 — you'll see it in network traffic and firewall logs, but don't expect it to surface in application-level logs. An HTTP-delivered payload, operating at L7, will leave traces across almost every monitored layer — network traffic, WAF alerts, application logs, and endpoint detection solutions. Understanding this helps you know where to look and what to correlate.

Finally — knowing the correct terminology makes you a better analyst. Clear, precise technical language in reports and escalations is a professional skill, and it starts with knowing what each protocol is actually called and what it does.

---

*Next: 08 - Network Naming — DNS in depth.*

---

**Author:** Khalil Djemai — Aspiring SOC Analyst | Ex-Full Stack Developer
**Series:** ClearSignal — Network+ Notes
**Sources:** Mike Meyers — CompTIA Network+ Course (Udemy) & "CompTIA Network+ All-in-One Exam Guide" | TCM Security
**GitHub:** https://github.com/kotaid
**LinkedIn:** https://www.linkedin.com/in/khalildjemai
