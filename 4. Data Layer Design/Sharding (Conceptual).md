# Sharding (Conceptual)

---

## What Is Sharding?

Sharding is the process of **splitting a large dataset into smaller pieces** called *shards*, and distributing them across **multiple databases or nodes**.

Each shard holds **only a subset of the total data**.

Goal:
- Scale **horizontally**, not vertically

---

## Why Sharding Is Needed

Single database problems:
- Storage limits
- CPU and memory bottlenecks
- I/O contention
- Slow queries at scale

Sharding solves:
- Data size limits
- Read/write throughput
- Hotspot reduction

But introduces:
- Complexity
- Coordination
- New failure modes

---

## Sharding vs Replication

| Aspect | Replication | Sharding |
|-----|-----------|--------|
| Purpose | Availability & reads | Scalability |
| Data | Same data everywhere | Different data per node |
| Writes | Usually single leader | Distributed |
| Complexity | Moderate | High |

Replication copies data.  
Sharding **divides responsibility**.

---

## What Is a Shard?

A shard is:
- A **logical partition** of data
- Usually hosted on one database node
- Independently managed

Example:
- Users 1–1M → Shard A
- Users 1M–2M → Shard B

---

## Sharding Key

A **shard key** determines where data lives.

Good shard key properties:
- High cardinality
- Even distribution
- Immutable
- Frequently queried

Bad shard keys:
- Boolean fields
- Timestamps
- Highly skewed values

Shard key mistakes are forever.

---

## Common Sharding Strategies

### 1. Range-Based Sharding
Data is split by value ranges.

Example:
- A–F → Shard 1
- G–M → Shard 2
- N–Z → Shard 3

**Pros:**
- Simple
- Range queries are fast

**Cons:**
- Hot shards
- Uneven growth

---

### 2. Hash-Based Sharding
Shard = `hash(key) % N`

**Pros:**
- Even distribution
- Prevents hotspots

**Cons:**
- Range queries are expensive
- Resharding is painful

---

### 3. Directory-Based Sharding
A lookup service maps keys → shards.

**Pros:**
- Flexible
- Easy to rebalance

**Cons:**
- Extra hop
- Directory is a single point of failure

---

### 4. Geo-Based Sharding
Data placed near users.

Example:
- India → Shard IN
- US → Shard US
- EU → Shard EU

**Pros:**
- Low latency
- Regulatory compliance

**Cons:**
- Cross-region queries are hard

---

## Querying in a Sharded System

### Targeted Query
Shard key is known.

Fast.
Efficient.
Cheap.

---

### Scatter-Gather Query
Shard key unknown → query all shards.

Slow.
Expensive.
Embarrassing.

Avoid in designs unless unavoidable.

---

## Cross-Shard Challenges

### Joins
- No native joins across shards
- Handled at application layer

---

### Transactions
- Distributed transactions are hard
- Often avoided
- Eventual consistency preferred

---

### Aggregations
- Partial aggregation per shard
- Final aggregation at application layer

---

## Re-sharding (The Pain)

When shards become unbalanced:
- Add new shards
- Move data
- Update routing logic

Problems:
- Data migration
- Downtime risks
- Cache invalidation
- Client coordination

Design for resharding **from day one**.

---

## Hot Shards

Occurs when:
- One shard receives disproportionate traffic
- Caused by bad shard key choice

Mitigations:
- Better shard key
- Write fan-out
- Caching hot keys
- Dynamic sharding

---

## Sharding + Caching

Caches reduce shard pressure:
- Cache hot keys
- Cache read-heavy data
- Protect shards from spikes

But:
- Cache invalidation becomes harder
- Shard-aware caching may be required

---

## When NOT to Shard

Do not shard when:
- Dataset is small
- Traffic is low
- Vertical scaling is enough
- Team lacks distributed systems experience

Sharding too early is self-harm.

---

## Real-World Use Cases

- User databases
- Order systems
- Event logs
- Time-series data
- Multi-tenant SaaS platforms

---

## System Design Trade-offs

| Benefit | Cost |
|-----|----|
| Horizontal scalability | Operational complexity |
| Higher throughput | Cross-shard coordination |
| Fault isolation | Hard debugging |
| Unlimited growth | Expensive migrations |

---

## Key Takeaways

- Sharding enables scale, not simplicity
- Shard key choice decides system fate
- Avoid cross-shard operations
- Design resharding early
- Sharding is a commitment

---

## Mental Model

Replication is **cloning**.  
Sharding is **delegation**.

One makes systems safer.  
The other makes them bigger.

Both make them harder.
