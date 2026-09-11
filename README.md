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

#### 11. **Server Sends Response** (Layer 7 → Layer 2)
The web server builds the response packet:
- **Layer 7:** HTTP response with status code (200 OK) and HTML content
- **Layer 4:** TCP header with source port (443) and destination port (65432 — the port assigned by NAT)
- **Layer 3:** IP header with source IP (54.239.28.30) and destination IP (203.0.113.50 — router's public IP)
- **Layer 2:** Ethernet frame with appropriate MAC addresses for the internet route

#### 12. **Packet Travels Back Through Internet** (Layer 3)
The response packet is routed back through multiple routers:
- Each router examines destination IP (203.0.113.50 — your router's public IP)
- Routers forward it hop-by-hop until it reaches your ISP and then your router

#### 13. **Router/Firewall De-NAT Translation** (Layers 3-4) — **KEY STEP**
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

#### 14. **Switch Routes Back to PC** (Layer 2 - Data Link) — **KEY STEP**
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

#### 15. **TCP Reassembly & Application Processing** (Layers 4 → 7)
PC1 receives the response packet:
- **Layer 2:** Network driver strips the Ethernet frame, passes payload to Layer 3
- **Layer 3:** IP layer checks destination IP (matches local IP), passes to Layer 4
- **Layer 4:** TCP layer checks destination port (54321 matches the outbound connection), reassembles data from multiple packets if needed
- **Layer 7:** Application layer (browser) receives the complete HTTP response and renders the webpage

---

### **Summary: How Return Traffic Finds Its Way Back Among 1000s of PCs**

| Layer | Technology | How It Works |
|-------|------------|--------------|
| **Layer 3-4** | **NAT Table** | Router maintains entries mapping private IP:port ↔ public IP:port. When response arrives, router matches destination port and translates back to original private IP. |
| **Layer 2** | **MAC Address Table (CAM)** | Switch learns which MAC addresses are on which ports from outbound traffic. When response arrives, switch uses the destination MAC to forward to the correct port. |
| **Layer 4** | **TCP Connection State** | Firewall tracks established connections (SYN-ACK received). Return packets matching an established connection state are allowed through. |
| **Unique Identifier** | **IP:Port Combination** | Each PC-to-server connection has a unique source IP:port tuple. This uniqueness ensures return traffic targets the correct PC. |

**Key Insight:** The router doesn't need to know about all 1000 PCs individually. It only tracks **active connections** in its NAT table. When a response arrives, the destination port number uniquely identifies which internal PC should receive it.



