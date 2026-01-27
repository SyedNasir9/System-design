# Caching and Compression to Cut Bandwidth

---

## What This Means

**Caching** avoids sending the same data repeatedly.  
**Compression** reduces the size of data when it *must* be sent.

Together, they:
- cut bandwidth usage
- reduce latency
- lower infrastructure cost
- improve user experience

Core idea:
> The fastest request is the one you don’t make. The second fastest is the smallest one.

---

## Why This Exists

Bandwidth is expensive and latency-sensitive:
- mobile users pay for data
- cross-region traffic costs money
- large responses slow everything down
- spikes amplify costs fast

Caching + compression exist to:
- reduce repeated data transfer
- shrink payload sizes
- absorb traffic spikes
- protect origins from overload

---

# 1) Caching: Don’t Send It Again

---

## Where Caching Happens (Layers)

Caching works best when applied **as close to the user as possible**:

1. **Browser cache**
2. **CDN / Edge cache**
3. **Reverse proxy cache** (NGINX, Envoy, Varnish)
4. **Application cache** (in-memory)
5. **Distributed cache** (Redis/Memcached)

Each layer reduces traffic to the next one.

Rule:
> Every cache hit is bandwidth you didn’t pay for.

---

## What to Cache

Good candidates:
- static assets (JS, CSS, images, fonts)
- API GET responses
- configuration and metadata
- public or semi-public content
- computed results

Bad candidates:
- highly dynamic data with strict freshness
- personalized data (unless cache keying is perfect)
- sensitive data without strong isolation

---

## Cache Headers (The Real Controls)

Important headers:
- `Cache-Control: max-age=`
- `Cache-Control: public | private`
- `s-maxage` (for shared caches like CDN)
- `ETag` / `If-None-Match`
- `Last-Modified`

Best practice:
- **immutable assets** → long TTL + hashed filenames
- **HTML/APIs** → short TTL or conditional caching

---

## Cache Validation (Save Bandwidth Even on Miss)

Conditional requests:
- client sends `If-None-Match` or `If-Modified-Since`
- server replies `304 Not Modified`
- no payload sent

This still saves bandwidth even when cache expires.

---

# 2) Compression: Send Less Data

---

## What Compression Does

Compression reduces response size before sending it over the wire.

Common algorithms:
- **Gzip** (widely supported)
- **Brotli** (better compression, especially for text)
- **Zstd** (great ratio, not universally supported yet)

Text compresses extremely well.
Binary files usually don’t.

---

## What to Compress

Compress:
- HTML
- JSON
- CSS
- JS
- SVG
- text-based logs/responses

Do NOT compress:
- already compressed formats (JPEG, PNG, MP4, ZIP)
- encrypted or random data

Rule:
> Compress text. Don’t double-compress blobs.

---

## Compression Trade-Offs

Compression costs:
- CPU time
- memory

So:
- enable compression at **edge/CDN** when possible
- avoid compressing tiny responses
- reuse compressed variants via caching

---

# 3) Caching + Compression Together (Best Combo)

---

## How They Work Together

- CDN caches **compressed versions** per encoding
- browser sends `Accept-Encoding`
- CDN responds with cached gzip/br version
- origin doesn’t get hit at all

Result:
- minimal bandwidth
- minimal latency
- minimal origin load

---

## Vary Header (Critical)

Use:
