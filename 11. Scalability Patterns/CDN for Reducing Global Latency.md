# CDN for Reducing Global Latency

---

## What This Means

A **CDN (Content Delivery Network)** is a network of geographically distributed servers (edge locations / PoPs) that **cache and serve content closer to users** instead of making every request travel to your origin server.

Core idea:
> The internet is slow because physics exists. CDNs help you argue with physics using money and caching.

---

## Why This Exists

Without a CDN:
- users far from your origin get **high latency**
- your origin gets hammered by global traffic
- spikes become outages
- large static files (images/videos/JS/CSS) load painfully slow
- TLS handshakes and long-distance connections waste time

CDNs exist to:
- reduce **global latency** (shorter network distance)
- reduce **origin load** (edge caching)
- improve **availability** (serve cached content even if origin is struggling)
- absorb **DDoS** and traffic spikes
- optimize delivery (compression, HTTP/2/3, TCP tuning)

---

# 1) How a CDN Reduces Latency

---

## What Actually Gets Faster

A CDN improves latency by:
- serving responses from a **nearby edge PoP**
- reducing **RTT (round-trip time)** from user ↔ server
- reusing connections from edge ↔ origin (fewer expensive handshakes)
- reducing origin CPU/network work via caching

The biggest win is usually:
- static assets (JS/CSS/images/fonts)
- large media files
- cacheable API responses (carefully)

---

## The Request Flow (Simple)

1. User requests `https://cdn.example.com/app.js`
2. DNS routes user to nearest CDN PoP
3. CDN checks cache:
   - **HIT** → serve from edge (fast)
   - **MISS** → fetch from origin, cache it, serve to user

---

# 2) What You Can Put Behind a CDN

---

## Best Fit (Common)

- images, videos, downloads
- JS/CSS bundles
- fonts
- static website pages
- public API GET responses (with correct caching headers)

---

## Possible But Needs Care

- authenticated content (must prevent caching leaks)
- personalized pages (usually not cacheable)
- dynamic APIs (often cacheable only with short TTL or specific keys)

Rule:
> CDN caching + auth + personalization is where bugs turn into “security incidents”.

---

# 3) Caching Behavior (How the CDN Decides)

---

## Cache Keys

A CDN stores objects based on a **cache key**, commonly built from:
- URL path
- query string (all or selected params)
- headers (e.g., `Accept-Encoding`, sometimes auth-related headers if explicitly configured)

If your cache key is wrong, you can:
- serve wrong variants
- leak private data
- destroy hit rate

---

## TTL and Headers

Most CDNs obey standard cache headers:
- `Cache-Control: max-age=...`
- `s-maxage` (shared caches)
- `ETag` / `If-None-Match`
- `Last-Modified` / `If-Modified-Since`

Common patterns:
- **immutable assets**: long TTL (days/months) + hashed filenames
- **HTML**: short TTL (seconds/minutes) or no-cache, depending on site
- **APIs**: short TTL or conditional caching with validation

---

## Cache Invalidation (Purging)

If you deploy a new version, you need one of:
- **cache busting** (best): change filename using content hash  
  Example: `app.a1b2c3.js`
- **purge/invalidate** (okay): tell CDN to delete cached objects
- **short TTL** (fallback): wait for cache expiry

Best practice:
> Use hashed filenames so you don’t need to purge on every deploy.

---

# 4) CDN Patterns for Web Apps

---

## Pattern A: Static SPA + API Origin

- CDN serves SPA assets (HTML/CSS/JS)
- API stays behind a separate domain (or same with careful rules)
- assets get long TTL, API mostly not cached (or selectively cached)

---

## Pattern B: Full-Site CDN (Edge Cached HTML)

- CDN caches HTML pages too (especially for marketing sites)
- uses short TTL + stale-while-revalidate for freshness
- origin protected from spikes

---

## Pattern C: Multi-Region Origins + CDN

- CDN fetches from the nearest/healthiest origin region
- better global performance + improved resilience

---

# 5) CDN for APIs (When It Makes Sense)

---

## Cacheable API Reads

CDN can cache:
- `GET /products`
- `GET /public/catalog`
- `GET /docs`

You typically avoid caching:
- POST/PUT/DELETE
- personalized responses
- anything with sensitive data unless you use strict cache controls

Useful tricks:
- `Cache-Control: private` vs `public`
- `Vary` headers for response variants
- signed URLs / tokens for controlled access to cached objects

---

# 6) DevOps Responsibilities (The Part You Actually Touch)

---

## What DevOps Sets Up

- DNS + CDN distribution configuration
- TLS certificates at the edge
- origin protection (WAF, rate limiting)
- cache behaviors (paths, headers, query string rules)
- compression and protocol tuning (HTTP/2/3)
- logging (access logs, real-time metrics)
- deploy strategy (asset hashing + invalidations)

A CDN is “set and forget” only in fairy tales and vendor marketing.

---

# 7) Observability (How You Know It’s Working)

---

## Key CDN Metrics

- **cache hit ratio**
- **edge latency** (TTFB at edge)
- **origin fetch rate** (how often CDN hits your origin)
- **origin latency** (if misses are slow, users still suffer)
- **4xx/5xx** at edge and origin
- **bytes served** (bandwidth)
- **top objects** / hot paths
- **geo distribution** (where traffic is coming from)

Goal:
> High hit ratio + low edge latency + low origin load.

---

# 8) Common Mistakes

- not using hashed filenames, then purging everything every deploy
- caching personalized/authenticated content by accident
- including useless query params in cache key (destroys hit rate)
- ignoring `Vary` headers (wrong content served to wrong clients)
- TTL too long for HTML (users see old site)
- TTL too short for static assets (origin gets hammered)
- assuming CDN fixes backend slowness (it only hides it for cacheable content)

---

## Interview-Ready Summary

> A CDN reduces global latency by serving cached content from edge locations near users, reducing round-trip time and offloading origin servers. Requests are routed to a nearby PoP; cache hits are served immediately, while misses fetch from origin and populate the cache. CDNs work best for static assets and can also cache safe, cacheable API GET responses when cache keys and headers are correctly designed. Key operational concerns are cache invalidation (prefer hashed filenames), correct cache-key design to avoid leaks or low hit rates, and observability via hit ratio, edge/origin latency, and origin fetch rate.

---

## Final Takeaway

CDNs are how you make a global app feel less global and more “instant”.
They don’t remove latency, they relocate work:
- do more at the edge
- do less at the origin
- annoy physics slightly less
