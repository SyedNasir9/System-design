# Caching for Performance

## What Is Caching?
Caching is the practice of storing frequently accessed data in fast storage, typically in memory, so future requests can be served without querying slower backend systems.

The primary goal of caching is to reduce latency and decrease load on downstream services such as databases.

---

## Why Caching Helps
When used correctly, caching provides several benefits:

- Faster response times for users
- Reduced load on databases and backend services
- Lower infrastructure and operational costs
- Improved system scalability under high traffic

Caching often turns expensive or slow operations into near-instant responses.

---

## Trade-offs and Challenges
Caching introduces complexity and must be handled carefully:

- **Cache invalidation is hard**: keeping cached data in sync with the source of truth
- **Risk of stale data**: users may temporarily see outdated information
- **Added complexity**: additional infrastructure and failure modes

Caching improves performance, but incorrect caching can lead to subtle and hard-to-debug issues.

---

## Common Caching Strategies

### Time To Live (TTL)
Cached data expires after a fixed period and is refreshed when requested again.

### Write-Through Cache
Writes update both the cache and the database simultaneously, helping maintain consistency.

### Write-Around Cache
Writes go directly to the database and invalidate the cache, reducing unnecessary cache writes.

### Write-Behind Cache
Writes are acknowledged after updating the cache and persisted to the database asynchronously, improving write latency but increasing risk.

---

## Key Takeaway
Caching can dramatically improve performance and reduce costs, but correctness and data freshness must be carefully managed.
