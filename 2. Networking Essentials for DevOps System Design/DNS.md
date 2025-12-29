 # DNS (Domain Name System) – Notes

## What is DNS?
- DNS is the **phonebook of the Internet**
- Translates **domain names** (example.com) → **IP addresses** (192.0.2.1, IPv6 supported)
- Removes the need for humans to remember numeric IPs

---

## Why DNS Exists
- Humans prefer names, computers require IPs
- Enables:
  - Scalability
  - Load balancing
  - Redundancy
  - Service discovery

---

## Key DNS Components

### 1. DNS Recursive Resolver
- First stop for a DNS query
- Receives request from client (browser / OS)
- Finds the answer by querying other DNS servers
- Caches results for faster future lookups

Examples:
- ISP DNS
- Google DNS (8.8.8.8)
- Cloudflare DNS (1.1.1.1)

---

### 2. Root Nameserver
- Top of the DNS hierarchy
- Knows where TLD servers are
- Does **not** know IPs for domains
- Example: `.` (root zone)

---

### 3. TLD Nameserver
- Handles top-level domains
- Examples:
  - `.com`
  - `.org`
  - `.net`
- Points to authoritative nameservers for domains

---

### 4. Authoritative Nameserver
- Final source of truth
- Stores actual DNS records for a domain
- Returns IP address or record directly
- No further lookups needed

---

## DNS Lookup Flow (Uncached)

1. User enters `example.com` in browser
2. Query sent to **DNS Recursive Resolver**
3. Resolver queries **Root Nameserver**
4. Root returns `.com` TLD server
5. Resolver queries **.com TLD server**
6. TLD returns **authoritative nameserver**
7. Resolver queries authoritative server
8. IP address returned to resolver
9. Resolver returns IP to browser
10. Browser sends HTTP request to the IP

---

## Types of DNS Queries

### Recursive Query
- Client expects a **complete answer**
- Resolver must resolve fully or return error

### Iterative Query
- Server returns the **best information it has**
- Client continues querying next server

### Non-Recursive Query
- Answer is already cached or server is authoritative

---

## DNS Caching

### Why Caching Exists
- Improves performance
- Reduces latency
- Lowers DNS infrastructure load

### Cache Locations

#### 1. Browser Cache
- Fastest
- Short TTL
- First place checked

#### 2. OS Cache (Stub Resolver)
- OS-level DNS client
- Checked before external queries

#### 3. Recursive Resolver Cache
- ISP or public resolver cache
- Honors TTL values

---

## TTL (Time To Live)
- Defines how long a DNS record can be cached
- Lower TTL:
  - Faster updates
  - More DNS queries
- Higher TTL:
  - Better performance
  - Slower propagation

---

## Recursive Resolver vs Authoritative Server

| Aspect | Recursive Resolver | Authoritative Server |
|------|-------------------|---------------------|
| Role | Finds answers | Stores answers |
| Location | Near client | Near domain owner |
| Caching | Yes | No |
| Source of truth | No | Yes |

---

## Common DNS Records

| Record | Purpose |
|------|--------|
| A | Maps domain → IPv4 |
| AAAA | Maps domain → IPv6 |
| CNAME | Alias to another domain |
| MX | Mail servers |
| TXT | Verification, SPF, DKIM |
| NS | Authoritative nameservers |
| SOA | Zone metadata |
| PTR | Reverse DNS |
| SRV | Service discovery |

---

## Reverse DNS
- IP address → domain name
- Uses `PTR` records
- Common in email validation

---

## Anycast DNS
- Same IP advertised from multiple locations
- Traffic routed to nearest server
- Improves latency and availability
- Used by Cloudflare DNS

---

## DNS Security Concepts
- DNSSEC: Protects against DNS spoofing
- DNS over TLS (DoT)
- DNS over HTTPS (DoH)
- Cache poisoning prevention

---

## Cloudflare DNS Highlights
- 1.1.1.1 public resolver
- Anycast-based global network
- Infrastructure-level authoritative DNS
- Partial operator of **F-root server**

---

## Key Takeaways (System Design View)
- DNS is **foundational infrastructure**
- Latency and availability depend heavily on DNS
- Caching behavior affects rollout and failures
- Misconfigured DNS can take entire systems down

---
