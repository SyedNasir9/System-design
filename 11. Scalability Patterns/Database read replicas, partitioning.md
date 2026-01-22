# Database Read Replicas and Partitioning

---

## What This Means

Two common ways to scale databases when your app gets popular (or when it gets inefficient and you blame “scale”):

- **Read replicas**: copy data from the primary to replicas so reads can be served by more nodes.
- **Partitioning (sharding)**: split data into smaller chunks so no single database node has to hold or serve everything.

Core idea:
> Read replicas scale reads. Partitioning scales the dataset and total throughput.

---

## Why This Exists

Databases usually hit limits before stateless services do:
- CPU and IO bottlenecks
- connection limits
- write contention / locks
- large indexes and slow queries
- hot tables and hot keys
- storage growth

These patterns exist to:
- handle more **read traffic**
- reduce **latency**
- increase **availability**
- scale beyond a single node’s **storage/compute limits**

---

# 1) Read Replicas

---

## What Are Read Replicas?

A **read replica** is a database node that **receives replication from the primary** and serves **read-only queries**.

Typical setup:
- **Primary (writer)**: handles writes + some reads
- **Replicas (readers)**: handle read traffic

Replication styles:
- **Asynchronous** (most common): fast writes, replicas can lag
- **Synchronous**: stronger consistency, slower writes
- **Semi-synchronous**: middle ground

---

## When Read Replicas Help

Read replicas are great when you have:
- high read-to-write ratio (e.g., 90% reads)
- expensive read queries
- analytics/reporting reads you want off the primary
- geo-distributed users needing nearby reads

They do **not** fix:
- write bottlenecks
- bad schema/design
- missing indexes
- “SELECT *” crimes against humanity

---

## Replication Lag (The Catch)

With async replication, replicas can be behind the primary.
This creates **eventual consistency** issues.

Examples of pain:
- user updates profile, refreshes, still sees old data
- payment succeeds but “order status” reads stale

Common mitigations:
- **read-your-writes**: route a user to primary for a short time after write
- **session/txn pinning**: same request flow reads from primary
- **staleness tolerance**: allow replicas only if lag < X seconds
- **cache for reads** (when appropriate)
- **change data capture** for event-driven consumers

Rule:
> Replicas are fast, but they can lie briefly.

---

## Read Routing Patterns

How your app chooses where to read from:

- **Primary for writes, replica for reads** (classic)
- **Primary for critical reads**, replicas for non-critical reads
- **Replica pool with health + lag checks**
- **Geo-read routing** (users read from nearest region replica)

Risks:
- accidentally sending writes to replicas
- inconsistent behavior under failover
- silent lag causing confusing UX

---

## Failover and Promotion

If primary dies:
- a replica can be promoted to primary

Concerns:
- split-brain prevention
- DNS/connection string updates
- replication topology rebuild
- application retry behavior

Failover is where “we have replicas” turns into “we have a plan” or “we have regrets”.

---

# 2) Partitioning (Sharding)

---

## What Is Partitioning?

**Partitioning** splits data so it’s stored/served in pieces.

Two big types:

### A) Vertical Partitioning
Split by columns/features:
- user table in DB-A
- orders table in DB-B
- analytics tables elsewhere

Good for:
- isolating workloads
- reducing contention
- separating “hot” from “cold” storage

### B) Horizontal Partitioning (Sharding)
Split by rows:
- users 1..N distributed across shards

Good for:
- scaling dataset size
- scaling total throughput (reads + writes)

---

## Sharding Strategies

### 1) Range-based Sharding
Shard by ranges:
- user_id 1-1M → shard1
- 1M-2M → shard2

Pros:
- easy to understand
- efficient range queries

Cons:
- hotspots if new data clusters in one range (new users all hit shardN)

---

### 2) Hash-based Sharding
Shard by hash(key) % num_shards

Pros:
- good distribution
- reduces hotspots

Cons:
- range queries become harder (scatter/gather)
- resharding is painful when shard count changes

---

### 3) Consistent Hashing
Similar idea, but reduces movement when adding/removing shards.

Pros:
- easier scaling events than simple modulo hash
- less data reshuffle

Cons:
- still complex
- operational overhead remains real

---

### 4) Directory-based Sharding
A lookup table maps key → shard

Pros:
- flexible placement
- easy to move specific tenants/users

Cons:
- directory becomes critical dependency
- needs caching and high availability

---

## Hot Keys and Uneven Load

Sharding doesn’t magically prevent hotspots:
- one celebrity user
- one tenant with massive traffic
- one product going viral
- one “global counter” row

Mitigations:
- isolate big tenants (“tenant per shard”)
- split hot partitions further
- caching for hot reads
- avoid global counters (use distributed counters or batching)

---

## Cross-Shard Queries (The Tax)

Once you shard, joins across shards become:
- slow
- complicated
- expensive
- frequently avoided

Patterns to survive:
- design for query locality (most queries hit one shard)
- denormalize carefully
- use a search/index system (Elastic/OpenSearch) for cross-cutting queries
- use analytics warehouses for global reporting

Rule:
> Sharding is trading database simplicity for application complexity.

---

# 3) Combining Read Replicas + Sharding

---

## Common “Grown-Up” Setup

- Each shard has:
  - 1 primary (writes)
  - N read replicas (reads)

So you scale:
- **writes and dataset** by adding shards
- **reads** by adding replicas per shard

This is powerful, and also creates many moving parts for humans to break.

---

# 4) Operational Reality

---

## Backups and Restore Complexity

With replicas:
- backups are easier (dump from replica, reduce primary load)

With shards:
- backups must be coordinated
- restore must reconstruct multiple shards consistently

---

## Schema Migrations

Replicas:
- manageable, but still careful with online migrations

Shards:
- multiplied effort: migration must run across all shards
- version skew issues
- rollout coordination becomes critical

---

## Consistency and Transactions

- replicas introduce eventual consistency on reads
- sharding makes cross-shard transactions hard

Solutions:
- avoid cross-shard transactions via data modeling
- sagas / outbox patterns for multi-entity workflows
- accept eventual consistency where possible

---

# 5) When to Use What

---

## Use Read Replicas When
- reads are the bottleneck
- you can tolerate eventual consistency for some reads
- you want better availability for read traffic
- you need geo-local reads

## Use Partitioning/Sharding When
- dataset is too large for one node
- writes are bottlenecked
- a single primary can’t keep up
- you need isolation between tenants/workloads

## Don’t Use Either When
- your queries are unindexed and slow
- your schema is chaotic
- you don’t have monitoring and backups
Because you’ll just scale the chaos.

---

# 6) Observability

---

## Replicas
Track:
- replication lag (seconds/bytes)
- replica read QPS/latency
- errors and connection saturation
- failover times

## Shards
Track:
- per-shard QPS/latency
- per-shard storage growth
- hotspot detection (top keys/tenants)
- imbalance metrics (max shard load vs avg)

You want to know:
- “Which shard is dying?”
not just
- “The database is dying.”

---

## Interview-Ready Summary

> Read replicas scale database read throughput by replicating data from a primary to one or more read-only nodes, but introduce replication lag and eventual consistency challenges that require read-routing strategies. Partitioning (sharding) scales beyond a single node by splitting data vertically or horizontally across multiple databases; it improves throughput and storage capacity but increases operational complexity, makes cross-shard queries and transactions harder, and requires careful shard-key selection to avoid hotspots. At large scale, systems often combine sharding for write/dataset scaling with read replicas per shard for read scaling.

---

## Final Takeaway

- **Read replicas**: easiest win for read-heavy systems, but consistency becomes “sometimes”.
- **Partitioning/sharding**: real scaling power, but you pay in complexity forever.
- The correct shard key is the difference between a scalable system and a distributed dumpster fire.
