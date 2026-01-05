# Basic Replication (Leader–Follower)

---

## Why Replication Exists
Replication means **maintaining multiple copies of the same data** across different nodes.

Primary goals:
- High availability
- Fault tolerance
- Read scalability
- Disaster recovery

Without replication:
- One crash = game over
- One region failure = outage headline

---

## What is Leader–Follower Replication?

Leader–Follower (also called **Primary–Replica** or **Master–Slave**) replication is a model where:

- **Leader (Primary)** handles all writes
- **Followers (Replicas)** copy data from the leader
- Followers typically serve **read requests**

Only one node decides the truth. The others agree politely.

---

## Core Components

### Leader (Primary)
- Accepts all write operations
- Orders transactions
- Maintains the authoritative data state
- Streams changes to followers

---

### Followers (Replicas)
- Receive updates from the leader
- Apply changes in the same order
- Serve read traffic
- Can be promoted if leader fails

---

## Replication Workflow

1. Client sends **write request** to leader
2. Leader:
   - Validates the write
   - Updates its local storage
   - Writes to replication log
3. Leader sends changes to followers
4. Followers apply updates
5. Client receives acknowledgment (timing depends on sync mode)

---

## Replication Modes

### Synchronous Replication
- Leader waits for followers to confirm writes
- Strong consistency
- Higher latency
- Safer but slower

**Failure behavior:**
- If a follower is slow, writes slow down

---

### Asynchronous Replication
- Leader does not wait for followers
- Lower latency
- Higher throughput
- Risk of data loss on leader failure

**Failure behavior:**
- Followers may lag behind

---

### Semi-Synchronous Replication
- Leader waits for **at least one** follower
- Balanced trade-off
- Common in production systems

---

## Read and Write Flow

### Writes
- Always go to the leader
- Single write authority prevents conflicts

### Reads
- Can go to:
  - Leader (strong consistency)
  - Followers (eventual consistency)

This introduces **replication lag**.

---

## Replication Lag

### What It Is
Time difference between:
- When data is written on the leader
- When it appears on followers

---

### Why It Happens
- Network latency
- Slow disks
- High write volume
- Follower resource constraints

---

### Why It Matters
- Stale reads
- Inconsistent user experience
- Read-after-write anomalies

---

## Failover

### What Happens When Leader Fails
1. Failure is detected
2. One follower is promoted to leader
3. Clients are redirected
4. Replication resumes from new leader

This sounds simple. It isn’t.

---

### Common Failover Challenges
- Split brain (two leaders)
- Data loss (async replication)
- Manual vs automatic promotion
- Client reconnection delays

---

## Split Brain Problem

### What It Is
Two nodes believe they are the leader.

### Why It’s Dangerous
- Conflicting writes
- Data corruption
- Extremely hard recovery

### Prevention
- External consensus systems (Zookeeper, etcd)
- Quorum-based leader election
- Fencing tokens

---

## Pros of Leader–Follower Replication

- Simple mental model
- Strong write consistency
- Easy to reason about
- Widely supported

Used by:
- MySQL
- PostgreSQL
- Redis (replication mode)
- MongoDB (primary-secondary)

---

## Cons of Leader–Follower Replication

- Leader is a bottleneck
- Write scalability is limited
- Failover complexity
- Replication lag affects reads

---

## Common Use Cases

- Relational databases
- Read-heavy applications
- Systems needing strong consistency for writes
- Traditional OLTP workloads

---

## System Design Trade-offs

| Aspect | Decision |
|-----|--------|
| Write scalability | Limited |
| Read scalability | High |
| Consistency | Strong (writes) |
| Availability | Depends on failover |
| Complexity | Medium |

---

## Real-World Patterns

- Leader for writes
- Multiple followers per region
- Read replicas near users
- Leader in primary region
- Async replication across regions

---

## Key Takeaways

- Replication ≠ scalability for writes
- Leader–Follower simplifies consistency
- Replication lag is unavoidable
- Failover is harder than it sounds
- Always design for stale reads

---

## Mental Model

Leader says:  
**“This is the truth.”**

Followers say:  
**“We’ll copy it… eventually.”**

Design systems assuming that **eventually** is not instant.
