# Latency vs Durability

## The Trade-off
Latency and durability often work against each other in system design.  
Designing for faster responses usually reduces data safety, while guaranteeing durability increases write latency.

---

## Latency
Latency refers to how quickly a system responds to a request.

### Characteristics
- Fast responses
- Better user experience
- Higher throughput
- Increased risk of data loss

Systems optimized for low latency may acknowledge writes before data is safely persisted.

---

## Durability
Durability ensures that once data is acknowledged, it will not be lost even if the system crashes.

### Characteristics
- Strong data safety guarantees
- Slower write operations
- Higher infrastructure and replication cost

Durability often requires synchronous disk writes or replication across nodes.

---

## When to Favor Latency
Latency-first designs are acceptable when occasional data loss is low risk or recoverable:

- Logs
- Metrics
- Likes and views
- Telemetry data

---

## When to Favor Durability
Durability should be prioritized when data loss is unacceptable:

- Payments
- Orders
- Passwords
- Account state changes

---

## Key Takeaway
Critical data should be **safe, not fast**.  
Latency optimizations are only appropriate when the business can tolerate data loss.
