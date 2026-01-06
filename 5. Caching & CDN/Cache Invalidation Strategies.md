# Cache Invalidation Strategies

---

## What Is Cache Invalidation?

**Cache invalidation** is the process of **removing or updating stale data** in a cache when the underlying source of truth changes.

Caching is easy.  
Keeping cached data correct is the real problem.

---

## Why Cache Invalidation Is Hard

- Data changes unpredictably
- Multiple cache layers exist (CDN, app, DB)
- Users expect fresh data instantly
- Systems are distributed

This is why the saying exists:
> “There are only two hard problems in computer science: cache invalidation and naming things.”

---

## The Core Problem

Cached data becomes **stale** when:
- Database values change
- Business rules update data
- Writes happen from multiple sources

If stale data is served:
- Users see incorrect information
- Systems behave inconsistently
- Bugs appear “randomly”

---

## Common Cache Invalidation Strategies

---

## 1. Time-Based Expiration (TTL)

### How It Works
- Cached data expires after a fixed time
- Automatically removed or refreshed

Example:
- Cache user profile for 5 minutes

---

### Pros
- Simple
- No coordination needed
- Works well for read-heavy systems

---

### Cons
- Data can be stale until TTL expires
- Choosing TTL is guesswork

---

### Best Used When
- Slight staleness is acceptable
- Data changes infrequently
- High traffic, low risk

---

## 2. Write-Through Cache

### How It Works
- Data is written to:
  - Database
  - Cache
- Cache is always updated on write

---

### Pros
- Cache always has fresh data
- Simple read logic

---

### Cons
- Slower writes
- Cache becomes tightly coupled to DB writes

---

### Best Used When
- Strong consistency is required
- Write volume is moderate

---

## 3. Write-Behind (Write-Back) Cache

### How It Works
- Write goes to cache first
- Cache asynchronously updates database

---

### Pros
- Very fast writes
- Good for high-throughput systems

---

### Cons
- Risk of data loss if cache fails
- More complex recovery logic

---

### Best Used When
- Performance is critical
- Eventual consistency is acceptable

---

## 4. Explicit Invalidation (Cache Busting)

### How It Works
- Cache entries are manually deleted or updated when data changes

Example:
- User updates profile → invalidate `user:{id}` cache

---

### Pros
- Strong consistency
- Immediate freshness

---

### Cons
- Complex logic
- Easy to miss edge cases
- Tight coupling between app and cache

---

### Best Used When
- Critical data correctness matters
- Cache keys are predictable

---

## 5. Versioned Cache Keys

### How It Works
- Cache keys include a version
- On update, version changes

Example:
product:v3:123


---

### Pros
- Old cache naturally ignored
- No need to delete old keys immediately

---

### Cons
- Cache size grows
- Old data cleaned up only by TTL or eviction

---

### Best Used When
- Schema changes
- Large content updates
- CDN or static asset caching

---

## 6. Event-Based Invalidation

### How It Works
- Data change emits an event
- Consumers invalidate or update cache

Example:
- DB update → event → cache invalidation

---

### Pros
- Scales well
- Decoupled architecture
- Works across services

---

### Cons
- Event delivery delays
- Requires reliable messaging

---

### Best Used When
- Microservices architecture
- Multiple consumers share cached data

---

## 7. Read-Through Cache with Revalidation

### How It Works
- Cache miss triggers fetch from source
- Cache updated with fresh data

---

### Pros
- Simple read path
- Cache self-heals

---

### Cons
- Stale data until miss
- Thundering herd risk

---

### Best Used When
- Data changes occasionally
- Read-heavy workloads

---

## Cache Invalidation Patterns by Layer

---

### Application Cache
- Explicit invalidation
- TTL + versioned keys
- Event-based updates

---

### CDN Cache
- TTL-based
- Cache-Control headers
- Invalidation APIs
- Versioned URLs

---

### Database Cache
- Short TTLs
- Read-through caching
- Minimal logic

---

## Common Cache Invalidation Mistakes

- Caching mutable data forever
- Forgetting multi-key dependencies
- Invalidating only one layer
- No monitoring of stale reads
- Overengineering early

Caches should **fail safely**, not silently.

---

## Cache Invalidation vs Cache Consistency

| Strategy | Consistency | Complexity |
|-------|------------|-----------|
| TTL | Eventual | Low |
| Write-through | Strong | Medium |
| Explicit invalidation | Strong | High |
| Event-based | Eventual → Strong | High |
| Versioned keys | Eventual | Medium |

Pick based on **business impact**, not purity.

---

## Observability for Invalidation

Track:
- Cache hit ratio
- Stale read incidents
- Invalidation failures
- Cache eviction rate
- Latency during misses

If you don’t measure staleness, you’re guessing.

---

## Practical Rule of Thumb

- Start with **TTL**
- Add **explicit invalidation** for critical paths
- Use **events** when systems grow
- Avoid write-behind unless you really know why

---

## Key Takeaways

- Cache invalidation is unavoidable
- No single strategy fits all use cases
- Strong consistency increases complexity
- TTL is the safest default
- Invalidation logic must evolve with scale

---

## Mental Model

Caching speeds up reads.  
Invalidation protects correctness.

Speed without correctness is just fast failure.
