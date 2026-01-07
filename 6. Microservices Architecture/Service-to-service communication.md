# Service-to-Service Communication

---

## What Is Service-to-Service Communication?

**Service-to-service communication** is how independent services in a distributed system talk to each other to complete a request.

In monoliths:
- Function calls
- Shared memory

In microservices:
- Network calls
- Partial failure is guaranteed
- Latency is unavoidable

This changes everything.

---

## Why It Matters

Every service-to-service call introduces:
- Network latency
- Failure probability
- Retry complexity
- Debugging pain

One request often fans out into **multiple downstream calls**.

---

## Common Communication Patterns

---

### 1. Synchronous Communication

The calling service **waits** for a response.

Examples:
- HTTP/REST
- gRPC

Flow:
1. Service A sends request
2. Service B processes it
3. Service B responds
4. Service A continues

---

#### Pros
- Simple to reason about
- Immediate response
- Easy to implement

---

#### Cons
- Tight coupling
- Cascading failures
- Latency adds up quickly

If one downstream service is slow, **everything slows**.

---

### 2. Asynchronous Communication

The calling service **does not wait** for a response.

Examples:
- Message queues
- Event streams
- Pub/Sub systems

Flow:
1. Service A publishes event
2. Message broker stores it
3. Service B consumes later

---

#### Pros
- Loose coupling
- Better resilience
- Scales well

---

#### Cons
- Eventual consistency
- Harder debugging
- More complex flow

You trade simplicity for survivability.

---

## Communication Protocols

---

### REST (HTTP/JSON)

- Human-readable
- Widely supported
- Easy debugging

But:
- Verbose payloads
- Higher latency
- No strict contracts

Good for:
- External APIs
- Simpler internal calls

---

### gRPC

- Binary protocol (Protobuf)
- Strong contracts
- Faster than REST

But:
- Harder to debug
- Steeper learning curve

Good for:
- Internal service-to-service calls
- High-performance systems

---

### Messaging Systems

- Kafka
- RabbitMQ
- SQS / PubSub

Good for:
- Event-driven systems
- Decoupled workflows
- High throughput

---

## Service Discovery

Services must **find each other**.

### Static Discovery
- Hardcoded IPs
- DNS entries

Breaks the moment things scale.

---

### Dynamic Discovery
- Service registry
- Kubernetes DNS
- Service mesh

Required for:
- Autoscaling
- Failover
- Zero-downtime deployments

---

## Load Balancing Between Services

Requests must be distributed across instances.

Options:
- Client-side load balancing
- Server-side load balancers
- Service mesh proxies

Bad load balancing = uneven load + random failures.

---

## Failure Handling (This Is the Hard Part)

---

### Timeouts
Always set them.

No timeout = infinite wait = thread exhaustion.

---

### Retries
Useful, but dangerous.

- Too many retries → retry storm
- Retrying without backoff → self-inflicted DDoS

---

### Circuit Breakers
Stop calling a failing service.

States:
- Closed (normal)
- Open (fail fast)
- Half-open (test recovery)

Prevents cascading failure.

---

## Data Consistency Reality

Service-to-service communication introduces:
- Partial updates
- Eventual consistency
- Incomplete transactions

Distributed systems **do not get ACID for free**.

---

## Observability Requirements

You *cannot* debug service communication blind.

You need:
- Distributed tracing
- Correlation IDs
- Structured logs
- Metrics per dependency

Without this, outages become ghost stories.

---

## Common Anti-Patterns

- Chatty services (too many calls)
- Synchronous chains 6 services deep
- No timeouts
- No retries or infinite retries
- Assuming the network is reliable

The network is never reliable.

---

## Real-World Example

User request → API Gateway →  
Auth Service → User Service → Order Service → Payment Service

One slow call = user stares at spinner wondering why life is unfair.

---

## Key Takeaways

- Service communication happens over unreliable networks
- Latency and failure are normal, not edge cases
- Synchronous is simple but fragile
- Asynchronous is resilient but complex
- Observability is mandatory, not optional

---

## System Design Mental Model

Every service call is a **failure boundary**.

Design like it will fail.  
Because it will.
