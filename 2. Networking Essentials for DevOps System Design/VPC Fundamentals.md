# Virtual Private Cloud (VPC)

---

## What is a VPC?
A **Virtual Private Cloud (VPC)** is a **secure, isolated private cloud** hosted within a **public cloud**.

It allows customers to:
- Run applications
- Store data
- Host websites
- Control networking

Just like a private cloud, but **without owning physical infrastructure**.

---

## Simple Analogy
- **Public cloud:** A crowded restaurant  
- **VPC:** A reserved table inside that restaurant  

Same building, same kitchen, but **only you can sit at your table**.

---

## Public Cloud vs Private Cloud

### Public Cloud
- Shared infrastructure
- Multiple customers (multitenancy)
- Data is isolated, hardware is shared
- Examples:
  - AWS
  - Google Cloud
  - Microsoft Azure

### Private Cloud
- Single tenant
- Dedicated infrastructure
- Used by one organization only

### Virtual Private Cloud (VPC)
- A **private cloud inside a public cloud**
- Single-tenant at the network level
- Combines **scalability + isolation**

---

## How is a VPC Isolated?

VPC isolation is achieved using multiple networking technologies:

### Subnets (Layer 3)
- Reserved ranges of **private IP addresses**
- Not accessible from the public Internet
- Used to segment workloads (public vs private subnets)

### VLAN (Layer 2)
- Virtual Local Area Network
- Logical separation at the data link layer
- Prevents traffic leakage between tenants

### VPN
- Encrypted tunnel over the public Internet
- Securely connects:
  - On-prem → VPC
  - User → VPC
- Protects data in transit

---

## Additional VPC Networking Features

### Network Address Translation (NAT)
- Maps private IPs → public IPs
- Allows outbound Internet access
- Enables hosting public-facing apps securely

### BGP Route Configuration
- Custom routing between:
  - VPCs
  - On-prem networks
  - Other clouds
- Used in hybrid and multi-cloud setups

---

## Advantages of Using a VPC

### Scalability
- Resources can be added or removed on demand
- No physical capacity planning

### Easy Hybrid Cloud
- Simple VPN connectivity
- On-prem + cloud integration
- Ideal for gradual cloud migration

### Better Performance
- Cloud data centers outperform most on-prem setups
- Lower latency for global users

### Improved Security
- Logical isolation
- Managed infrastructure security
- Especially useful for small and mid-sized companies

---

## When a VPC Might Not Be Enough
- Extremely strict compliance requirements
- Regulations requiring physical isolation
- Some large enterprises still prefer private clouds

---

## Key Takeaways (System Design Perspective)
- VPC = private networking + public cloud scale
- Isolation is logical, not physical
- Subnets, VLANs, and VPNs are core building blocks
- NAT enables controlled Internet access
- Foundation for microservices, Kubernetes, and secure cloud architectures

---

# VPC Networking Fundamentals

## 1. Subnets

### What is a Subnet?
A **subnet** is a logical partition of an IP network that divides a VPC into smaller networks.

---

### Why Subnets Matter
- Reduce broadcast traffic
- Improve performance
- Enforce isolation between workloads
- Enable security boundaries

---

### Key Concepts
- Network ID: All host bits = 0
- Broadcast ID: All host bits = 1
- Usable IPs = Total IPs − 2
- CIDR notation (`/24`, `/16`, etc.)

---

### Public vs Private Subnets
- **Public subnet:** Has a route to Internet Gateway
- **Private subnet:** No direct Internet access (uses NAT)

---

## 2. Route Tables

### What is a Route Table?
A **route table** defines how traffic is directed within a VPC.

Every subnet is associated with a route table.

---

### Common Route Entries

| Destination | Target |
|------------|--------|
| VPC CIDR | Local routing |
| 0.0.0.0/0 | Internet Gateway or NAT |
| Specific CIDR | VPC Peering / VPN |

---

### Route Selection Logic
1. Match destination IP
2. Choose **longest prefix match**
3. If no match → use default route

---

### Why Route Tables Are Critical
- Control traffic flow
- Enable Internet access
- Enable private communication
- Misconfiguration = silent outages

---

## 3. Network Access Control Lists (NACLs)

### What is a NACL?
A **Network ACL** is a **stateless, subnet-level firewall**.

---

### Key Characteristics
- Applied at **subnet level**
- Stateless (return traffic must be explicitly allowed)
- Supports **ALLOW and DENY**
- Rules evaluated in numerical order

---

### NACL Rule Components
- Rule number (priority)
- Protocol (TCP, UDP, ICMP, ALL)
- Port range
- Source / Destination CIDR
- Allow or Deny

---

### Default NACL
- Allows all inbound and outbound traffic
- Custom NACLs override default behavior

---

## 4. Security Groups

### What is a Security Group?
A **Security Group** is a **stateful, resource-level firewall**.

Applied to:
- VM network interfaces
- Load balancers
- Endpoint gateways

---

### Key Characteristics
- Stateful
- Allow rules only
- Scoped to a single VPC
- Can span subnets and zones

---

### Stateful Behavior
If inbound traffic is allowed:
- Return traffic is automatically allowed
- No outbound rule needed

---

### Security Group Rules Define
- Direction (inbound / outbound)
- Protocol
- Port range
- Source / destination (CIDR or SG)

Rule order does **not** matter.  
Least restrictive rule wins.

---

## Security Groups vs NACLs

| Feature | Security Group | NACL |
|------|---------------|------|
| Scope | Resource | Subnet |
| Stateful | Yes | No |
| Rule Type | Allow only | Allow + Deny |
| Rule Order | Not relevant | Important |
| Granularity | Fine | Coarse |

---

## Final System Design Summary

| Layer | Responsibility |
|-----|---------------|
| VPC | Network isolation |
| Subnets | Segmentation |
| Route Tables | Traffic direction |
| Security Groups | Instance-level security |
| NACLs | Subnet-level security |

---
