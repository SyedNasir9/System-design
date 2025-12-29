# Virtual Private Cloud (VPC)

---

## What is a VPC?
A **Virtual Private Cloud (VPC)** is a **secure, isolated private cloud** hosted within a **public cloud**.

It allows customers to:
- Run applications
- Store data
- Host websites
- Control networking

…just like a private cloud, but **without owning physical infrastructure**.

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
- Combines scalability + isolation

---

## How is a VPC Isolated?

VPC isolation is achieved using multiple networking technologies:

### Subnets (Layer 3)
- Reserved ranges of **private IP addresses**
- Not accessible from the public Internet
- Used to segment workloads (public vs private subnets)

---

### VLAN (Layer 2)
- Virtual Local Area Network
- Logical separation at the data link layer
- Prevents traffic leakage between tenants

---

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

---

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

---

### Easy Hybrid Cloud
- Simple VPN connectivity
- On-prem + cloud integration
- Ideal for gradual cloud migration

---

### Better Performance
- Cloud data centers outperform most on-prem setups
- Lower latency for global users

---

### Improved Security
- Logical isolation
- Managed infrastructure security
- Especially beneficial for:
  - Small and mid-sized companies

---

## When VPC Might Not Be Enough
- Extremely strict compliance requirements
- Regulatory environments requiring physical isolation
- Some large enterprises prefer fully private clouds

---

## Key Takeaways (System Design Perspective)
- VPC = private networking + public cloud scale
- Isolation is logical, not physical
- Subnets, VLANs, and VPNs are core building blocks
- NAT enables controlled Internet access
- Foundation for:
  - Microservices
  - Kubernetes
  - Secure cloud architectures
