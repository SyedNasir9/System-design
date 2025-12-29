# Dynamic Host Configuration Protocol (DHCP)

**Last Updated:** 12 Dec, 2025

---

## What is DHCP?
Dynamic Host Configuration Protocol (DHCP) is a **network protocol** that automatically assigns IP addresses and other network configuration parameters to devices on a network.

Instead of manual configuration, DHCP allows devices to join a network and receive settings automatically.

### Information Provided by DHCP
- IP Address  
- Subnet Mask  
- Default Gateway  
- DNS Server addresses  
- Other TCP/IP configuration options  

**Note:**  
Automation simplifies network administration, reduces configuration errors, and enables seamless connectivity.

---

## Components of DHCP

### DHCP Server
- Maintains IP address pools
- Dynamically assigns IPs and configuration to clients
- Tracks leases and renewals

### DHCP Relay
- Forwards DHCP messages between clients and servers on different subnets
- Essential in routed networks

### DHCP Client
- Any device requesting configuration
- Examples: PC, phone, printer, VM, container host

### IP Address Pool
- Predefined range of IPs available for leasing

### Subnets
- Logical network partitions
- Help organize IP allocation

### Lease
- Time duration an IP is assigned to a client
- Must be renewed before expiration

### DNS Servers
- Provided to clients for name resolution

### Default Gateway
- Router address for external communication

### DHCP Options
- Subnet mask
- Domain name
- Time servers
- Vendor-specific settings

---

## DHCP Advanced Features

- **Renewal:** Client renews lease to keep same IP
- **Failover:** Multiple DHCP servers for redundancy
- **Dynamic DNS Updates:** Automatically updates DNS records
- **Audit Logging:** Tracks lease assignments for troubleshooting

---

## DHCP Packet Format (Key Fields)

- **Hardware Length (8 bits):** MAC address length (6 for Ethernet)
- **Hop Count (8 bits):** Maximum relay hops
- **Transaction ID (32 bits):** Matches requests and replies
- **Seconds (16 bits):** Time since client boot
- **Flags (16 bits):** Broadcast reply indicator
- **Client IP Address (4 bytes):** 0.0.0.0 if unassigned
- **Your IP Address (4 bytes):** IP assigned by server
- **Server IP Address (4 bytes):** DHCP server IP
- **Gateway IP Address (4 bytes):** Relay/router IP
- **Client Hardware Address:** MAC address
- **Server Name (64 bytes):** Optional
- **Boot Filename (128 bytes):** For diskless booting
- **Options (Variable):** Additional configuration parameters

---

## Working of DHCP

- Operates at **Application Layer**
- Uses **UDP**
  - Server: Port 67
  - Client: Port 68
- Follows **client-server model**
- Message exchange process called **DORA**

---

## DHCP Message Types (8 Total)

### 1. DHCP Discover
- Client broadcasts to find DHCP servers
- Source IP: `0.0.0.0`
- Destination IP: `255.255.255.255`
- Destination MAC: `FF:FF:FF:FF:FF:FF`

---

### 2. DHCP Offer
- Server offers an unused IP and configuration
- Includes:
  - Offered IP
  - Lease time
  - Server identifier
- Client accepts the first offer received

---

### 3. DHCP Request
- Client broadcasts acceptance of offered IP
- Performs **gratuitous ARP** to check IP conflicts
- Includes client identifier (MAC)

---

### 4. DHCP Acknowledgment (ACK)
- Server confirms IP assignment
- Binds IP to client with lease time
- IP becomes active on client

---

### 5. DHCP Negative Acknowledgment (NACK)
- Sent if requested IP is invalid
- Occurs when:
  - Pool is exhausted
  - Scope mismatch

---

### 6. DHCP Decline
- Client rejects offered IP
- Triggered if IP conflict is detected via ARP

---

### 7. DHCP Release
- Client voluntarily releases IP
- Remaining lease time is canceled

---

### 8. DHCP Inform
- Client already has a static IP
- Requests additional configuration only
- Server replies with ACK (no IP allocation)

---

## DHCP Security Concerns

- **DHCP Starvation Attack**
  - Flooding requests to exhaust IP pool

- **Rogue DHCP Servers**
  - Assign malicious gateway or DNS

- **Man-in-the-Middle Attacks**
  - Traffic interception via fake configuration

- **DNS Manipulation**
  - Redirects traffic to malicious DNS servers

---

## Protection Against DHCP Attacks

- Enable **DHCP Snooping** on switches
- Implement **Port Security**
- Use IP address filtering
- Monitor DHCP logs and traffic

---

## Key Takeaways (System Design Perspective)
- DHCP enables zero-touch network onboarding
- Critical for large-scale and dynamic environments
- Failure impacts entire subnet connectivity
- Security controls are mandatory in enterprise networks
