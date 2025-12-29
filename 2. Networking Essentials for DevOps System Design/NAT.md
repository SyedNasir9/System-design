# Network Address Translation (NAT)
---

## What is NAT?
Network Address Translation (NAT) allows multiple devices in a **private network** to access the Internet using **one or few public IP addresses**.

It translates **private IP addresses ↔ public IP addresses**, helping conserve IPv4 space and adding a basic security layer by hiding internal systems.

---

## Why NAT Exists
- IPv4 supports only **2³² (~4.3 billion) addresses**
- Number of connected devices far exceeds available public IPs
- NAT allows thousands of devices to share limited public IPs

---

## Key Benefits of NAT
- Conserves IPv4 addresses
- Masks internal IP addresses
- Allows many devices to share one public IP
- Adds a basic layer of network security

---

## Working of NAT

1. Internal device sends a request to the Internet
2. Packet reaches the **NAT-enabled router**
3. Router:
   - Replaces private IP with public IP
   - Assigns a unique source port
4. Mapping is stored in the **NAT table**
5. Response from server arrives
6. NAT uses table entry to forward packet to correct internal device

---

## Why NAT Works
- Multiple devices share one public IP
- Port numbers uniquely identify sessions
- Internal network structure remains hidden

---

## Why NAT Modifies Port Numbers
If multiple internal devices:
- Use the same source port
- Contact the same destination

Replies become ambiguous.

NAT solves this by:
- Modifying both **IP address and port**
- Storing unique mappings in the NAT table
- Ensuring correct packet delivery

---

## Examples of NAT Usage

### Internet Access for Private Networks
- All internal devices use private IPs
- NAT router translates them to one public IP

### Connecting Multiple Office Locations
- Branches use private IP ranges
- NAT enables inter-office communication without IP conflicts

---

## NAT Inside and Outside Addresses

| Term | Meaning |
|----|-------|
| Inside Local | Private IP inside the local network |
| Inside Global | Public IP representing the internal host |
| Outside Local | Destination IP as seen internally |
| Outside Global | Actual public IP of external host |

---

## Types of NAT

### 1. Static NAT
- One-to-one mapping (private ↔ public)
- Used for hosting servers
- Not scalable or cost-effective

---

### 2. Dynamic NAT
- Maps private IPs to a pool of public IPs
- Limited by pool size
- Requests dropped if pool is exhausted

---

### 3. Port Address Translation (PAT)
- Also called **NAT Overload**
- Multiple private IPs share a single public IP
- Uses different port numbers
- Most widely used NAT type

---

## NAT Techniques

- **Static Mapping:** Fixed private ↔ public IP mapping
- **IP Masquerading:** Entire private network behind one public IP
- **Translation Table Mapping:** Uses NAT table for tracking
- **PAT:** Port-level translation
- **Round-Robin Mapping:** Distributes incoming traffic across multiple hosts

---

## Pros and Cons of NAT

### Pros
- Conserves public IPs
- Improves privacy
- Hides internal network structure
- Enables Internet access for private networks

### Cons
- Breaks end-to-end connectivity
- Issues with VoIP, gaming, P2P
- Adds processing overhead on routers
- Complicates peer-to-peer communication

---

## Key Takeaways (System Design View)
- NAT is essential due to IPv4 limitations
- PAT is the most common implementation
- NAT tables are critical stateful components
- Misconfigured NAT can break applications silently
- IPv6 reduces the need for NAT, but does not eliminate it in practice
