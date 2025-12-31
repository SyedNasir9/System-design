# Load Balancing Algorithms (Theory)

---

## Overview
Load balancing algorithms determine **how incoming requests are distributed across multiple servers**.

The primary goal is:
- Prevent server overload
- Improve performance
- Increase availability
- Ensure fair resource utilization

This document covers **three commonly used algorithms**:
1. Round Robin
2. Resource-Based
3. Hashing (Source IP Hash)

---

## 1. Round Robin Load Balancing Algorithm

### What is Round Robin?
Round Robin is a **static load balancing algorithm** that distributes requests **sequentially** across a list of servers.

Each incoming request is assigned to the next server in order, looping back to the first server after reaching the end.

---

### How It Works (Conceptually)
- Server list: S1 → S2 → S3 → S1 → S2 → …
- No consideration of:
  - Server load
  - CPU usage
  - Memory
  - Response time

All servers are treated **equally**.

---

### Key Characteristics
- Simple and deterministic
- No runtime decision-making
- Predictable request distribution

---

### When to Use Round Robin
- All servers have **similar capacity**
- Requests are **uniform**
- Simplicity is more important than optimization
- Small or predictable workloads

---

### Advantages
- Very easy to implement
- Fair request distribution (in theory)
- Minimal overhead

---

### Disadvantages
- Ignores server health and load
- Can overload slower servers
- Not suitable for heterogeneous systems

---

## 2. Resource-Based Load Balancing Algorithm

### What is Resource-Based Load Balancing?
Resource-based load balancing distributes requests based on **real-time resource availability** of servers.

Resources may include:
- CPU usage
- Memory utilization
- Disk I/O
- Network bandwidth

Requests are routed to the server with the **most available resources**.

---

### How It Works (Conceptually)
- Load balancer continuously monitors servers
- Evaluates current resource metrics
- Sends new requests to the least-loaded server

Decisions are made **dynamically**.

---

### Key Characteristics
- Dynamic and adaptive
- Requires monitoring and metrics
- Reacts to real-time conditions

---

### When to Use Resource-Based Load Balancing
- Servers have **different capacities**
- Workloads are **resource-intensive**
- Traffic patterns are unpredictable
- Performance optimization is critical

---

### Advantages
- Efficient resource utilization
- Prevents server overload
- Adapts to changing conditions

---

### Disadvantages
- Higher complexity
- Monitoring overhead
- More expensive to operate
- Slower decision-making than static methods

---

## 3. Hashing Load Balancing Algorithm (Source IP Hash)

### What is Hash-Based Load Balancing?
Hash-based load balancing assigns requests to servers based on a **hash value** computed from a request attribute.

Most commonly used:
- **Source IP address**

Requests from the same source are consistently routed to the same server.

---

### How It Works (Conceptually)
1. Extract a value (e.g., source IP)
2. Apply a hash function
3. Map the hash to a server
4. Route request to that server

This ensures **deterministic routing**.

---

### Key Characteristics
- Deterministic routing
- Provides session persistence
- Static in nature

---

### When to Use Hash-Based Load Balancing
- Applications require **session stickiness**
- Stateful applications
- Authentication-heavy systems
- Banking, carts, user sessions

---

### Advantages
- Session consistency
- No need for external session storage
- Predictable routing

---

### Disadvantages
- Uneven load if traffic is skewed
- Poor scaling behavior when servers change
- Adding/removing servers can break session mapping

---

## Comparison Summary

| Algorithm | Type | Decision Basis | Best For |
|--------|------|---------------|---------|
| Round Robin | Static | Order-based | Uniform servers & traffic |
| Resource-Based | Dynamic | Real-time metrics | Performance-critical systems |
| Hashing (IP Hash) | Static | Request attribute | Session persistence |

---

## System Design Takeaways
- Round Robin favors **simplicity**
- Resource-Based favors **efficiency**
- Hashing favors **consistency**
- Real systems often combine algorithms
- Algorithm choice impacts scalability and reliability

---

## Final Notes
There is **no universally best algorithm**.

Choosing the right load balancing strategy depends on:
- Server heterogeneity
- Traffic patterns
- Stateful vs stateless design
- Performance requirements
