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

# Life of a Packet

## Scenario: PC1 → Web Server (Internet)

### 1. DHCP — IP Configuration

On a typical IPv4 network, the PC obtains its network configuration through DHCP:

**DORA:**

* **Discover** — Client broadcasts DHCP Discover.
* **Offer** — DHCP server offers an IP address.
* **Request** — Client requests the offered address.
* **ACK** — DHCP server confirms the lease.

PC receives:

* IP address
* Subnet mask
* Default gateway
* DNS server

---

### 2. DNS Resolution

User enters `https://example.com`.

The PC/browser performs a DNS query to resolve the domain name to an IP address.

```text
example.com → 54.239.28.30
```

DNS commonly uses **UDP/53**.

---

### 3. ARP — Find the Next-Hop MAC

The destination is outside the local subnet, so the PC needs the MAC address of its **default gateway**.

```text
PC → ARP Broadcast:
"Who has 192.168.1.1?"
```

Gateway responds with its MAC address.

```text
192.168.1.1 → AA:BB:CC:DD:EE:FF
```

The PC stores this in its ARP cache.

> ARP resolves **IPv4 address → MAC address** on the local network.

---

### 4. TCP 3-Way Handshake

For HTTPS, TCP establishes the connection to the server on port 443:

```text
PC → Server     SYN
PC ← Server     SYN-ACK
PC → Server     ACK
```

TCP connection is now established.

---

### 5. TLS Handshake

Because the connection is HTTPS, TLS negotiation occurs before HTTP data is exchanged.

Simplified:

```text
ClientHello
    ↓
ServerHello + Certificate
    ↓
Certificate Validation
    ↓
Key Exchange
    ↓
Session Keys Established
```

The client verifies the server certificate using its trusted CA store.

---

### 6. HTTP Request + Encapsulation

The browser now sends the HTTP request over the encrypted TLS session.

Conceptually:

```text
Layer 7  HTTP/TLS
   ↓
Layer 4  TCP
   ↓
Layer 3  IP
   ↓
Layer 2  Ethernet
```

Example:

```text
TCP:
Source Port      = 54321
Destination Port = 443

IP:
Source IP        = 192.168.1.100
Destination IP   = 54.239.28.30

Ethernet:
Source MAC       = PC1 MAC
Destination MAC  = Gateway MAC
```

---

### 7. Access Switch → Gateway

The PC sends the Ethernet frame to the access switch.

The switch:

1. Examines the destination MAC.
2. Looks up the MAC address in its MAC/CAM table.
3. Forwards the frame toward the gateway.

```text
PC1 → Access Switch → Gateway
```

The switch operates primarily at **Layer 2**.

---

### 8. Router / Firewall Processing

The gateway/router/firewall receives the packet.

It may perform:

* Firewall policy inspection
* Routing lookup
* NAT/PAT
* Security inspection

For Internet access, NAT may translate:

```text
192.168.1.100:54321
        ↓
203.0.113.50:65432
```

The firewall maintains connection/NAT state:

```text
Inside:
192.168.1.100:54321

Public:
203.0.113.50:65432

Destination:
54.239.28.30:443
```

---

### 9. Routing Across the Internet

The packet travels through multiple routers.

Each router:

* Examines the destination IP.
* Performs a routing-table lookup.
* Forwards the packet to the next hop.
* Decrements the IP TTL.

The **Layer-3 IP packet is routed hop-by-hop**, while the Layer-2 frame is replaced at each Layer-3 hop.

```text
Router → Router → Router → Web Server
```

---

# Return Traffic

### 10. Web Server Sends Response

The server sends the response back:

```text
Source:
54.239.28.30:443

Destination:
203.0.113.50:65432
```

The response travels back through the Internet toward the firewall's public IP.

---

### 11. Firewall Performs Reverse NAT

The firewall receives:

```text
54.239.28.30:443
        ↓
203.0.113.50:65432
```

It checks its NAT/state table and finds:

```text
203.0.113.50:65432
        ↓
192.168.1.100:54321
```

It translates the destination back to PC1:

```text
Source:
54.239.28.30:443

Destination:
192.168.1.100:54321
```

Because this matches an existing stateful connection, the firewall permits the return traffic.

---

### 12. Firewall → Switch → PC1

The firewall now needs to deliver the packet to `192.168.1.100`.

If necessary, it uses ARP:

```text
192.168.1.100 → PC1 MAC
```

It creates a new Ethernet frame:

```text
Source MAC:
Firewall MAC

Destination MAC:
PC1 MAC
```

The switch receives the frame and uses its MAC table:

```text
PC1 MAC → Port 1
```

Then:

```text
Firewall → Access Switch → PC1
```

---

### 13. PC Processes the Response

PC1 receives the Ethernet frame.

```text
Layer 2 → Ethernet frame processed
    ↓
Layer 3 → IP packet processed
    ↓
Layer 4 → TCP stream/reassembly
    ↓
TLS     → Data decrypted
    ↓
Layer 7 → Browser processes HTTP response
```

The browser renders the webpage.

---

# Key Interview Concepts

### ARP

```text
IPv4 Address → MAC Address
```

Used on the local network to reach the next-hop device.

### Switching

```text
Destination MAC → Switch MAC Table → Port
```

### Routing

```text
Destination IP → Routing Table → Next Hop
```

### NAT/PAT

```text
Private IP:Port
      ↓
Public IP:Translated Port
```

### Stateful Firewall

Tracks connections and allows return traffic belonging to an established session.

### Important distinction

```text
MAC = Local Layer-2 delivery
IP  = Layer-3 end-to-end addressing
Port = Layer-4 application/session identification
```

### Typical Internet flow

```text
PC
 ↓
Access Switch
 ↓
Router / Core
 ↓
Firewall
 ↓
ISP
 ↓
Internet
 ↓
Web Server
```

### Return flow

```text
Web Server
 ↓
Internet
 ↓
ISP
 ↓
Firewall
 ↓
Router / Core
 ↓
Access Switch
 ↓
PC
```





