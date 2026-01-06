# CDN Role in Performance

---

## What Is a CDN?

A **Content Delivery Network (CDN)** is a **globally distributed network of servers** that delivers content to users from the **closest possible location**.

Instead of every user hitting your origin server:
- Users hit a nearby edge server
- The edge serves cached content
- The origin is protected from traffic spikes

---

## Why CDNs Exist

Without a CDN:
- All traffic goes to one region
- Latency increases with distance
- Origin servers get overloaded
- Global users suffer

CDNs exist to:
- Reduce latency
- Reduce load on origin servers
- Improve availability
- Handle traffic spikes gracefully

---

## Where a CDN Sits in the Request Path

Typical flow:

User  
→ **CDN Edge**  
→ Origin (only if cache miss)

Most requests never reach the origin.

---

## How CDNs Improve Performance

### 1. Reduced Latency
- Content served from edge locations close to users
- Fewer network hops
- Faster Time To First Byte (TTFB)

Distance matters. Physics is rude and unavoidable.

---

### 2. Caching Static Content
CDNs cache:
- Images
- CSS / JS
- Videos
- Fonts
- Static HTML

These files:
- Rarely change
- Are requested often
- Are expensive to serve repeatedly

Perfect cache candidates.

---

### 3. Offloading Origin Servers
CDNs:
- Absorb most read traffic
- Reduce database and app load
- Prevent origin overload during spikes

Your backend gets to breathe.

---

### 4. Parallelism and Optimized Delivery
CDNs:
- Use optimized TCP/TLS stacks
- Support HTTP/2 and HTTP/3
- Deliver assets in parallel

Faster delivery without changing your app code.

---

## CDN and Dynamic Content

Modern CDNs don’t only cache static files.

They can:
- Cache API responses (carefully)
- Use short TTLs
- Perform conditional requests
- Support edge logic

Still, the **source of truth remains the origin**.

---

## Cache Behavior at the Edge

### Cache Hit
- Served immediately
- Lowest latency
- Zero origin load

---

### Cache Miss
- CDN forwards request to origin
- Response cached (if allowed)
- Returned to user

---

### Cache Expiration
Controlled by:
- Cache-Control headers
- TTL
- Invalidation rules

---

## CDN Cache Control Basics

Common headers:
- `Cache-Control`
- `Expires`
- `ETag`
- `Last-Modified`

These decide:
- What gets cached
- For how long
- When to revalidate

CDNs follow your instructions. Bad headers mean bad caching.

---

## CDN and Scalability

CDNs scale by default.

They help with:
- Flash traffic
- Viral content
- Product launches
- DDoS-like traffic patterns

Without rewriting your backend.

---

## CDN and Reliability

CDNs improve availability by:
- Serving cached content during origin outages
- Routing around failed regions
- Acting as a protective buffer

Even if the origin is struggling, users may still get responses.

---

## CDN vs Application Cache

| Aspect | CDN | Application Cache |
|-----|-----|------------------|
| Location | Edge (near user) | Near application |
| Best for | Static & semi-static content | Business data |
| Latency | Very low | Low |
| User awareness | Transparent | App-controlled |
| Scope | Global | Service-level |

They are complementary, not competitors.

---

## CDN and Security (Performance Side Effects)

Security features that also help performance:
- TLS termination at edge
- Rate limiting
- Bot filtering
- DDoS absorption

Less junk traffic reaches your app.

---

## When CDN Helps the Most

- Global user base
- Media-heavy applications
- Read-heavy workloads
- Public websites
- APIs with cacheable responses

---

## When CDN Helps Less

- Highly personalized responses
- Non-cacheable dynamic content
- Internal-only services

Even then, it can still reduce connection overhead.

---

## Common CDN Mistakes

- Caching everything blindly
- Forgetting cache invalidation
- Setting TTLs too long
- Not monitoring cache hit ratio
- Treating CDN as a database

CDN is fast storage, not a source of truth.

---

## Observability for CDN Performance

Track:
- Cache hit ratio
- Edge latency
- Origin request rate
- Error rates
- Regional performance

If your CDN hit rate is low, you’re paying for decoration.

---

## Key Takeaways

- CDN reduces latency by serving content closer to users
- It offloads traffic from origin servers
- It improves scalability and availability
- It complements application and database caching
- CDN performance depends on correct cache headers

---

## Mental Model

CDN is a **global read-optimization layer**.

Your backend does the thinking.  
The CDN does the repeating.
