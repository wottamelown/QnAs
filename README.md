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

<img width="936" height="1074" alt="image" src="https://github.com/user-attachments/assets/735fd621-5d5a-4184-b420-4b0ce66520b3" />


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

## Q10. Tell me the common port numbers for core services and protocols.

* HTTP — 80

* HTTPS — 443

* DNS — 53

* DHCP — 67/68

* SSH — 22

* FTP — 20/21

* SMTP — 25

* RDP — 3389

* SNMP — 161/162

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
