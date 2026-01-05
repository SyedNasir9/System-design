# Caching Fundamentals

---

## Why Caching Exists
Caching stores **frequently accessed data** in a **faster storage layer** so systems don’t repeatedly compute or fetch the same data.

Primary goals:
- Reduce latency
- Reduce load on databases and services
- Improve throughput
- Increase system stability

Without caching:
- Every request hits the database
- Databases panic first
- Everything else follows

---

## What Is a Cache?

A cache is a **temporary data store** that holds copies of data from a slower source.

Key properties:
- Faster than the original data source
- Limited in size
- Data can be stale

Cache trades **accuracy for speed**. Always.

---

## Where Caches Exist

### Client-Side Cache
- Browser cache
- Mobile app cache
- Local memory

**Pros:** Zero network latency  
**Cons:** Hard to control, stale data risk

---

### CDN / Edge Cache
- Caches responses near users
- Static assets, APIs, media

**Pros:** Global low latency  
**Cons:** Limited logic, invalidation complexity

---

### Application Cache
- In-memory caches inside services
- Shared caches (Redis, Memcached)

**Pros:** Fast, controllable  
**Cons:** Memory-bound, consistency issues

---

### Database Cache
- Query cache
- Buffer pool
- Index cache

**Pros:** Transparent  
**Cons:** Limited control

---

## Cache Read Strategies

### Cache-Aside (Lazy Loading)
1. App checks cache
2. If miss → fetch from DB
3. Store result in cache
4. Return response

**Most common pattern**

**Pros:**
- Simple
- Cache only hot data

**Cons:**
- Cache miss latency
- Stale data risk

---

### Read-Through Cache
- Cache fetches data from DB automatically
- App only talks to cache

**Pros:**
- Cleaner application code

**Cons:**
- Cache tightly coupled to data source

---

### Write-Through Cache
- Writes go to cache and DB together

**Pros:**
- Cache always consistent

**Cons:**
- Higher write latency

---

### Write-Behind (Write-Back)
- Write to cache first
- DB updated asynchronously

**Pros:**
- Very fast writes

**Cons:**
- Data loss risk on cache failure

---

## Cache Invalidation Strategies

### Time-Based Expiration (TTL)
- Data expires after fixed time

**Pros:**
- Simple
- Safe default

**Cons:**
- Data may be stale before TTL expires

---

### Write Invalidation
- Remove cache entry on update

**Pros:**
- Fresher data

**Cons:**
- Complex in distributed systems

---

### Explicit Update
- Update cache when DB changes

**Pros:**
- Strong consistency

**Cons:**
- High complexity

---

## Cache Eviction Policies

Because caches are finite, something must go.

### LRU (Least Recently Used)
- Evict data not accessed recently

Most common choice.

---

### LFU (Least Frequently Used)
- Evict data accessed least often

Better for long-term popularity.

---

### FIFO
- Evict oldest entry

Simple, rarely optimal.

---

## Cache Consistency Models

### Strong Consistency
- Cache always reflects DB

Rare. Expensive.

---

### Eventual Consistency
- Cache updates over time

Common. Practical.

---

### Read-Your-Writes
- Users see their own updates immediately

Often implemented selectively.

---

## Common Caching Problems

### Cache Stampede
Many requests miss cache simultaneously and hit DB.

**Solutions:**
- Request coalescing
- Locking
- Early refresh

---

### Cache Penetration
Requests for non-existent data bypass cache.

**Solutions:**
- Cache null values
- Bloom filters

---

### Cache Invalidation Problem
Hardest problem in computer science.

Right after naming things.

---

## When to Cache

Good candidates:
- Read-heavy data
- Expensive computations
- Slowly changing data
- Hot keys

Bad candidates:
- Highly volatile data
- Strong consistency requirements
- Low reuse data

---

## System Design Trade-offs

| Aspect | Trade-off |
|-----|---------|
| Latency | Lower |
| Complexity | Higher |
| Consistency | Weaker |
| Cost | Lower DB usage |
| Failure modes | New ones |

---

## Real-World Usage Patterns

- Cache user profiles
- Cache session data
- Cache API responses
- Cache DB query results
- Cache authorization checks

---

## Key Takeaways

- Cache is a performance optimization, not storage
- Stale data is inevitable
- Invalidation strategy matters more than TTL
- Cache failures must not break the system
- Always design cache as optional

---

## Mental Model

Database is the **source of truth**.  
Cache is a **helpful liar**.

Trust it for speed.  
Verify it for correctness.
