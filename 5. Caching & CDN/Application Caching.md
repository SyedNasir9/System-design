# Application Caching

---

## What Is Application Caching?

Application caching is the practice of **storing frequently accessed data in fast storage** so the application doesn’t have to recompute it or fetch it from slower systems like databases or external APIs.

In short:
> Stop doing the same expensive work again and again.

---

## Why Caching Exists

Without caching:
- Every request hits the database
- Databases become the bottleneck
- Latency increases
- Costs increase
- Systems fall over under load

Caching exists to:
- Reduce latency
- Reduce load on backend systems
- Improve throughput
- Increase system resilience

---

## Where Application Caching Sits

Typical request flow:

Client  
→ Application  
→ **Cache**  
→ Database / External Service  

If cache hit:
- Response is fast
- Backend untouched

If cache miss:
- Fetch from backend
- Store in cache
- Return response

---

## What Should Be Cached?

Good candidates:
- Frequently read data
- Expensive-to-compute results
- Data that changes infrequently
- Aggregated or derived data

Examples:
- User profiles
- Configuration values
- Feature flags
- Product details
- Permissions
- API responses

Bad candidates:
- Highly volatile data
- Strongly consistent transactional data
- One-time-use data

---

## Cache Granularity

### Object-Level Caching
Cache entire objects.
- User object
- Product object

Simple and common.

---

### Query-Level Caching
Cache query results.
- `SELECT * FROM products WHERE category = X`

Fast, but fragile if data changes often.

---

### Computation-Level Caching
Cache derived results.
- Reports
- Aggregations
- Scoring results

High value, often expensive to recompute.

---

## Cache Storage Options

### In-Memory Cache (Local)
Stored inside application memory.

Pros:
- Extremely fast
- No network call

Cons:
- Lost on restart
- Doesn’t scale across instances

Use when:
- Single instance
- Low criticality

---

### Distributed Cache
Shared cache outside the app.

Pros:
- Shared across services
- Scales horizontally
- Survives app restarts

Cons:
- Network latency
- Additional infrastructure

Use when:
- Multiple instances
- High traffic systems

---

## Cache Expiration Strategies

### Time-To-Live (TTL)
Cache entry expires after a fixed time.

Simple.
Predictable.
Can serve stale data briefly.

---

### Manual Invalidation
Explicitly remove cache when data changes.

Accurate.
Hard to get right.
Often forgotten.

---

### Write-Through
Write goes to cache and database together.

Pros:
- Cache always warm

Cons:
- Slower writes

---

### Write-Behind
Write goes to cache first, DB later.

Fast writes.
Risky if cache fails.

---

## Cache Access Patterns

### Cache-Aside (Lazy Loading)
Most common pattern.

Flow:
1. Check cache
2. If miss, read DB
3. Update cache

Pros:
- Simple
- Flexible

Cons:
- Cache misses hurt

---

### Read-Through
Cache automatically loads from DB.

Pros:
- Cleaner app logic

Cons:
- Less control

---

### Write-Through
Every write updates cache and DB.

Pros:
- Consistency

Cons:
- Higher write latency

---

## Consistency and Caching

Caching always introduces a trade-off.

You choose between:
- Fresh data
- Fast data

Most systems accept:
- **Eventual consistency**

Critical systems (payments, inventory):
- Cache carefully
- Or don’t cache at all

---

## Cache Invalidation: The Hard Part

The two hard problems in computer science:
1. Naming things
2. Cache invalidation
3. Off-by-one errors

Invalidation strategies:
- TTL-based expiry
- Event-driven invalidation
- Versioned keys

There is no perfect solution.
Only acceptable trade-offs.

---

## Common Failure Scenarios

### Cache Stampede
Many requests miss cache simultaneously.

Mitigations:
- Request locking
- Staggered TTLs
- Pre-warming

---

### Stale Data
Cache returns outdated data.

Mitigations:
- Short TTL
- Background refresh
- Soft expiration

---

### Cache Outage
Cache goes down.

Design rule:
- Cache must be optional
- App should fall back to DB

Never let cache failure kill the system.

---

## When NOT to Cache

- Data changes constantly
- Strong consistency is mandatory
- Data access is already cheap
- Dataset is tiny

Caching everything is laziness, not optimization.

---

## Observability for Caching

You must track:
- Cache hit ratio
- Cache latency
- Eviction rate
- Error rate

A cache you can’t observe is a liability.

---

## Real-World Usage

Typical production stack:
- Application cache for hot data
- Database for source of truth
- CDN for edge caching
- Message queues for invalidation events

Caching is layered, not single-point.

---

## Key Takeaways

- Caching is about **latency and load**, not convenience
- Cache is not a database
- Invalidation is harder than storage
- Cache must never be a single point of failure
- Design for cache misses, not hits

---

## Mental Model

Cache is a **performance optimization**, not a correctness mechanism.

Treat it like a helpful liar.
Fast.
Usually right.
Occasionally wrong.
