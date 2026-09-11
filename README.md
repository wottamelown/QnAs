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

#### 3. **ARP Resolution** (Layer 2-3)
To reach the gateway and eventually the internet, your PC needs to know the MAC address of the next hop (default gateway). It sends an ARP broadcast asking "Who has this IP address?" The gateway responds with its MAC address.

#### 4. **Layer 2/3 Travel** (Data Link & Network)
- **Switches** forward frames using the MAC address table (Layer 2)
- **Routers** use routing tables to direct packets toward their destination (Layer 3)
- The packet is encapsulated with source/destination MAC and IP headers

#### 5. **Routing to Destination** (Layer 3 & beyond)
The packet travels across multiple routers and networks, with each router examining the destination IP and forwarding accordingly using its routing table until it reaches the destination network.

#### 6. **SSL/TLS Handshake** (Layer 6-7 - Transport Security)
Once the TCP connection is established (port 443 for HTTPS), an SSL/TLS handshake occurs:
- Client sends "Client Hello" with supported cipher suites
- Server responds with "Server Hello" and its certificate
- Client verifies the certificate and exchanges encryption keys
- Secure encrypted session is established

#### 7. **HTTP Request & Response** (Layer 7 - Application)
The browser sends an HTTP GET request over the encrypted TLS tunnel. The web server processes the request and returns the requested resource (HTML, CSS, images, etc.).



