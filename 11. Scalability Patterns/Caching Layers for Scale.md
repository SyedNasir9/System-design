# Caching Layers for Scale

---

## What This Means

A **caching layer** is a fast storage layer that keeps **frequently used data closer to the app/user** so you don’t keep hammering slow systems (usually your database).

Core idea:
> Your database is not a punching bag. Cache is how you stop treating it like one.

---

## Why This Exists

Systems add caching to:
- reduce **latency** (fast reads)
- reduce **load on databases/services**
- handle **traffic spikes**
- improve **availability** (serve stale data when origin is struggling)
- cut costs (fewer expensive DB reads)

Without cache, “scale” often becomes “DB is on fire”.

---

# 1) Where Caching Fits in the Architecture

---

## Common Cache Layers (From Closest to User to Deepest)

1. **Browser / Client cache**
2. **CDN cache** (edge)
3. **API gateway / reverse proxy cache** (like NGINX, Varnish)
4. **Application in-memory cache** (per instance)
5. **Distributed cache** (Redis/Memcached)
6. **Database cache** (buffer pool, query cache depending on DB)

Most real systems use multiple layers, because humans love complexity.

---

## Cache vs Database (What Cache Is NOT)

Cache should be:
- fast
- cheap
- disposable

Cache should NOT be:
- the source of truth
- the only place data lives
- relied on for durability

Rule:
> Cache is an optimization layer, not a data storage strategy.

---

# 2) What You Cache

---

## Good Things to Cache

- read-heavy data (product catalog, config, feature flags)
- computed results (expensive aggregation)
- user profile data (with careful invalidation)
- auth tokens / sessions (short-lived)
- permissions/ACL checks (carefully TTL’d)
- API responses (if they’re safe and cacheable)

## Bad Things to Cache (Unless You Like Bugs)

- rapidly changing data with strict correctness needs (balances, payments)
- data requiring per-user privacy unless your keying is perfect
- anything where stale results create serious harm

Caching “money” data is how you create “free money” incidents.

---

# 3) Cache Strategies (How Data Gets In)

---

## Strategy A: Cache-Aside (Lazy Loading)

Workflow:
1. App reads from cache
2. If miss → read from DB
3. Store result in cache with TTL
4. Return response

Pros:
- simple
- cache only what’s needed

Cons:
- first request is slow (miss)
- risk of cache stampede on popular keys

Best for:
- most typical app caching

---

## Strategy B: Read-Through Cache

Workflow:
- App asks cache for data
- cache automatically loads from DB on miss

Pros:
- app logic cleaner
- consistent loading behavior

Cons:
- cache layer becomes more complex
- needs good error handling and fallback

Best for:
- platforms with shared caching libraries

---

## Strategy C: Write-Through Cache

Workflow:
- writes go to cache and DB together (cache updated immediately)

Pros:
- cache always warm for written keys
- consistent reads after writes

Cons:
- write latency increases
- cache must be highly available
- still needs expiry/eviction strategy

Best for:
- when read-your-writes is important

---

## Strategy D: Write-Back (Write-Behind)

Workflow:
- writes go to cache first
- async flush to DB later

Pros:
- very fast writes

Cons:
- data loss risk if cache dies
- complex consistency guarantees

Best for:
- specialized systems where you can tolerate eventual persistence

---

# 4) Cache Invalidation (The Hard Part)

---

## Common Invalidation Methods

### TTL-based (Time To Live)
- data expires after X seconds/minutes

Pros:
- simple
Cons:
- data can be stale until TTL expires

### Event-based Invalidation
- on update, publish event → delete/update cache key

Pros:
- fresher data
Cons:
- more moving parts, more failure modes

### Versioned Keys
- include a version in the key (e.g., `user:123:v7`)
- bump version on update

Pros:
- avoids tricky deletes
Cons:
- old keys linger until eviction

Rule:
> Invalidation is where caching stops being fun and becomes “systems design”.

---

# 5) Avoiding Cache-Related Disasters

---

## Cache Stampede (Thundering Herd)

Problem:
- popular key expires
- thousands of requests miss together
- all hit DB simultaneously
- DB dies
- cache “worked” right up until it didn’t

Fixes:
- **request coalescing / singleflight** (only one loader)
- **jittered TTL** (randomize expiry)
- **stale-while-revalidate** (serve stale while one request refreshes)
- **soft TTL + hard TTL** (refresh early)

---

## Cache Penetration (Misses for Non-Existent Data)

Problem:
- attackers or bugs request random keys
- cache misses always go to DB
- DB suffers

Fixes:
- cache “not found” results briefly (negative caching)
- validate inputs
- rate limit

---

## Cache Hot Keys

Problem:
- one key gets massive traffic
- single cache node becomes bottleneck

Fixes:
- key splitting (shard key by small suffix)
- local cache in front of distributed cache
- replicate hot data
- use CDN/proxy caching for public data

---

# 6) Consistency Patterns

---

## Read-Your-Writes

Users expect:
- after update, they immediately see the update

Options:
- bypass cache for a short time after write
- write-through caching
- invalidate keys on write
- pin reads to primary source temporarily

---

## Stale Data Tolerance

Decide per endpoint:
- can it be stale for 5 seconds?
- 1 minute?
- never?

Best practice:
> Treat staleness as a product decision, not an accident.

---

# 7) Caching in DevOps Workflows

---

## How DevOps Uses Caching

- CDN caching for static assets and APIs to reduce origin load
- Redis/Memcached clusters as shared infra services
- caching for build pipelines (dependency caches) to speed CI/CD
- edge caching + WAF for resilience during spikes
- cache-aware autoscaling and SLO alerting

DevOps work here is:
- provisioning caches (IaC)
- making them highly available
- monitoring, backup strategy (if needed), upgrades
- tuning TTLs and eviction policies
- incident response when cache becomes the bottleneck

---

# 8) Observability for Caching

---

## Metrics That Matter

- **hit rate** (hits / total)
- **miss rate**
- **p95/p99 latency** of cache operations
- **evictions** (memory pressure)
- **memory usage**
- **hot keys**
- **backend fallback rate** (DB calls triggered by cache misses)
- **error rate** (timeouts, connection errors)

Hit rate alone is not enough.
A cache with high hit rate can still be slow or overloaded.

---

# 9) Common Mistakes

- using cache as the source of truth
- no TTLs (stale forever = wrong forever)
- not caching “not found” results (DB gets hammered)
- one global TTL causing stampede
- caching sensitive data without proper keying/segmentation
- ignoring eviction behavior (LRU/LFU) and memory sizing
- forgetting cache in deployments (schema changes break cached objects)

---

## Interview-Ready Summary

> Caching layers improve scalability by serving frequently accessed data from faster storage closer to the application or user, reducing latency and offloading databases and downstream services. Common layers include browser, CDN, proxy, in-process, and distributed caches (Redis/Memcached). Typical strategies are cache-aside, read-through, and write-through, each with trade-offs in complexity and consistency. The hardest parts are invalidation and failure modes such as stampede, penetration, and hot keys, which are mitigated using TTL jitter, stale-while-revalidate, request coalescing, and negative caching. Effective cache design is driven by user staleness tolerance and monitored via hit rate, latency, evictions, and backend fallback rate.

---

## Final Takeaway

Caching is how you scale reads without turning your database into a crime scene.
Just remember:
- cache is disposable
- invalidation is hard
- retries + stampedes are brutal
- stale data must be a decision, not a surprise
