# Real-Time vs Eventually Consistent

## Overview
Consistency defines how quickly updates are visible across a system.  
In distributed systems, achieving real-time consistency is expensive and difficult to scale, so many systems rely on eventual consistency instead.

---

## Real-Time (Strong Consistency)
Real-time or strongly consistent systems ensure that all reads reflect the most recent write.

### Characteristics
- Always up-to-date data
- Strong correctness guarantees
- Higher latency on writes
- Harder to scale under load

Strong consistency often requires synchronous replication, coordination, or consensus between nodes.

---

## Eventually Consistent Systems
Eventually consistent systems allow temporary inconsistencies between replicas, with the guarantee that data will converge over time.

### Characteristics
- Faster reads and writes
- Better scalability
- Temporary inconsistencies are acceptable
- Simpler failure handling

This approach is common for user-facing features where slight delays in data propagation are tolerable.

---

## Common Use Cases
- **Real-Time consistency**: payments, authentication, permissions
- **Eventual consistency**: feeds, likes, comments, profile updates

Most large systems use a mix of both approaches depending on the criticality of the data.

---

## Key Takeaway
Most real-world systems combine real-time and eventual consistency.  
The choice depends on how critical it is for the data to be immediately correct.
