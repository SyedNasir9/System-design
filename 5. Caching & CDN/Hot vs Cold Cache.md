# Hot Cache vs Cold Cache

---

## What Do “Hot” and “Cold” Cache Mean?

**Hot cache** and **cold cache** describe the **state of a cache**, not a type of cache.

- **Hot Cache:** Cache is already populated with frequently accessed data
- **Cold Cache:** Cache is empty or missing required data

This difference directly impacts **latency, load, and stability**.

---

## Cold Cache

### Definition
A **cold cache** occurs when:
- Cache is empty
- Cache has been recently restarted
- Data has expired or been evicted

Every request results in a **cache miss**.

---

### What Happens in a Cold Cache

1. Request arrives
2. Cache miss
3. Data fetched from database or upstream service
4. Cache populated
5. Response returned

This is slow and expensive.

---

### Characteristics

- High latency
- High database load
- Low cache hit ratio
- Risk of traffic spikes (stampede)

---

### Common Causes

- Application restart
- Cache eviction
- New deployment
- First request for new data
- Region expansion

---

## Hot Cache

### Definition
A **hot cache** contains:
- Frequently requested data
- Recently accessed entries
- Preloaded or warmed content

Most requests result in **cache hits**.

---

### What Happens in a Hot Cache

1. Request arrives
2. Cache hit
3. Response returned immediately

Fast, cheap, and stable.

---

### Characteristics

- Low latency
- Minimal database load
- High cache hit ratio
- Predictable performance

---

## Comparison Table

| Aspect | Cold Cache | Hot Cache |
|-----|-----------|----------|
| Cache state | Empty / sparse | Fully populated |
| Latency | High | Low |
| DB load | High | Low |
| User experience | Poor | Smooth |
| Risk | Stampede | Minimal |

---

## Cache Warming

### What Is Cache Warming?

**Cache warming** is the process of **preloading data into cache** before real user traffic arrives.

---

### Why Cache Warming Matters

- Prevents cold-start latency
- Protects database
- Stabilizes deployments

---

### Common Warming Strategies

- Preload popular keys on startup
- Replay recent traffic
- Scheduled background refresh
- Lazy warming (natural access)

---

## Cold Cache vs Cache Miss

Not the same thing:

- **Cache miss:** One key not present
- **Cold cache:** Most or all keys missing

Cold cache = many cache misses at once.

---

## Cache Stampede Risk

A cold cache can trigger a **cache stampede**:
- Many requests miss cache simultaneously
- All hit the database
- Database becomes overloaded

This is how “everything was fine until it wasn’t” happens.

---

## Mitigation Techniques

- Request coalescing
- Locking on cache rebuild
- Stale-while-revalidate
- Rate limiting
- Gradual rollout

Cold caches are dangerous when traffic is high.

---

## Real-World Examples

### CDN
- Cold cache after deployment or purge
- First users pay latency cost
- Later users benefit

---

### Application Cache
- Restart clears in-memory cache
- DB gets hammered temporarily

---

### Database Cache
- Cold buffer pool after restart
- Disk reads spike

---

## When Cold Cache Is Acceptable

- Low traffic systems
- Non-critical paths
- Batch jobs
- Internal tools

For user-facing systems, cold cache is a liability.

---

## Key Takeaways

- Hot vs cold is about **cache state**
- Cold cache causes latency spikes
- Hot cache enables predictable performance
- Cache warming reduces cold-start pain
- Design must assume cache will fail

---

## System Design Mental Model

Cold cache tests system **resilience**.  
Hot cache shows system **efficiency**.

If your system only works when the cache is hot, it’s fragile.
