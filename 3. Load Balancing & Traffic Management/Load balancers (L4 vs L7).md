 # Load Balancing

---

## What is Load Balancing?
**Load balancing** is the practice of distributing computational or network workloads across **two or more servers** to improve performance, reliability, and scalability.

On the Internet, load balancing is commonly used to distribute incoming traffic across multiple backend servers so that **no single server becomes overwhelmed**.

---

## Why Load Balancing is Important
Without load balancing:
- A single server handles all requests
- Server becomes overloaded
- High latency and slow response times
- Single point of failure

With load balancing:
- Traffic is distributed evenly
- Better performance and lower latency
- Higher availability
- Fault tolerance

---

## Simple Analogy
Imagine a grocery store with **8 checkout counters** but only **1 open**.

- Everyone waits in one long line
- Checkout is slow
- Customers get frustrated

Now open all 8 counters:
- Customers are distributed
- Waiting time drops drastically
- Store operates efficiently

Load balancing does the same thing for servers.

---

## How Load Balancing Works
1. Client sends a request
2. Request reaches the **load balancer**
3. Load balancer selects a backend server
4. Request is forwarded to that server
5. Server processes request and responds

This process repeats for every request.

---

## What is a Load Balancer?
A **load balancer** is a tool or service that distributes incoming traffic across multiple servers.

Types:
- **Hardware-based** load balancers
- **Software-based** load balancers
- **Cloud-managed** load balancers
- **CDN-integrated** load balancers

---

## Load Balancing Algorithms
Load balancers decide where to send traffic using algorithms.

These algorithms fall into two categories:
- Static
- Dynamic

---

## Static Load Balancing Algorithms
Static algorithms do **not** consider the current state of servers.

### Characteristics
- Predefined rules
- No awareness of server health or load
- Simple and fast
- Can lead to inefficiencies

### Examples
- **Round Robin**: Requests are distributed sequentially
- **Random**: Requests sent randomly
- **Round Robin DNS**: DNS rotates IP addresses

### Pros
- Easy to configure
- Low overhead

### Cons
- Cannot react to slow or failed servers
- Uneven load if servers differ in capacity

---

## Dynamic Load Balancing Algorithms
Dynamic algorithms consider **real-time server conditions**.

### Characteristics
- Aware of server health
- Adjusts traffic dynamically
- More complex to configure

### Common Dynamic Algorithms
- **Least Connections**: Sends traffic to server with fewest active connections
- **Weighted Least Connections**: Accounts for server capacity
- **Resource-Based**: Uses CPU, memory, or latency metrics
- **Geolocation-Based**: Routes users to nearest server

### Pros
- Efficient traffic distribution
- Better performance
- Higher reliability

### Cons
- Higher complexity
- Requires monitoring and health checks

---

## Where Load Balancing is Used

### Web Applications
- Distributes user traffic across web servers
- Improves response times

### Global Systems (GSLB)
- Distributes traffic across geographically distributed servers
- Routes users to nearest or healthiest region

### Data Centers
- Internal traffic distribution
- Service-to-service communication

### Cloud Environments
- Managed load balancers (AWS ALB/NLB, GCP LB, Azure LB)
- Auto-scaling integration

---

## Server Monitoring
Dynamic load balancers continuously monitor backend servers.

### Health Checks
- Periodic probes (HTTP, TCP, ICMP)
- Measure:
  - Availability
  - Response time
  - Error rates

If a server becomes unhealthy:
- Traffic is reduced or stopped
- Requests are routed elsewhere

---

## What is Failover?
**Failover** is the process of automatically rerouting traffic when a server fails.

### Why Failover Matters
- Prevents downtime
- Maintains service availability
- Protects user experience

### Failover Flow
1. Server fails health checks
2. Load balancer marks it unhealthy
3. Traffic is redirected to healthy servers
4. Failed server is excluded until recovery

Failover must happen **quickly** to avoid service disruption.

---

## Layer 4 vs Layer 7 Load Balancing

---

## Layer 4 Load Balancing (Transport Layer)

### How It Works
- Operates at **TCP/UDP level**
- Routes traffic based on:
  - IP address
  - Port
  - Protocol

### Characteristics
- Does not inspect request content
- Very fast
- Low overhead

### Use Cases
- High-throughput systems
- Simple routing
- Non-HTTP traffic

### Examples
- TCP load balancing
- UDP load balancing

---

## Layer 7 Load Balancing (Application Layer)

### How It Works
- Operates at **HTTP/HTTPS level**
- Inspects request details:
  - URL path
  - Headers
  - Cookies
  - Query parameters

### Characteristics
- Intelligent routing
- Content-aware
- Slightly higher latency

### Use Cases
- Microservices
- API gateways
- Path-based routing
- Canary deployments

---

## L4 vs L7 Comparison

| Feature | Layer 4 | Layer 7 |
|------|-------|--------|
| OSI Layer | Transport (L4) | Application (L7) |
| Protocol Awareness | TCP/UDP only | HTTP/HTTPS |
| Content Inspection | ❌ No | ✅ Yes |
| Performance | Very high | Slightly lower |
| Routing Flexibility | Limited | Advanced |
| Use Case | Simple, fast routing | Smart, content-based routing |

---

## Key Takeaways (System Design Perspective)
- Load balancing is foundational for scalability and reliability
- Static algorithms are simple but risky at scale
- Dynamic algorithms improve efficiency and resilience
- Health checks and failover are non-negotiable
- L4 = speed, L7 = intelligence
- Real systems often use **both** together

---

## Final Notes
- Load balancers remove single points of failure
- Poor load balancing causes cascading failures
- Every large-scale system relies on this concept, whether visible or not
