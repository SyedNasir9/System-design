# DDoS Protection

---

## What This Means

**DDoS (Distributed Denial of Service)** is when an attacker uses many machines (bots) to flood your system with traffic so real users can’t use it.

**DDoS protection** is the set of controls that keep your service available under abusive traffic.

Core idea:
> It’s not “hacking” in the movie sense. It’s more like 10 million people trying to enter the same door at once, on purpose.

---

## Why This Exists

Without DDoS protection you risk:
- outages (service becomes unreachable)
- massive cloud bills (bandwidth + autoscaling cost)
- false positives in monitoring (everything looks “down”)
- cascading failures (DB, caches, queues get overwhelmed)
- “we scaled!” followed by “we scaled our bill!”

Protection exists to:
- keep availability and latency stable under attack
- absorb/bounce abusive traffic at the edge
- protect your origin and stateful dependencies
- enforce fair usage

---

# 1) Types of DDoS Attacks (What You’re Defending Against)

---

## A) Volumetric Attacks (Bandwidth Flood)
Goal: saturate network links.
Examples:
- UDP floods
- amplification attacks (DNS/NTP/CLDAP/Memcached reflection)

Symptoms:
- bandwidth spikes
- upstream congestion
- packets dropped before your servers even see requests

---

## B) Protocol Attacks (Connection/State Exhaustion)
Goal: exhaust load balancers, firewalls, or server connection tables.
Examples:
- SYN flood
- TCP connection floods
- SSL/TLS renegotiation/handshake abuse (resource-heavy)

Symptoms:
- huge concurrent connections
- LB/edge CPU spikes
- timeouts even at low “request” counts

---

## C) Application-Layer Attacks (L7)
Goal: look like legitimate HTTP traffic but overwhelm your app.
Examples:
- HTTP GET/POST floods to expensive endpoints
- login brute-force (credential stuffing overlaps here)
- slowloris (slow request trickle)

Symptoms:
- normal-looking HTTP traffic
- high CPU on app servers
- DB/Redis hammered
- high p95/p99 latency and 5xx

---

# 2) The “Defense in Depth” Model

---

## Layer 0: Network Edge (Upstream Scrubbing / Anycast)
Best place to stop volumetric floods:
- anycast edge networks
- ISP/provider scrubbing centers

Why:
> If your link is saturated, no amount of application tuning matters.

---

## Layer 1: CDN + Edge Caching
A CDN can:
- absorb traffic at edge PoPs
- serve cached content without touching origin
- hide your origin IP (important)

Works great for:
- static assets
- cacheable pages/APIs

---

## Layer 2: WAF (Web Application Firewall)
Stops/limits common L7 abuse:
- suspicious patterns
- known bad bots
- OWASP-style payloads
- geo/rate restrictions
- bot management (fingerprinting)

WAF is not perfect, but it filters a lot of garbage cheaply.

---

## Layer 3: Rate Limiting + Throttling
Controls request volume by:
- IP
- user/account
- API key
- token/client id
- endpoint

Common policies:
- global rate limit
- per-route limits (`/login`, `/search`, `/checkout`)
- burst + sustained limits (token bucket/leaky bucket)

Important:
> Rate limiting is how you stop “one client” from becoming “your entire traffic”.

---

## Layer 4: Load Balancer Protections
LB-level controls:
- connection limits
- SYN cookies / TCP protections (platform-dependent)
- request size limits
- timeouts (carefully)
- keep-alive tuning
- circuit breakers to protect backends

---

## Layer 5: App + Dependency Hardening
Because L7 attacks target expensive work:
- cache hot reads
- protect DB with query limits and indexes
- use queues for heavy work
- degrade gracefully (serve partial results)
- disable expensive features during attack (feature flags)

---

# 3) DDoS Protection Workflow (Operational)

---

## Step 1: Detect
Signals:
- traffic spikes (RPS, bandwidth)
- connection count spikes
- WAF blocked requests rising
- increased 4xx/5xx
- p95/p99 latency jump
- sudden geo/IP distribution changes

---

## Step 2: Classify the Attack
- Volumetric? (bandwidth)
- Protocol? (connections)
- L7? (targeting endpoints)

Correct classification matters because:
- wrong mitigation wastes time and breaks legit users

---

## Step 3: Mitigate at the Highest Layer Possible (Closest to Edge)
Typical order:
1. CDN rules / cache more aggressively
2. WAF rules (bot blocks, signatures, geo)
3. rate limits per endpoint
4. tighten LB limits/timeouts
5. protect origin (temporary allowlists, blocklists)
6. scale selectively (only if it helps and won’t bankrupt you)

---

## Step 4: Protect Dependencies
Even if edge holds, L7 can still melt:
- DB (connection pool limits)
- caches (hot key protection)
- queues (backpressure)

Use:
- circuit breakers
- bulkheads (per-service limits)
- load shedding (return 429/503 early)

---

## Step 5: Post-Incident
- analyze logs and traffic patterns
- tune WAF/rate limits
- add caching for expensive GETs
- fix endpoints that are too expensive per request
- update runbooks and alerts

---

# 4) Key Design Patterns

---

## Pattern A: Hide the Origin
- origin should not be directly reachable from the internet
- only accept traffic from CDN/WAF IP ranges (or private connectivity)

Why:
> If attackers can hit origin directly, they bypass your expensive defenses.

---

## Pattern B: “Cache the Front Door”
- cache landing pages, static assets, and safe GET endpoints at CDN
- set correct cache headers
- use stale-while-revalidate during stress

This reduces origin exposure dramatically.

---

## Pattern C: Rate Limit High-Risk Endpoints
- `/login`, `/signup`, `/search`, `/password-reset`
- add CAPTCHA / challenge when thresholds exceeded
- per-account and per-IP limits

---

## Pattern D: Graceful Degradation
During attack:
- disable non-essential features (recommendations, heavy search)
- return cached/stale content
- serve “read-only mode”
- prioritize critical paths (checkout, auth)

---

# 5) Metrics You Should Track

---

## Edge / CDN / WAF
- requests allowed vs blocked
- challenge rates (CAPTCHA/JS challenge)
- top countries/ASNs
- cache hit ratio
- edge latency (TTFB)

## Load Balancer
- active connections
- new connection rate
- request rate
- 4xx/5xx
- timeouts

## App + Dependencies
- p95/p99 latency
- error rate
- CPU/memory
- DB QPS, slow queries
- connection pool saturation
- queue depth

---

# 6) Common Mistakes

---

- no CDN/WAF, origin exposed to the internet
- only scaling app servers (attack just scales your bill)
- rate limiting only by IP (NAT/shared IPs cause false blocks)
- blocking too aggressively and taking yourself down
- not protecting the database (the real victim)
- no runbooks or automation for mitigation
- ignoring egress costs during volumetric events

Worst mistake:
> Thinking DDoS is purely “a network problem”. L7 DDoS is an “expensive endpoint” problem.

---

## Interview-Ready Summary

> DDoS protection keeps services available under abusive traffic by using defense-in-depth: upstream/anycast scrubbing for volumetric floods, CDN caching to absorb traffic at the edge, WAF rules and bot management for L7 filtering, rate limiting for fair usage, and load balancer/app hardening to prevent connection and dependency exhaustion. Operationally, teams detect anomalies, classify the attack type (volumetric, protocol, application-layer), mitigate at the edge first, protect stateful dependencies with backpressure and circuit breakers, and refine controls post-incident based on traffic analysis.

---

## Final Takeaway

DDoS protection is basically:
- stop junk traffic early (edge)
- limit expensive work (app)
- protect stateful systems (DB/cache)
- have a plan that works at 3 AM

Attackers bring traffic.
You bring architecture and policies.
