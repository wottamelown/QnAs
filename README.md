# Q-As

Quick reference guide for networking protocols, ports, and addressing.

---

## Common Protocols With Port Numbers

### Network Services & Core Protocols

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 22 | SSH / SFTP | Encrypted remote access & file transfer | TCP | Replaces Telnet |
| 23 | Telnet | Unencrypted remote access | TCP | **Deprecated** — use SSH instead |
| 25 | SMTP | Mail transfer between servers | TCP | For server-to-server relay |
| 53 | DNS | Domain name resolution | UDP / TCP | TCP for zone transfers |
| 67, 68 | DHCP | Dynamic IP address assignment | UDP | Client-server configuration |
| 69 | TFTP | Lightweight file transfer | UDP | Used by Cisco phones to retrieve config from CUCM (DHCP option 150 or 66) |
| 80 | HTTP | Web traffic (unencrypted) | TCP | Plaintext communication |
| 123 | NTP | Network time synchronization | UDP | Keeps device clocks in sync |
| 443 | HTTPS | Web traffic (encrypted) | TCP | Uses SSL/TLS encryption |
| 514 | Syslog | Centralized logging | UDP | Used by SIEM and log aggregation |

### Directory & Authentication

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 389 | LDAP | Lightweight Directory Access Protocol | UDP / TCP | Query user databases and directory services |
| 636 | LDAPs | LDAP Secure | TCP | Encrypted LDAP with SSL/TLS |

### Network Management

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 161 | SNMP | Simple Network Management Protocol | UDP | Monitor and gather statistics from network devices |

### File Sharing & Remote Access

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 445 | SMB | Server Message Block | TCP | Microsoft file sharing, printer sharing, and authentication |
| 3389 | RDP | Remote Desktop Protocol | TCP | Microsoft native remote desktop access |

### Databases

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 1433 | MSSQL | Microsoft SQL Server | TCP | Database connections |
| 3306 | MySQL | MySQL Database | TCP | Open-source relational database |
| 5432 | PostgreSQL | PostgreSQL Database | TCP | Advanced open-source relational database |
| 6379 | Redis | In-memory data store | TCP | Caching and session storage |

### Voice & Communication

| Port | Protocol | Purpose | Transport | Notes |
|------|----------|---------|-----------|-------|
| 5060, 5061 | SIP / VoIP | Session Initiation Protocol | TCP / UDP | Establish VoIP connections; 5061 is encrypted (SIPS) |

---

## IP Addresses and Subnetting

### Private IP Address Ranges (RFC 1918)

| Class | Range | Subnet Mask | CIDR | Use |
|-------|-------|-------------|------|-----|
| A | 10.0.0.0 – 10.255.255.255 | 255.0.0.0 | 10.0.0.0/8 | Large private networks |
| B | 172.16.0.0 – 172.31.255.255 | 255.255.0.0 | 172.16.0.0/12 | Medium private networks |
| C | 192.168.0.0 – 192.168.255.255 | 255.255.255.0 | 192.168.0.0/16 | Small private networks (most common) |

### CIDR Notation & Common Subnet Masks

| CIDR | Subnet Mask | Usable Hosts | Network Size |
|------|-------------|--------------|--------------|
| /24 | 255.255.255.0 | 254 | Class C network |
| /25 | 255.255.255.128 | 126 | Half of /24 |
| /26 | 255.255.255.192 | 62 | Quarter of /24 |
| /27 | 255.255.255.224 | 30 | 1/8 of /24 |
| /28 | 255.255.255.240 | 14 | 1/16 of /24 |
| /16 | 255.255.0.0 | 65,534 | Class B network |
| /8 | 255.0.0.0 | 16,777,214 | Class A network |

### Special IP Addresses

| Range | Purpose |
|-------|---------|
| 127.0.0.1 | Loopback (localhost) |
| 0.0.0.0 | Default route / all interfaces |
| 255.255.255.255 | Broadcast address | 
| 224.0.0.0 – 239.255.255.255 | Multicast |
| 240.0.0.0 – 255.255.255.255 | Reserved for future use |

### Common Networking Terms

Broadcast Address: Used by Devices to send ARP (To discover Who has this IP address?) & DHCP (Client - Server DHCP DORA)
DORA: (Discover, Offer, Request, Acknoledgement)

<img width="700" height="800" alt="image" src="https://github.com/user-attachments/assets/735fd621-5d5a-4184-b420-4b0ce66520b3" />


## Life Of A Packet

### Scenario: PC1 → Web Server (Internet)

#### 1. **DHCP IP Addressing** (Layer 2-3)
Before accessing any network, your PC must obtain an IP address, gateway, and DNS server information through the DHCP DORA process:
- **Discover:** Client broadcasts DHCP Discover packet to find a DHCP server
- **Offer:** DHCP server responds with an available IP address
- **Request:** Client requests that specific IP address
- **Acknowledgment:** Server confirms the assignment

#### 2. **DNS Resolution** (Layer 7 - Application)
You type a URL (e.g., `amazon.com`) into your browser. The browser queries a DNS server (port 53, UDP) to resolve the human-readable domain name to an IP address. If the DNS server doesn't have the record cached, it performs recursive queries to authoritative name servers.

#### 3. **ARP Resolution - Finding the Gateway MAC** (Layer 2-3)
To reach the internet, your PC needs the MAC address of the default gateway (since packets cannot travel without knowing the Layer 2 destination):
- PC sends ARP broadcast: "Who has IP 192.168.1.1?" (the default gateway)
- Gateway responds: "I have 192.168.1.1, my MAC is AA:BB:CC:DD:EE:FF"
- PC learns the gateway's MAC address and caches it in its ARP table

#### 4. **TCP Connection Establishment** (Layer 4 - Transport)
Before sending data, TCP performs a three-way handshake with the web server:
- **SYN:** PC sends a packet with SYN flag to server (54.239.28.30:443)
- **SYN-ACK:** Server responds with SYN-ACK, confirming it received the request
- **ACK:** PC acknowledges the connection
- Connection is now established; the firewall/NAT notes this connection state

#### 5. **Packet Encapsulation at Source** (Layers 7 down to 2)
Your PC builds the HTTP request and wraps it in layers:
- **Layer 7 (Application):** HTTP GET request: "GET / HTTP/1.1"
- **Layer 4 (Transport):** TCP header with source port (e.g., 54321) and destination port (443)
- **Layer 3 (Network):** IP header with source IP (192.168.1.100) and destination IP (54.239.28.30)
- **Layer 2 (Data Link):** Ethernet frame with source MAC (PC's MAC) and destination MAC (gateway's MAC)

#### 6. **Switch Forwards to Gateway** (Layer 2 - Data Link)
The PC sends the frame out to the network switch:
- Switch examines the destination MAC (AA:BB:CC:DD:EE:FF = gateway)
- Switch looks up its **MAC address table** to find which port the gateway is on
- Switch forwards the frame out the port connected to the gateway
- *(Multiple PCs on the same switch don't interfere; the switch maintains a separate MAC entry for each port)*

#### 7. **Router/Firewall Processing** (Layers 3-4)
The packet reaches your router/firewall:
- **Firewall examines the packet:** Checks if it matches outbound rules (usually allowed by default)
- **NAT Translation** (if enabled): 
  - Replaces the source IP (192.168.1.100) with the router's public IP (e.g., 203.0.113.50)
  - Replaces the source port (54321) with a new port (e.g., 65432)
  - **Router creates a NAT session entry:** `{192.168.1.100:54321} ↔ {203.0.113.50:65432}`
  - This entry is **critical for return traffic** — the router will use it to translate responses back
- Packet continues to the internet with public source IP

#### 8. **Routing Across the Internet** (Layer 3)
The packet travels through multiple routers and ISPs:
- Each router examines the destination IP (54.239.28.30)
- Each router consults its **routing table** to determine the next hop
- Routers update the **hop count (TTL)** and forward the packet
- Eventually, the packet reaches the destination web server

#### 9. **SSL/TLS Handshake** (Layer 6-7 - Encryption)
Once the TCP connection is established, SSL/TLS negotiation occurs (for HTTPS):
- **Client Hello:** PC sends supported cipher suites and TLS version
- **Server Hello:** Server responds with chosen cipher, its certificate, and public key
- **Client verifies certificate:** PC checks the certificate is signed by a trusted CA
- **Key exchange:** PC and server establish a shared encryption key
- **Finished:** Both sides confirm the handshake; encrypted tunnel is ready

#### 10. **HTTP Request Sent** (Layer 7 - Application)
The browser sends the HTTP GET request over the encrypted TLS tunnel:
- Server receives the request and processes it
- Server generates the response (HTML, CSS, images, etc.)
- Server sends the response back

---

### **RETURN TRAFFIC - How Does the Router Know Where to Send It Back?**

#### 11. **Server Sends Response** 
The web server builds the response packet:
- **Layer 7:** HTTP response with status code (200 OK) and HTML content
- **Layer 4:** TCP header with source port (443) and destination port (65432 — the port assigned by NAT)
- **Layer 3:** IP header with source IP (54.239.28.30) and destination IP (203.0.113.50 — router's public IP)
- **Layer 2:** Ethernet frame with appropriate MAC addresses for the internet route

#### 12. **Packet Travels Back Through Internet** 
The response packet is routed back through multiple routers:
- Each router examines destination IP (203.0.113.50 — your router's public IP)
- Routers forward it hop-by-hop until it reaches your ISP and then your router

#### 13. **Router/Firewall De-NAT Translation** (
Your router receives the response packet. Here's how it knows where to send it:
- **Router looks at:** Destination IP (203.0.113.50) and destination port (65432)
- **Router searches its NAT table:** Finds the entry: `{192.168.1.100:54321} ↔ {203.0.113.50:65432}`
- **Router translates back:**
  - Replaces destination IP (203.0.113.50) → 192.168.1.100 (your PC's private IP)
  - Replaces destination port (65432) → 54321 (your PC's original port)
- **Firewall allows return traffic:** Since this packet matches an existing connection state (established by the outbound SYN), the firewall permits it
- Packet is now: `Source: 54.239.28.30:443, Destination: 192.168.1.100:54321`

**Why this works with 1000s of PCs:**
- Each PC has a unique **private IP** and **port combination** (e.g., 192.168.1.100:54321, 192.168.1.101:54322, etc.)
- Router maintains a **separate NAT entry for each connection** in its NAT table
- The combination of destination IP + destination port uniquely identifies which PC to send the response to
- Router lookups are fast (hash tables) — even with 1000s of entries, the lookup is O(1)

#### 14. **Switch Routes Back to PC** 
The packet reaches your local switch with destination IP 192.168.1.100:
- Switch examines the destination MAC address (PC1's MAC address)
- Switch looks up its **MAC address table** to find which port is connected to PC1
- Example MAC table:
  - `MAC AA:BB:CC:11:22:33 → Port 1` (PC1)
  - `MAC AA:BB:CC:44:55:66 → Port 2` (PC2)`
  - `MAC AA:BB:CC:77:88:99 → Port 3` (PC3)
  - `MAC AA:BB:CC:DD:EE:FF → Port 24` (Gateway/Router)
- **Switch forwards the frame out Port 1** where PC1 is connected
- Packet is delivered to PC1

**Why this works with 1000s of PCs:**
- The switch learns MAC addresses through **dynamic MAC learning:**
  - When PC1 sends an outbound packet, switch notes: "MAC AA:BB:CC:11:22:33 is on Port 1"
  - When PC2 sends an outbound packet, switch notes: "MAC AA:BB:CC:44:55:66 is on Port 2"
  - And so on for each PC
- Switch maintains a **MAC address table** (CAM table) with entries for each connected device
- When a response comes in from the router, switch uses this table to forward to the correct port
- Each port only receives traffic destined for devices on that port — **switch prevents flooding** and keeps network efficient

#### 15. **TCP Reassembly & Application Processing** 
PC1 receives the response packet:
- **Layer 2:** Network driver strips the Ethernet frame, passes payload to Layer 3
- **Layer 3:** IP layer checks destination IP (matches local IP), passes to Layer 4
- **Layer 4:** TCP layer checks destination port (54321 matches the outbound connection), reassembles data from multiple packets if needed
- **Layer 7:** Application layer (browser) receives the complete HTTP response and renders the webpage



### My Troubleshooting Flow
-- Ping not working
-First check the phyiscal layer. Connections, cables, 
-Then check the ipconfig, ifconfig, DNS, adapter settings. 
-Then try to traceroute to multiple hops one by one. 
-Check Router ACLs or firewall policies. 

-- Slow Network (Wireshark, debug flow)

----------------------------

### Routing Protocols



## OSPF (Open Shortest Path First)

### Overview
- **Type:** Interior Gateway Protocol (IGP)
- **Standard:** Open standard (vendor-neutral)
- **Algorithm:** Link-state routing using Dijkstra algorithm
- **Administrative Distance:** 110
- **Key Feature:** End-to-end network visibility across the entire topology

### Core Concepts

**Configuration Model:**
- Interfaces are configured for OSPF, not routers directly
- Routers advertise interface reachability

**Area Architecture:**
- **Area 0 (Backbone Area):** The central hub connecting all areas; all inter-area traffic flows through it
- **Normal Area:** Accepts redistributed routes from other routing protocols (BGP, EIGRP)
- **Stub Area:** Rejects external route redistribution; reduces routing table size
- **Totally Stubby Area:** Relies on default routes only; useful for spoke networks

**Router Types:**
- **ABR (Area Border Router):** Connects Area 0 to other areas; handles inter-area route summarization

### Link State Advertisements (LSA)

LSAs are routing updates that communicate network topology. Key types:

| LSA Type | Advertised By | Purpose |
|----------|---------------|---------|
| Type 1 | All routers | Router-to-router connectivity within an area |
| Type 2 | DR (Designated Router) | Network segment information for multi-access networks |
| Type 3 | ABR | Inter-area route summarization |

### OSPF Adjacency Requirements (STTAMP)

Before two OSPF routers can exchange routing information, they must meet these criteria:

((((STTAMP))))
| Criteria | Details |
|----------|---------|
| **S**ubnet | Subnet masks must match (same network segment) |
| **T**imers | Hello and Dead timers must be identical |
| **T**ype | Multicast addresses must match (224.0.0.5 for all routers, 224.0.0.6 for DR) |
| **A**rea | Area ID and area type must match (Normal, Stub, Not-So-Stubby) |
| **M**TU | Maximum Transmission Unit must be the same (typically 1500 bytes) |
| **P**assword | Authentication passwords must match (Plain text or MD5 hash) |

### OSPF Metrics (Cost Calculation)

OSPF uses **cost** to determine the best path, not hop count.

**Default Reference Bandwidth:** 100 Mbps

**Formula:**
```
Cost = Reference Bandwidth / Interface Bandwidth
```

**Examples:**
- Fast Ethernet (100 Mbps): 100 Mbps / 100 Mbps = **1**
- Gigabit Ethernet (1000 Mbps): 100 Mbps / 1000 Mbps = **0.1** (rounded up to **1**)
- T1 (1.544 Mbps): 100 Mbps / 1.544 Mbps = **64.8** (rounded to **65**)

**Note:** For modern networks with high-speed links, increase the reference bandwidth to properly differentiate costs between 10G and 1G interfaces.


-----------------------------------------------------------------------------------------------------------------

### Frequently asked Q & As

Sorry about that. Here is the complete version with your original wording preserved, unnecessary spacing removed, and formatting optimized for a GitHub README.

Network Engineer Interview Questions & Answers

# Network Engineer Interview Questions & Answers

## Q1. Walk me through the life of a packet from a client to a web server on the Internet.

A client creates an HTTP request → browser hands data to TCP → TCP segments it and starts a connection (3-way handshake) → data goes to IP for routing → Ethernet for framing → packet hits the default gateway → router checks routing table → sends it out to ISP → traverses multiple hops (BGP-based routing) → reaches server’s network → final router forwards to server → server’s NIC receives frame → passes up to IP/TCP → delivers to the web server app.

## Q2. What is the logic behind using VLANs?

VLANs logically separate broadcast domains on a switch. They reduce broadcast traffic, improve security by isolating departments or roles, and simplify network segmentation without needing separate physical switches.

## Q3. Explain the difference between access ports and trunk ports.

* **Access port:** Carries traffic for a single VLAN. Used for end-user devices.

* **Trunk port:** Carries tagged traffic for multiple VLANs using 802.1Q tags. Used between switches, firewalls, routers, or servers with VLAN-aware NICs.

## Q4. What does a switch do when it receives a frame with an unknown destination MAC address?

It floods the frame out all ports in the VLAN except the port it arrived on. When the device replies, the switch learns the MAC and populates its CAM table.

## Q5. How do you add a VLAN to a trunk port on a switch?

On Cisco:

```
switchport trunk allowed vlan add <vlan-id>
```

Or specify a list.

Logic: Modify the allowed VLAN list so that tagged traffic for that VLAN is permitted across the trunk.

## Q6. Describe how you would design a simple, resilient office network.

* Redundant core switches in a stack or VSS/VPC.

* Distribution and access layers with dual uplinks.

* VLAN segmentation by department.

* Firewall at the edge with a HA pair.

* Redundant Internet connections.

* DHCP, DNS, and authentication centralized.

* Use spanning-tree enhancements (RSTP, root guard).

* Use routing protocols or static routes depending on size.

## Q7. How many usable IP addresses are in a /28 network range?

16 total → 14 usable (1 network address, 1 broadcast).

## Q8. Describe a complex networking problem you have solved and communicate with stakeholders.

Example:

> “We had intermittent application drops. I traced it to asymmetric routing between two firewalls during a failover. I captured packets, compared flow tables, and implemented route adjustments. I kept stakeholders updated via short status summaries and avoided technical jargon so management understood impact and timelines.”

## Q9. Explain the TCP 3-way handshake.

* **SYN:** Client requests a TCP session.

* **SYN-ACK:** Server acknowledges and agrees to session.

* **ACK:** Client acknowledges back → session established.


ICMP does not use port numbers, that was a trick question.

## Q11. Explain how you would configure firewall security policies.

* Identify zones and interfaces.

* Establish least-privilege rules: only needed ports, apps, and directions.

* Add NAT rules if required.

* Apply user IDs or groups if supported.

* Configure logging and rule comments.

* Test from low-impact users before full deployment.

* Monitor hits to ensure rules work as expected.

## Q12. How does high availability work on Palo Alto firewalls?

Two firewalls form an HA pair (active/passive or active/active). They synchronize configuration, sessions, and routing. Health-checking monitors interfaces and paths. If the active unit fails, the passive becomes active with minimal traffic disruption.

## Q13. Explain Phase 1 and Phase 2 of an IPsec VPN.

* **Phase 1 (IKE):** Establishes a secure, authenticated control channel. Negotiates encryption, hashing, DH group, lifetime. Creates the IKE SA.

* **Phase 2 (IPsec):** Negotiates data tunnel parameters (ESP/AH), encryption, selectors, lifetimes. Creates the IPsec SA used for actual traffic.

## Q14. What is Policy-Based Forwarding (PBF) and what is it used for?

PBF overrides normal routing decisions based on policy. You can force specific traffic (e.g., VoIP or backup circuits) to use a different gateway or path regardless of routing table preferences.

## Q15. Explain authentication and encryption in a wireless network.

Authentication is handled via WPA2/3 using PSK or 802.1X with RADIUS. Encryption uses AES (CCMP) to protect data between client and AP. 802.1X creates unique session keys per user.

## Q16. What is MPLS and the logic behind how it works?

MPLS forwards packets using labels instead of full IP lookups. Provider routers push/pop/swap labels. It creates predictable paths and supports VPNs, traffic engineering, and QoS. It’s faster and more flexible than pure IP routing.

## Q17. How do you monitor networks and what tools do you use?

* SNMP/NetFlow for traffic and device health.

* Syslog for event logging.

* Tools: SolarWinds, PRTG, Zabbix, Cisco DNA, Palo Alto Panorama.

* Packet captures when needed (Wireshark).

* Alerts and dashboards for proactive monitoring.

## Q18. A user could access an application yesterday but not today. How do you troubleshoot this?

Follow a layered approach:

* Check if anyone else is affected.

* Verify physical connectivity (cables, Wi-Fi, switchport).

* Check IP addressing (DHCP, VLAN changes).

* Ping gateway, then app server.

* Verify DNS resolution.

* Check firewall logs for denies.

* Review recent network changes or patches.

## Q19. Explain the difference between MTU and MSS.

* **MTU:** Maximum Layer-2 frame size (e.g., 1500 bytes for Ethernet).

* **MSS:** TCP payload size after headers. Usually MTU – 40 bytes. MSS ensures packets don’t exceed MTU to avoid fragmentation.

## Q20. What is the difference between stateful and stateless firewalls?

* **Stateful:** Tracks sessions; only allows return traffic automatically.

* **Stateless:** Only checks each packet individually against rules; doesn’t track sessions. Source IP Dest IP.

## Q21. What is NAT and why do we use it?

Network Address Translation converts private IPs to public IPs. Used for IPv4 conservation, security, and to hide internal addressing. Also used for inbound destination NATing to internal services.

## Q22. If you encounter an issue with a vendor's network device, what steps do you take?

* Gather logs, config snippets, packet captures.

* Reproduce the issue if possible.

* Check release notes or known bugs.

* Open a vendor case with detailed findings.

* Implement workarounds until a fix or patch is provided.

* Document everything for future reference.

## Q23. What is the difference between link-state and distance-vector routing protocols?

* **Link-state:** Routers share full topology info (OSPF, IS-IS). Faster convergence, more scalable.

* **Distance-vector:** Routers send route tables to neighbors (RIP). Slower convergence, simpler.

## Q24. Why might OSPF get stuck in EXSTART or 2-WAY state?

* MTU mismatch.

* Duplicate router IDs.

* Point-to-multipoint network type issues.

* DR/BDR election conditions not satisfied.

* Misconfigured network types.

## Q25. Tell me about a time you went above and beyond in your role.

Example:

> “A core switch failed after hours, impacting multiple departments. I stayed late, rebuilt the configuration from backups, repatched the stack, and validated routing. I also wrote a post-incident report so management clearly understood the root cause and corrective actions.”

## Q26. How would you prioritise between two tickets when one is a higher priority but you're already working on the other?

Pause the lower-priority ticket, update notes, and switch to the higher-priority issue. Communicate to both requestors: notify the low-priority stakeholder of the delay and give an ETA, then focus on resolving the higher-priority issue first.

### Q26. What is Port Security explain with types and violations?

Port Security is a Layer 2 Cisco switch feature that restricts access to a switch port based on MAC addresses. It prevents unauthorized devices from connecting and helps protect against MAC flooding attacks.


## Types of Secure MAC Address Learning

| Type | Description |
|------|-------------|
| **Static** | Administrator manually configures the permitted MAC address |
| **Dynamic** | Switch automatically learns MAC addresses, but they are not saved in the configuration |
| **Sticky** | Switch automatically learns MAC addresses and adds them to the running configuration; can be saved to startup configuration |

## Port Security Violation Modes

| Mode | Action |
|------|--------|
| **Protect** | Drops unauthorized frames silently |
| **Restrict** | Drops unauthorized frames and increments the violation counter; logging or SNMP notifications may occur |
| **Shutdown** ERR Disable | Places the port into an error-disabled state (default mode on many Cisco switches) |

### Q27. Difference b/w Explicit Rule & Implicit Rule?

 - Explicit rule: A rule you manually configure in the firewall to allow or deny specific traffic.
 - Implicit rule: A hidden/default rule automatically applied when no explicit rule matches.

### Q28. How STP avoids Looping?

 - Switches elect a Root Bridge.
 - Each switch selects its best path toward the Root Bridge.
 - STP puts redundant ports/links into a Blocking state.
 - If the active path fails, STP can unblock the backup path.

### Q29. How Cisco FTD is different from Cisco ASA?

Cisco ASA is the traditional Cisco stateful firewall platform, mainly focused on network security and firewall functions. Cisco FTD is Cisco's modern Next-Generation Firewall software that combines stateful firewalling with IPS, application control, URL filtering, and other advanced security features.

---

## 1. Networking Fundamentals

### Q27. What is the difference between a router and a firewall?

A router primarily forwards traffic between networks, while a firewall controls traffic based on security policies and can inspect sessions, applications, and threats.

### Q28. What is a stateful firewall?

It maintains a session table and understands the state of connections. Return traffic is allowed based on an established session rather than treating every packet independently.

### Q29. What is the difference between TCP and UDP?

TCP is connection-oriented and reliable; UDP is connectionless and faster but does not guarantee delivery.

### Q30. Explain the TCP three-way handshake.

Client sends SYN → server responds SYN-ACK → client sends ACK. The TCP session is then established.

### Q31. What happens when a user accesses https://google.com?

DNS resolves the domain → client establishes TCP connection → TLS handshake occurs → HTTP request is sent → server responds.

### Q32. What is ARP?

ARP maps an IPv4 address to a MAC address on the local network.

### Q33. What is the difference between Layer 2 and Layer 3?

Layer 2 handles frames and MAC addresses; Layer 3 handles packets and IP addresses.

### Q34. What is VLAN?

VLANs logically separate a physical switch into multiple Layer 2 networks.

### Q35. What is a trunk port?

A trunk carries traffic for multiple VLANs, usually using 802.1Q tagging.

### Q36. What is NAT?

NAT translates one IP address space into another, commonly private IPs into a public IP for Internet access.

---

## 2. Firewall Questions

### Q37. What is the difference between SNAT and DNAT?

SNAT changes the source IP, commonly for outbound Internet access. DNAT changes the destination IP, commonly for publishing internal servers.

### Q38. What is a firewall security policy?

It defines which traffic is allowed or denied based on parameters such as source, destination, service, interface, and application.

### Q39. What is the difference between deny and implicit deny?

An explicit deny is a configured rule. Implicit deny means traffic that doesn't match an allow rule is denied by default.

### Q40. What is security policy order?

Firewalls generally evaluate policies from top to bottom, and the first matching policy is applied.

### Q41. What is deep packet inspection?

It examines packet or session contents beyond basic IP and port information to identify applications, threats, or unwanted content.

### Q42. What is SSL inspection?

The firewall decrypts encrypted traffic, inspects it, and then re-encrypts it before forwarding it.

### Q43. What is IPS?

Intrusion Prevention System detects malicious traffic and can actively block or drop it.

### Q44. What is IDS?

Intrusion Detection System detects suspicious activity but normally does not block the traffic.

### Q45. What is the difference between IPS and firewall?

A firewall primarily controls access, while IPS analyzes traffic for attack patterns and malicious behavior.

### Q46. What is application control?

It identifies applications such as YouTube, BitTorrent, or Teams and allows or blocks them based on policy.

---

## 3. VPN

### Q47. What is an IPsec VPN?

IPsec provides encrypted and authenticated communication over an untrusted network such as the Internet.

### Q48. Site-to-site vs remote-access VPN?

Site-to-site connects networks together; remote-access VPN connects individual users to a corporate network.

### Q49. What is an IPsec tunnel?

It is an encrypted tunnel between two endpoints that protects traffic traveling across an untrusted network.

### Q50. What are Phase 1 and Phase 2 in IPsec?

Phase 1 establishes a secure management channel; Phase 2 negotiates the actual IPsec tunnel and traffic protection.

### Q51. What would you check if an IPsec VPN is down?

Check peer reachability, proposals, PSK/certificates, IKE settings, NAT, firewall policies, routing, and VPN logs.

### Q52. SSL VPN vs IPsec VPN?

IPsec operates at the network layer and is commonly used for site-to-site connectivity. SSL VPN commonly provides remote-user access through TLS.

---

## 4. Routing

### Q53. What is static routing?

A manually configured route that tells the device where to forward traffic.

### Q54. What is dynamic routing?

Routers automatically exchange routing information and dynamically calculate reachable networks.

### Q55. What is BGP?

BGP is a path-vector routing protocol used primarily to exchange routes between autonomous systems.

### Q56. What is an Autonomous System?

A network or group of networks under a common routing policy, identified by an ASN.

### Q57. What is the difference between eBGP and iBGP?

eBGP runs between different AS numbers; iBGP runs within the same AS.

### Q58. What is BGP mainly used for?

Internet routing, ISP connectivity, multi-homing, and exchanging routes between different organizations.

### Q59. What is SD-WAN?

SD-WAN centrally manages WAN connectivity and can dynamically select paths based on application, latency, loss, and link quality.

### Q60. What is the benefit of SD-WAN?

Better WAN visibility, centralized management, application-aware routing, and the ability to use multiple links efficiently.

---

## 5. Network Security

### Q61. What is network segmentation?

Dividing a network into separate security zones to limit communication and reduce the impact of a compromise.

### Q62. What is micro-segmentation?

More granular segmentation that applies security controls to individual workloads, applications, or hosts.

### Q63. What is DMZ?

A separate network zone used for publicly accessible services while isolating them from the internal network.

### Q64. Why should servers not be directly exposed to the Internet?

It increases the attack surface. Public services should be isolated and protected using controls such as firewalls, reverse proxies, and WAFs.

### Q65. What is a WAF?

Web Application Firewall protects web applications against attacks such as SQL injection and cross-site scripting.

### Q66. WAF vs firewall?

A network firewall primarily controls network traffic, while a WAF specifically understands and protects HTTP/HTTPS applications.

### Q67. What is Zero Trust?

Never automatically trust a user or device; continuously verify identity, device, context, and access.

### Q68. What is least privilege?

Give users and systems only the minimum access required to perform their job.

---

## 6. Authentication & Access

### Q69. What is 802.1X?

It provides port-based network access control and typically uses a supplicant, authenticator, and RADIUS server.

### Q70. What is RADIUS?

A centralized AAA protocol commonly used for network authentication and authorization.

### Q71. What is AAA?

Authentication verifies identity, Authorization determines permissions, and Accounting records activity.

### Q72. RADIUS vs TACACS+?

RADIUS is commonly used for network access authentication, while TACACS+ is widely used for administrative access to network devices and separates authentication, authorization, and accounting.

---

## 7. Troubleshooting Questions

### Q73. A user cannot access the Internet. What do you check?

Check IP configuration → gateway → DNS → routing → firewall policy → NAT → upstream connectivity.

### Q74. A user can ping 8.8.8.8 but cannot browse websites. What could be wrong?

Most likely DNS resolution, HTTP/HTTPS filtering, proxy configuration, or SSL inspection.

### Q75. A user can access internal servers but not the Internet. What do you check?

Default route, firewall policy, NAT, WAN interface, and upstream connectivity.

### Q76. Internet works by IP but not by hostname. What is the likely issue?

DNS.

### Q77. VPN is connected but the user cannot access an internal server. What do you check?

VPN routes, firewall policy, split tunneling, return route, NAT, and access rules.

### Q78. A server is reachable internally but not from the Internet. What do you check?

Public DNS, NAT/DNAT, firewall policy, server listening port, routing, and upstream firewall/security controls.

### Q79. How do you troubleshoot packet loss?

Use ping and traceroute, then check interfaces, errors, utilization, latency, WAN links, routing, and firewall logs.

### Q80. How do you troubleshoot high latency?

Check the path with traceroute, interface utilization, packet loss, WAN performance, routing, and overloaded devices.

---

## 8. Security Incident Questions

### Q81. What would you do if you detect a compromised workstation?

Isolate the endpoint, investigate logs and indicators of compromise, identify the attack vector, contain the threat, remediate, and monitor.

### Q82. What would you do if a firewall detects a critical attack?

Validate the alert, identify the source and target, block or contain the traffic if necessary, investigate logs, and escalate according to the incident process.

### Q83. What is an IOC?

Indicator of Compromise—evidence such as malicious IPs, domains, hashes, or unusual processes associated with an attack.

### Q84. What is SIEM?

A Security Information and Event Management system that collects, correlates, and analyzes logs to detect security incidents.

### Q85. Why are firewall logs important?

They help identify allowed and blocked traffic, troubleshoot connectivity, detect attacks, and investigate incidents.

---

## 9. FortiGate-Specific Questions

### Q86. What is FortiGate?

A next-generation firewall providing firewalling, VPN, IPS, application control, web filtering, SD-WAN, and other security functions.

### Q87. What is FortiManager?

A centralized management platform for managing multiple FortiGate devices and their configurations and policies.

### Q88. What is FortiAnalyzer?

A centralized logging, analytics, reporting, and security-event analysis platform for Fortinet devices.

### Q89. What is FortiGuard?

Fortinet's security intelligence service providing services such as IPS signatures, web filtering, application control, and threat intelligence.

### Q90. How do you troubleshoot a FortiGate policy issue?

Check policy order, source/destination, service, NAT, routing, logs, and use packet-flow/debug tools if required.

### Q91. What is FortiGate flow-based inspection?

Traffic is inspected as it flows through the firewall without fully reconstructing the entire session like proxy-based inspection.

### Q92. What is proxy-based inspection?

FortiGate acts as an intermediary, reconstructing and inspecting traffic before forwarding it.

### Q93. What is FortiGate SD-WAN?

It intelligently selects WAN paths based on configured rules and link-health metrics such as latency, jitter, and packet loss.

---

## 10. Scenario Questions — Very Important

These are worth practicing out loud. Senior interviews often move from definitions to scenarios.

### Q94. A branch suddenly loses connectivity to HQ. What do you check?

Check WAN link → tunnel status → routing → firewall policies → SD-WAN health → logs → ISP connectivity.

### Q95. Users complain that the Internet is slow. How do you investigate?

Check WAN utilization, packet loss, latency, firewall CPU/memory, top applications, security inspection load, and ISP performance.

### Q96. A firewall policy is allowing traffic but the application still doesn't work. Why?

Routing, NAT, return path, application dependencies, SSL inspection, DNS, or server-side issues could be involved.

### Q97. You see repeated connections from an unknown IP to an internal server. What do you do?

Identify the source, destination, port, and traffic pattern; check logs and threat intelligence, then block or contain it if malicious.

### Q98. How would you secure a new Internet-facing web server?

Place it in a DMZ, restrict firewall access, use DNAT/reverse proxy/WAF, enable monitoring and logging, harden the server, and allow only required ports.

### Q99. Management asks you to connect 100 branches securely. What would you propose?

Consider SD-WAN with centralized management, encrypted tunnels, segmentation, application-aware routing, and centralized security policies.

### Q100. How would you prevent branch-to-branch communication if branches only need to access HQ?

Use hub-and-spoke connectivity and firewall policies that allow branch-to-HQ traffic while denying branch-to-branch traffic.

### Q101. How do you approach a security design?

First understand requirements and traffic flows, identify risks, design segmentation and security controls, implement least privilege, then monitor and test.

---

## Interview Formulas to Remember

**For troubleshooting questions:**
Scope → Connectivity → Routing → Policy → NAT → Security → Logs

Code

**For security design questions:**
Requirements → Segmentation → Least Privilege → Security Controls → Monitoring → Testing

Code

**For incident questions:**
Detect → Validate → Contain → Investigate → Remediate → Monitor

Code

These structures will make your answers sound much more senior even when you keep them short.

---

## Advanced Network Security Questions

### Q102. What is a Next-Generation Firewall (NGFW)?

A firewall that combines traditional firewalling with features such as application control, IPS, web filtering, and user-based security.

### Q103. What is a security zone?

A logical network segment with a defined security level and access policy, such as LAN, DMZ, and WAN.

### Q104. Why do we use a DMZ?

To isolate Internet-facing systems from the internal network and reduce the impact of a compromise.

### Q105. What is defense in depth?

Using multiple security controls so that if one control fails, others still provide protection.

### Q106. What is a firewall's default-deny approach?

Deny all traffic by default and explicitly allow only the traffic that is required.

### Q107. What is an allowlist vs blocklist?

An allowlist permits only approved traffic; a blocklist denies known unwanted traffic.

### Q108. What is east-west traffic?

Traffic between internal systems, such as server-to-server or branch-to-branch traffic.

### Q109. What is north-south traffic?

Traffic entering or leaving the network, such as users accessing the Internet.

---

## Network Attacks

### Q110. What is a DoS attack?

An attack that attempts to make a service unavailable by exhausting its resources.

### Q111. What is a DDoS attack?

A DoS attack launched from multiple distributed sources.

### Q112. What is ARP spoofing?

An attacker sends fake ARP information to associate their MAC address with another device's IP address.

### Q113. How can you protect against ARP spoofing?

Use features such as Dynamic ARP Inspection, DHCP Snooping, and proper Layer 2 segmentation.

### Q114. What is MAC flooding?

An attack that floods a switch with fake MAC addresses to potentially force traffic flooding.

### Q115. How do you protect against MAC flooding?

Enable switch port security and limit the number of MAC addresses allowed on a port.

### Q116. What is VLAN hopping?

An attack where traffic escapes its intended VLAN and reaches another VLAN.

### Q117. How do you prevent VLAN hopping?

Disable unnecessary trunking, explicitly configure trunk ports, avoid using VLAN 1 where practical, and restrict allowed VLANs.

### Q118. What is DHCP starvation?

An attacker sends many DHCP requests to exhaust the available DHCP addresses.

### Q119. How do you prevent DHCP starvation?

Use DHCP Snooping and switch port security/rate limiting.

---

## Access Control & Network Security

### Q120. What is NAC?

Network Access Control verifies users and devices before allowing them onto the network.

### Q121. What is the purpose of 802.1X?

To authenticate a device or user before granting network access.

### Q122. What happens if 802.1X authentication fails?

The device can be denied access or placed into a restricted/quarantine VLAN depending on the configuration.

### Q123. What is MAB?

MAC Authentication Bypass allows devices that cannot perform 802.1X, such as printers, to authenticate using their MAC address.

### Q124. Why is MAB less secure than 802.1X?

MAC addresses can be spoofed, while 802.1X provides stronger identity-based authentication.

---

## Encryption & Certificates

### Q125. What is symmetric encryption?

The same key is used for encryption and decryption.

### Q126. What is asymmetric encryption?

It uses a public/private key pair.

### Q127. Why is asymmetric encryption used in TLS?

Mainly to authenticate endpoints and securely establish keys; symmetric encryption is then used for efficient data encryption.

### Q128. What is a digital certificate?

It binds an identity to a public key and is digitally signed by a trusted Certificate Authority.

### Q129. What is PKI?

Public Key Infrastructure manages certificates, keys, Certificate Authorities, and certificate trust.

### Q130. What happens during a TLS handshake?

The client and server negotiate security parameters, authenticate the server using its certificate, establish session keys, and then encrypt application traffic.

---

## IPS / IDS / Threat Detection

### Q131. What is a signature-based detection?

It identifies known threats by matching known attack patterns or signatures.

### Q132. What is anomaly-based detection?

It identifies behavior that deviates from an established normal baseline.

### Q133. What is a false positive?

Legitimate activity incorrectly identified as malicious.

### Q134. What is a false negative?

Malicious activity that the security system fails to detect.

### Q135. What is IPS tuning?

Adjusting signatures, thresholds, and policies to reduce false positives while maintaining effective detection.

---

## Proxy / Web Security

### Q136. What is a forward proxy?

It acts on behalf of internal users when they access external resources.

### Q137. What is a reverse proxy?

It sits in front of servers and handles incoming client requests before forwarding them to backend servers.

### Q138. Forward proxy vs reverse proxy?

Forward proxy protects/controls clients; reverse proxy protects and publishes backend servers.

### Q139. What is URL filtering?

Controlling access to websites based on URLs or website categories.

### Q140. Why use a proxy instead of allowing direct Internet access?

It provides centralized visibility, filtering, authentication, logging, and security inspection.

---

## DNS Security

### Q141. What is DNS poisoning?

Manipulating DNS responses so users are redirected to an incorrect or malicious destination.

### Q142. What is DNS tunneling?

Using DNS queries and responses to covertly transfer data, often for command-and-control or data exfiltration.

### Q143. How can DNS tunneling be detected?

Monitor unusual DNS query frequency, long/random domains, abnormal record types, and suspicious destinations.

### Q144. What is DNS filtering?

Blocking access to malicious or unwanted domains based on threat intelligence or category.

---

## Zero Trust & Modern Security

### Q145. What are the main principles of Zero Trust?

Verify explicitly, use least privilege, and assume breach.

### Q146. What is SASE?

A cloud-delivered architecture combining networking and security services such as SD-WAN, SWG, CASB, and ZTNA.

### Q147. What is ZTNA?

Zero Trust Network Access provides application-level access based on verified identity, device, and context rather than giving broad network access.

### Q148. ZTNA vs traditional VPN?

VPN generally provides network-level access; ZTNA provides controlled access to specific applications.

### Q149. What is CASB?

Cloud Access Security Broker provides visibility and security controls for cloud applications and services.

---

## Security Monitoring

### Q150. What is NetFlow?

It provides metadata about network conversations, such as source, destination, ports, and volume, without capturing the full packet.

### Q151. NetFlow vs packet capture?

NetFlow provides traffic metadata; packet capture provides the actual packet contents.

### Q152. What is a SOC?

A Security Operations Center monitors, detects, investigates, and responds to security incidents.

### Q153. What is SOAR?

Security Orchestration, Automation and Response automates security workflows and incident-response actions.

### Q154. What is threat intelligence?

Information about known or emerging threats, such as malicious IPs, domains, hashes, and attacker techniques.

---

## Advanced Scenario Questions

### Q155. You see a large amount of outbound traffic from one workstation. What do you check?

Identify the destination, application, user, process, traffic volume, firewall logs, and endpoint alerts to determine whether it is legitimate or data exfiltration.

### Q156. A firewall is showing thousands of blocked connections from one IP. What do you do?

Identify the source and target, check the pattern and threat intelligence, confirm whether it's scanning or an attack, and block or rate-limit it if necessary.

### Q157. An internal server is communicating with a known malicious IP. What do you do?

Isolate the server if necessary, block the destination, investigate the endpoint and firewall logs, identify the compromise, and remediate it.

### Q158. How would you secure a branch office?

Firewall at the edge, secure VPN/SD-WAN to HQ, VLAN segmentation, secure Wi-Fi, endpoint protection, least-privilege policies, logging, and centralized monitoring.

### Q159. How would you secure an Internet-facing application?

Put it behind a firewall/reverse proxy/WAF, expose only required ports, use TLS, harden the server, monitor logs, and restrict backend access.

### Q160. How would you investigate a suspected data exfiltration incident?

Identify the affected endpoint, review DNS/firewall/proxy/NetFlow logs, identify the destination and volume, inspect endpoint activity, contain the system, and preserve evidence.

### Q161. What would you check before implementing a new firewall rule?

Business requirement, source, destination, ports, protocol, direction, security zone, NAT, logging, and whether the rule can be restricted further.

### Q162. How do you make sure a firewall rule is secure?

Follow least privilege: specific source, destination, service, and users where possible, with logging enabled and unnecessary access removed.

### Q163. What is your approach when troubleshooting a security incident?

Identify → Validate → Contain → Investigate → Remediate → Monitor.

### Explain TCP Header?

 - TCP Header — Key Fields
 - Source Port: Port number of the sending application.
 - Destination Port: Port number of the receiving application.
 - Sequence Number: Identifies the position of data in the TCP stream.
 - Acknowledgment Number: Indicates the next byte the sender expects to receive.
 - Header Length (Data Offset): Indicates where the TCP data begins.
 - Flags: Control the TCP connection, such as:
 -  - SYN – Start connection
 -  - ACK – Acknowledge data
 -  - FIN – Gracefully close connection
 -  - RST – Immediately reset connection
 -  - PSH – Push data to application
 -  - URG – Urgent data
 - Checksum: Detects errors/corruption in the TCP segment.

### What is SD-WAN, explain Underlay & Overlay.

SD-WAN is a modern WAN architecture  that uses centralized management, secure overlays, application aware decision making, segmentation of the WAN traffic. 

The underlay is the underlying transport network such as MPLS, Internet, fiber or 5G, while the overlay is the logical network built on top of it, typically using encrypted tunnels. SD-WAN uses the overlay to apply policies and dynamically select the best available underlay path based on link performance.

### Explain STP

STP is spanning tree protocol, prevents loop in multiple switches or two switches having redundant links. It stops parallel redundant forwarding. There is to TTL in ethernet frames. Like packet. 

To prevent this the Switches does Election, this happens on Bridge ID of switches. Lowesr Bridge ID becomes Root Switch. 

Once the election happens, the port connected towards to the Root Switch becomes root port on other switches. 

After Election, The rest switches calculate their root ports. The one with lowest bridge ID then will win the election and have designated ports. the lowest will have blocked ports. 

---------------------------------------------------------------------------

## FortiManager

### Basic Setup

**Management Interface:**
- Port 1 is the default management interface
- Default Management IP: `192.168.1.99`

**Enable Web Access:**
config system interface show edit port1 set allowaccess http https end

Code

---

### GUI Modules Overview

| Module | Purpose |
|--------|---------|
| **Device Manager** | Add and manage devices; monitor VPN and SD-WAN; manage provisioning templates |
| **Policy & Objects** | Create firewall policies and reusable objects (IPs, FQDNs, security profiles, virtual servers) |
| **AP Manager** | Centralized management for multiple access points |
| **VPN Manager** | Configure and manage IPSec and SSL VPNs |
| **Fabric View** | Visualize physical and logical network topology |
| **FortiGuard** | Manage licenses and upgrade firmware on FortiGates |
| **FortiSwitch Manager** | Configure FortiSwitch port settings and VLAN assignments |
| **Reports** | Generate and analyze network reports |

---

### ADOMs (Administrative Domains)

**Purpose:** Create logical groupings of devices for centralized management

**Use Cases:**
- Organize by department, location, or customer
- Each ADOM has its own policies, templates, and objects
- Simplifies multi-tenant or large-scale deployments
- Enables delegation of administrative tasks

---

### Device Registration

**Three Methods:**

1. **Discovery by IP Address**
   - Discover device using its IP address
   - Enter credentials to authenticate

2. **Serial Number + IP Address**
   - Add device using its serial number
   - Then assign its IP address

3. **Bulk Import via CSV**
   - Import multiple devices at once using a CSV file
   - Reduces manual entry for large deployments

**Alternative: Fabric Connector**
- Register devices directly from FortiGate using Fabric Connectors
- Devices auto-register with FortiManager

---

### Provisioning Templates

**Template Types:**
- System templates
- SD-WAN templates
- IPSec tunnel templates
- BGP templates
- Static route templates
- CLI templates

**Workflow:**

1. **Create Individual Templates**
   - Define configuration for each template type

2. **Group Templates**
   - Combine related templates into **Template Groups**

3. **Assign to Devices**
   - Select which devices/groups receive the template group

4. **Import Option**
   - Import templates from existing FortiGate devices as a starting point
   - Modify and reuse across multiple devices

5. **Install Templates**
   - Go to Device Manager and install template group to target devices

---

### Device Groups

**Purpose:** Organize managed FortiGates for centralized policy and template management

**Setup:**
Device Groups > Managed FortiGates > Create New Group

Code

**Benefits:**
- Apply policies and templates to multiple devices simultaneously
- Simplify management of similar device types

---

### Scripts

**Two Types:**

| Type | Description | Use Case |
|------|-------------|----------|
| **CLI Scripts** | Native FortiGate CLI commands | Configuration, troubleshooting, automation |
| **TCL Scripts** | Tcl language-based automation | Advanced logic, conditional operations |

**Use Cases:**
- Zero-Touch Provisioning (ZTP): Push configurations to devices during onboarding
- Bulk configuration updates
- Device initialization automation

---

### Revision History

**Purpose:** Track configuration changes and revert to previous versions if needed

**How to Access:**
1. Go to **Device Manager**
2. Select a FortiGate device
3. Click **Device Revisions**

**What It Shows:**
- **Auto-Update:** Configuration updated automatically
- **Out Of Sync:** Device configuration differs from FortiManager
- **Modified:** Manual configuration changes made
- **Modified (recent auto updated):** Recent automatic updates applied

**Use:** Compare versions, understand change history, and rollback if necessary

---

### Policy & Objects

**Core Components:**

| Component | Description |
|-----------|-------------|
| **Policy Package** | Firewall ruleset applied to devices or device groups |
| **Objects** | Reusable elements: IP addresses, FQDNs, security profiles, virtual servers, service objects |
| **Normalized Interfaces** | Map logical interface names (e.g., LAN, WAN) to different physical interfaces per device |

**Setup Workflow:**

1. **Create Normalized Interfaces**
   - Define logical interface mappings for LAN and WAN
   - Ensures policies apply consistently across different device models

2. **Create Policy Package**
   - Define the policy container

3. **Create Firewall Policies**
   - Source and destination zones/addresses
   - Services and applications
   - Actions (Allow, Deny, etc.)
   - NAT settings if required

4. **Add Installation Targets**
   - Select which devices or device groups receive the policy

5. **Install Policy Package**
   - Deploy to target devices

**Why Normalized Interfaces Matter:**
- Devices may have different physical port names/numbers
- Normalized interfaces ensure policies work regardless of hardware differences
- Example: All "LAN" interfaces point to their respective physical interfaces per device

---

### VPN Manager

**Supported Topologies:**

| Topology | Description | Use Case |
|----------|-------------|----------|
| **Full Mesh** | Every site has a tunnel to every other site (HQ ↔ DC, HQ ↔ Site, DC ↔ Site) | Small networks with direct inter-site communication needs |
| **Star (Hub & Spoke)** | All remote sites tunnel to a central hub; sites forward traffic through the hub | Typical enterprise topology; centralized security and control |
| **Dial-Up** | Remote sites initiate tunnels to central hub on-demand | Temporary or mobile connections |

**Mesh Topology Details:**
- Every device connects to every other device
- High redundancy but complex for large deployments
- Generates many tunnels (N × (N-1) ÷ 2 tunnels for N sites)

**Star Topology Details:**
- Remote sites connect only to the central hub (HQ or data center)
- All inter-site traffic flows through the hub
- Scalable and easier to manage
- Single point of failure (hub should be redundant)

**Configuration in VPN Manager:**
- Select topology type
- Define hub (central point)
- Define spokes (remote sites)
- Configure IPSec parameters (encryption, authentication)
- Assign to device groups for deployment

---

### FortiAnalyzer

 - Once we boot into the FortiAnalyzer, we can see the following
 - FortiView: Summary of SOC, Dashboards, Network Security, System Performance.
 - Log View: Central Area to view logs from all the Foritgates or other devices.
 - Fabric View: Configure fabric connectors, view security fabric ratings.
 - FortiSOC: A subscription paid that enables playbook automation for security operations. A common FortiSOAR playbook is automated malicious-IP response. When FortiAnalyzer/FortiGate generates a security event, FortiSOAR extracts the source IP, checks its reputation, and if it's malicious, automatically blocks the IP on FortiGate, updates the incident, and notifies the SOC analyst.
 - Reports: Generate reports according to modules.

 - We need to generate the logs so we enable security sessions in firewall policy.
 - 
