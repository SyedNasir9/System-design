# Rate Limiting, Throttling, and Circuit Breakers (System Design)

---

## Why These Concepts Exist
Modern distributed systems fail **not because traffic exists**, but because **traffic is unmanaged**.

The goals are:
- Protect backend services
- Prevent cascading failures
- Maintain predictable performance
- Fail fast instead of failing catastrophically

Rate limiting, throttling, and circuit breakers solve **different parts of this problem**.

---

## 1. Rate Limiting

### What is Rate Limiting?
Rate limiting restricts **how many requests** a client can make **within a defined time window**.

Example:
- 100 requests per minute per user
- 10 requests per second per IP

If the limit is exceeded, requests are rejected (usually HTTP `429 Too Many Requests`).

---

### Why Rate Limiting Is Used
- Prevent abuse (bots, scrapers, DDoS-lite)
- Enforce fair usage
- Protect costly backend operations
- Control billing-sensitive APIs

---

### Common Rate Limiting Dimensions
Rate limits can be applied per:
- IP address
- API key
- User / Consumer
- Endpoint
- Application
- Organization / tenant

---

### Rate Limiting Algorithms (Conceptual)

#### Token Bucket
- Tokens refill at a fixed rate
- Each request consumes a token
- Allows short bursts
- Used by AWS API Gateway

#### Leaky Bucket
- Requests processed at a fixed rate
- Excess requests are queued or dropped
- Smooths traffic aggressively

#### Fixed Window
- Hard limit per time window
- Simple but causes burst issues at boundaries

#### Sliding Window
- Rolling time window
- More accurate, more complex

---

### Rate Limiting Trade-offs
- **Accuracy vs performance**
- **Centralized vs distributed counters**
- **Strict limits vs burst tolerance**

Rate limiting is about **fairness**, not traffic shaping.

---

## 2. Throttling

### What is Throttling?
Throttling is the **enforcement mechanism** that kicks in **when rate limits are exceeded**.

Rate limiting defines the rules.  
Throttling applies the punishment.

---

### How Throttling Works
- Requests above allowed rate are:
  - Rejected
  - Delayed
  - Dropped
- Clients receive signals to slow down

Common response:
- HTTP `429 Too Many Requests`
- Optional `Retry-After` header

---

### Throttling vs Rate Limiting
| Aspect | Rate Limiting | Throttling |
|-----|--------------|-----------|
| Purpose | Define allowed usage | Enforce limits |
| Nature | Policy | Action |
| Client impact | Invisible until exceeded | Explicit failure |
| Goal | Fairness | Protection |

You **configure rate limits**, but the system **throttles traffic**.

---

### Where Throttling Happens
- API Gateway level
- Load balancer
- Service mesh
- Application code (least preferred)

---

### Throttling Scope Examples
- Per-client throttling
- Per-API throttling
- Per-method throttling
- Account-level throttling
- Regional throttling

Throttling is **best-effort**, not a mathematical guarantee.

---

### Client Responsibility
Well-behaved clients:
- Implement retries with backoff
- Respect `Retry-After`
- Do not retry aggressively

Bad clients amplify outages.

---

## 3. Circuit Breakers

### What is a Circuit Breaker?
A circuit breaker **stops requests** to a failing service **before** the system collapses.

It prevents:
- Thread exhaustion
- Connection pile-ups
- Cascading failures

---

### Circuit Breaker States

#### Closed
- Requests flow normally
- Failures are monitored

#### Open
- Requests are blocked immediately
- Fast failure
- No load on failing service

#### Half-Open
- Limited test requests allowed
- Determines recovery
- Either closes or reopens

---

### Why Circuit Breakers Matter
Without them:
- Clients keep retrying
- Latency spikes
- Entire systems stall

With them:
- Failures are isolated
- Systems degrade gracefully
- Recovery is faster

---

### Circuit Breaker Triggers
A breaker may trip based on:
- Failure rate
- Timeout frequency
- Concurrent connections
- Request queue depth

---

### Circuit Breakers vs Retries
| Feature | Retries | Circuit Breakers |
|-----|--------|-----------------|
| Goal | Recover transient failures | Stop repeated failures |
| Risk | Amplifies load | Reduces load |
| Use when | Failure is rare | Failure is persistent |

Retries without circuit breakers **kill systems**.

---

## How These Concepts Work Together

### Typical Request Flow
Client  
→ Rate Limit Check  
→ Throttling (if exceeded)  
→ Circuit Breaker Check  
→ Backend Service

---

### Responsibility Separation
- **Rate Limiting**: Who is allowed to send traffic
- **Throttling**: What happens when they exceed limits
- **Circuit Breakers**: What happens when services fail

Each solves a **different failure mode**.

---

## API Gateway vs Service Mesh Perspective

### API Gateway
- Rate limiting per consumer
- Throttling at the edge
- Protects backend services from clients

### Service Mesh (Istio / Envoy)
- Circuit breakers between services
- Retries, timeouts, fault isolation
- Protects services from each other

Edge protection ≠ internal resilience.

---

## Common Design Mistakes
- No rate limits on public APIs
- Global retries without backoff
- Circuit breakers only at gateway, not internally
- Treating 429 as an error instead of a signal

---

## System Design Takeaways
- Rate limiting controls **fairness**
- Throttling enforces **survival**
- Circuit breakers ensure **stability**
- These are **mandatory**, not optional, at scale
- Absence of these guarantees outages

---

## Final Mental Model
Rate limiting says:  
**“Slow down.”**

Throttling says:  
**“Stop now.”**

Circuit breaker says:  
**“Don’t even try.”**
