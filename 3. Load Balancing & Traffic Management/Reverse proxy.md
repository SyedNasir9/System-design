# Reverse Proxy and Load Balancers (NGINX & HAProxy)

---

## What is a Reverse Proxy?
A **reverse proxy** sits **in front of backend servers** and forwards client requests to them.

Clients interact only with the reverse proxy, not directly with the backend servers.

---

## Why Reverse Proxies Are Used
Reverse proxies are commonly used to:
- Distribute load across servers
- Hide internal architecture
- Improve security
- Handle SSL termination
- Route requests to different services
- Buffer responses and protect backend servers

---

## NGINX as a Reverse Proxy

### What is NGINX?
NGINX is a **high-performance web server** that also functions as:
- Reverse proxy
- Load balancer
- API gateway
- Static content server

---

### How NGINX Reverse Proxying Works
When NGINX acts as a reverse proxy, it:
1. Receives the client request
2. Forwards the request to a backend server
3. Receives the response
4. Sends the response back to the client

The client never communicates directly with the backend.

---

### Protocol Support
NGINX can proxy requests to:
- HTTP servers
- Application servers using:
  - FastCGI
  - uWSGI
  - SCGI
  - Memcached

This makes NGINX suitable for modern web and microservice architectures.

---

### Header Management (Concept)
NGINX can:
- Modify request headers
- Add client IP information
- Control which headers reach backend services

This is useful for:
- Logging
- Authentication
- Security
- Observability

---

### Response Buffering
By default, NGINX **buffers responses** from backend servers.

#### Why buffering matters:
- Backend servers respond quickly
- Slow clients do not block backend processing
- Improves overall system efficiency

Buffering can be disabled when:
- Low latency streaming is required
- Real-time responses are critical

---

### NGINX in System Design
NGINX is often used as:
- Edge reverse proxy
- API gateway
- L7 load balancer
- SSL termination point

It is extremely common in:
- Microservices
- Kubernetes ingress
- Web applications

---

## HAProxy Overview

### What is HAProxy?
HAProxy is a **dedicated high-performance load balancer** designed specifically for:
- Traffic distribution
- High availability
- Reliability

Unlike NGINX, HAProxy does **not** serve web content.

---

### Job of HAProxy
HAProxy sits in front of backend servers and:
- Distributes incoming requests
- Performs health checks
- Handles failover
- Maintains session persistence

It supports **any TCP/IP service**, including:
- HTTP/HTTPS
- Databases
- Message queues
- Mail servers
- IoT systems

---

### Where HAProxy Is Used
HAProxy is commonly deployed:
- At the network edge
- Inside data centers
- In cloud environments
- As a sidecar proxy in container systems

---

### Key Strengths of HAProxy
- Extremely fast and efficient
- Advanced load balancing algorithms
- Strong support for high availability
- Excellent observability and traffic control
- Designed for large-scale traffic

---

## NGINX vs HAProxy (High-Level Comparison)

| Feature | NGINX | HAProxy |
|------|------|--------|
| Primary Role | Web server + reverse proxy | Dedicated load balancer |
| Content Serving | Yes (static & dynamic) | No |
| Layer Support | L7 (HTTP), limited L4 | L4 and L7 |
| Load Balancing | Yes | Yes (stronger focus) |
| Performance | Very high | Extremely high |
| Configuration Style | Simple & flexible | More explicit & traffic-focused |
| Best Use Case | API gateway, ingress, reverse proxy | High-performance load balancing |

---

## When to Use NGINX
- You need a reverse proxy **and** web server
- You want SSL termination and routing
- You are building microservices or APIs
- Kubernetes ingress controller
- Simpler operational model

---

## When to Use HAProxy
- You need **pure load balancing**
- Very high traffic volume
- Advanced traffic control
- Strong failover and HA requirements
- Load balancing non-HTTP services

---

## Common Real-World Pattern
Many production systems use **both**:

- **NGINX** at the edge for:
  - SSL termination
  - Routing
  - Security
- **HAProxy** behind it for:
  - High-performance load balancing
  - TCP-level traffic distribution

---

## System Design Takeaways
- Reverse proxies and load balancers solve different problems
- NGINX is versatile; HAProxy is specialized
- Choice depends on scale, protocol needs, and performance goals
- Large systems often layer these components

---

## Final Note
If NGINX is a **Swiss army knife**,  
HAProxy is a **precision-engineered traffic weapon**.

Both are boring. Both are essential. Both keep the internet from falling over.
