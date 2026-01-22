# Load Balancer Scaling

---

## What This Means (Without the Fairy Dust)

**Load balancer scaling** is the set of patterns used to keep traffic distribution reliable when request volume, connection count, or target instances grow (or shrink).

Core idea:
> One load balancer is a single point of sadness. Scaling is how you avoid that.

---

## Why This Exists

If you don’t scale load balancing properly, you get:
- **bottlenecks at the edge** (LB CPU/memory/conn limits)
- **uneven traffic** (some instances melt, others nap)
- **bad latency under spikes** (queueing + retries = doom loop)
- **connection storms** (especially websockets / gRPC / long-lived TCP)
- **regional outages** because everything funnels through one place

Load balancer scaling exists to:
- keep **throughput** and **latency** stable under growth
- remove **single points of failure**
- support **multi-AZ / multi-region**
- handle modern traffic patterns (TLS, HTTP/2, gRPC, websockets)

---

# 1) The Things That Actually Limit a Load Balancer

---

## Scaling Constraints You Must Respect

A load balancer is not magic, it’s software/hardware with limits:

- **RPS / throughput** (requests per second, Mbps/Gbps)
- **concurrent connections** (especially for long-lived connections)
- **TLS handshakes** and crypto overhead
- **L7 features cost** (WAF, auth, routing rules, rewrites)
- **health check load** (too many targets = too many probes)
- **statefulness** (sticky sessions, connection pinning)
- **control-plane limits** (rule count, target registration rate, config size)

If you scale your backend but not these, you just move the outage up one layer.

---

## L4 vs L7 and Why It Matters

- **L4 (TCP/UDP)**: faster, less CPU, fewer features, scales better for raw throughput
- **L7 (HTTP)**: routing, headers, auth, WAF, but heavier and more complex

Rule of life:
> If you need “smart,” you pay. If you need “fast,” keep it dumb.

---

# 2) Horizontal Scaling Patterns

---

## Pattern A: Make the Load Balancer Itself a Cluster

Most modern managed LBs already do this:
- multiple nodes behind a single “LB endpoint”
- auto-scale based on load
- multi-AZ redundancy

What you still must design:
- cross-zone behavior (on/off, costs)
- target scale-out speed vs traffic ramp
- health check and draining policies
- logging/metrics at scale

---

## Pattern B: Layered Load Balancing (Two-Tier)

**When one LB tier becomes too heavy**, split responsibilities:

**Tier 1 (Edge):**
- DDoS protection / WAF
- TLS termination
- simple routing to services/regions

**Tier 2 (Service LB):**
- per-service balancing
- finer routing (path/host)
- service-specific health checks

Benefits:
- isolates blast radius (one noisy service stops hurting others)
- avoids rule explosion on a single LB
- lets teams own their tier 2 configs

Tradeoff:
- extra hop (latency), extra cost, more moving parts (humans will find a way to misconfigure them)

---

## Pattern C: Sharded Load Balancers

Instead of one “global” LB:
- shard by **tenant**, **service**, **domain**, **geo**, or **hash**

Examples:
- `api-a.company.com` → LB-A, `api-b.company.com` → LB-B
- tenant-id hash → LB shard group
- region-specific entrypoints

Benefits:
- scales linearly by adding shards
- limits outage impact

Tradeoff:
- more operational complexity (more configs, certs, monitoring, automation required)

---

# 3) Global Scaling

---

## Option A: DNS Load Balancing (Geo / Weighted / Failover)

Use DNS to spread traffic:
- **geo DNS**: users go to nearest region
- **weighted**: percentage rollout, capacity balancing
- **failover**: unhealthy region removed

Pros:
- simple, cheap, great for multi-region entry
- no single global LB bottleneck

Cons:
- DNS caching means **failover isn’t instant**
- uneven distribution due to resolvers

Best for:
- multi-region active-active or active-passive, where “minutes-ish” failover is acceptable.

---

## Option B: Anycast + Global Edge

A global anycast address advertised from multiple PoPs:
- clients hit “nearest” edge by routing
- edge forwards to regional LBs or directly to services

Pros:
- very fast failover
- absorbs massive traffic at edge

Cons:
- harder to reason about
- depends on provider network

Best for:
- huge scale, latency-sensitive apps, DDoS-prone internet-facing services.

---

# 4) Backend Scaling Interactions (Where People Quietly Mess Up)

---

## Connection Draining Matters

When scaling down or deploying:
- drain existing connections
- stop sending new requests
- allow in-flight to finish

If you don’t:
- you create retry storms
- retries amplify load
- the “small deploy” turns into an outage

---

## Health Checks: Don’t Do Vibes-Based Checking

Good health checks:
- fast
- reflect real readiness (dependencies, warm caches, migrations)
- avoid expensive work

Bad health checks:
- hit databases
- do heavy logic
- fail open/closed incorrectly

Also: health check intervals must scale sensibly with target count.

---

## Sticky Sessions Reduce Effective Scaling

Sticky sessions:
- increase cache hit rates sometimes
- but they **pin load**, causing hot instances
- they block easy autoscaling and failover

Prefer:
- stateless services
- shared session store (Redis, etc.)
- token-based session (JWT with care)

---

## Don’t Ignore Load Balancing Algorithms

Common strategies:
- **round robin**: fine for uniform backends
- **least connections**: better for uneven request duration
- **weighted**: for mixed instance sizes
- **consistent hashing**: for cache affinity / stateful-ish needs

Pick based on your traffic shape, not because it sounds cool.

---

# 5) Scaling in Kubernetes (Because You’re Probably Doing That)

---

## Typical Stack

- **Ingress / Gateway** (L7 routing)
- **Service load balancing** (ClusterIP, NodePort, etc.)
- **External LB** (cloud LB in front of ingress)

Scaling knobs:
- HPA/VPA for pods
- scale ingress controller replicas
- tune conn limits, keep-alives, timeouts
- consider separate ingress per domain/service for shard-like behavior

Gotcha:
> If the ingress controller is the bottleneck, adding backend pods does nothing except increase your disappointment.

---

# 6) Observability for Load Balancer Scaling

---

## Metrics That Actually Matter

At the LB:
- RPS, throughput
- p95/p99 latency (LB + upstream)
- 4xx/5xx split (client vs server)
- active connections, connection rate
- TLS handshake rate and failures
- target health status flaps
- queueing / surge queue depth (if your LB has it)

At targets:
- saturation (CPU/mem), request latency
- errors/timeouts
- queue length / thread pool exhaustion

Key principle:
> You scale what you can see. Otherwise you scale randomly and call it “engineering.”

---

# 7) Common Mistakes

- assuming managed LB = “infinite”
- scaling backends without scaling the ingress/LB tier
- aggressive timeouts that cause retries and amplify load
- sticky sessions everywhere “because login”
- health checks that are expensive or inaccurate
- no connection draining during deploys
- one LB with 900 routing rules (congrats, you built a config bomb)
- treating “multi-AZ” as “multi-region” (not the same)

---

# 8) Practical Design Patterns

---

## Pattern: High-Scale Public API

- Global edge (WAF/CDN) → regional L7 LB
- Regional L7 LB → service mesh/gateway (optional) → services
- Autoscale: ingress + services
- Use rate limiting at edge
- Canary via weighted routing
- Strong draining + readiness checks

---

## Pattern: Websockets / Long-Lived Connections

- Prefer L4 LB where possible
- Tune idle timeouts
- Track concurrent connections, not just RPS
- Scale by sharding or anycast edge depending on size
- Plan for graceful reconnect storms

---

## Pattern: Microservices With Many Teams

- Edge tier for shared concerns (WAF/TLS/basic routing)
- Per-domain or per-team L7 ingress to avoid rule sprawl
- Standardized templates + automation (GitOps) for LB config

---

## Mental Model (Remember This)

- Load balancer scaling is mostly about **removing choke points**
- You scale with:
  - **bigger LB fleet** (managed clustering)
  - **more entrypoints** (shards)
  - **more regions** (DNS/anycast)
  - **less state** (no sticky)
- The real enemy is **connection count + retries**, not just raw RPS

---

## Interview-Ready Summary

> Load balancer scaling is achieved by removing edge bottlenecks via horizontal scaling (managed LB clustering), layering (edge tier plus per-service LBs), sharding traffic across multiple LBs, and expanding globally using DNS-based routing or anycast edge networks. Effective scaling requires correct health checks, connection draining, algorithm choice, and observability focused on RPS, latency percentiles, errors, and concurrent connections to prevent retry amplification and uneven load.

---

## Final Takeaway

Scaling load balancing is not “add more servers.” It’s making sure the **thing that decides where traffic goes** doesn’t become the reason traffic stops going anywhere.
