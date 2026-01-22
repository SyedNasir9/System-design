# Stateless Service Scaling

---

## What This Means

A **stateless service** is one where **any instance can handle any request** because the service does **not store user/session/request state in local memory/disk** between requests.

Core idea:
> If instances don’t “remember” anything, you can add/remove them like Lego blocks. Humans love Lego blocks.

---

## Why This Exists

Stateless scaling makes systems:
- easier to **autoscale**
- safer to **deploy** (rolling updates without breaking sessions)
- more **fault-tolerant** (an instance dying is boring, as it should be)
- better for **multi-AZ / multi-region**

Stateful services scale too, just with extra pain, paperwork, and late-night incidents.

---

# 1) What “State” Actually Means

---

## Types of State You Must Avoid Keeping Locally

Common “oops, we became stateful” patterns:

- **Session state** in memory (user login session stored in RAM)
- **Shopping cart** stored on the instance
- **Websocket connection affinity** without a plan
- **Local file uploads** (writing to instance disk)
- **In-memory caches** treated as source of truth
- **Job progress** stored in local memory

If a request depends on something only one instance knows, you’ve created a scaling trap.

---

## Stateless vs Stateful (Quick Comparison)

Stateless:
- requests are independent
- instances are interchangeable
- horizontal scaling is easy

Stateful:
- requests depend on prior local data
- instances are not interchangeable
- scaling needs coordination (sharding, replication, leader election, etc.)

---

# 2) The Scaling Model

---

## Horizontal Scaling (Scale Out)

Stateless scaling is typically **horizontal**:
- add more instances when load increases
- remove instances when load drops

This works because:
- no user-specific memory is lost when instances come and go
- load balancer can distribute requests freely

---

## Vertical Scaling (Scale Up)

You can also scale up (bigger instance), but:
- it has limits
- it increases blast radius
- it’s usually a temporary patch

Rule:
> Scale up buys time. Scale out buys stability.

---

# 3) How Stateless Services Stay “Stateless”

---

## Externalize All State

Put state in shared systems designed for it:

### Session / Auth State
- use **JWT** (stateless token) or
- store sessions in **Redis** / session store

### User Data
- databases (SQL/NoSQL)

### Files and Uploads
- object storage (S3, GCS, etc.)

### Caching
- Redis/Memcached as shared cache
- CDN for static content

### Background Work State
- queues (SQS, Kafka, RabbitMQ)
- job metadata in DB

The app becomes compute + logic, not a memory palace.

---

## Idempotency (So Retries Don’t Break Everything)

At scale, retries happen. If your endpoints aren’t idempotent:
- duplicate orders
- double payments
- repeated side effects

Common solutions:
- idempotency keys
- request deduplication store
- transactional outbox pattern

Scaling without idempotency is just multiplying your mistakes faster.

---

# 4) Load Balancing for Stateless Scaling

---

## Why Stateless + LB Is the Dream Combo

Because any instance can handle any request, you can use:
- round robin
- least connections
- weighted routing
- zone-aware routing

Avoid:
- sticky sessions unless you have a specific reason

Sticky sessions reduce randomness, and randomness is your friend here.

---

## Connection Draining (Deploys and Scale Down)

When removing instances:
- stop new traffic
- allow in-flight requests to finish
- terminate gracefully

Without draining:
- timeouts
- retries
- thundering herd
- your pager gets attention, which it doesn’t deserve.

---

# 5) Autoscaling: When and How

---

## Signals for Scaling

Good autoscaling signals:
- **RPS per instance**
- **p95 latency**
- **queue depth** (for async workloads)
- **CPU** (okay, but not always meaningful)
- **concurrency** (especially for IO-heavy APIs)

Bad signals:
- “average CPU is fine” while p99 latency is screaming

Best practice:
> Scale on what users feel (latency/errors), not what machines feel (CPU).

---

## Scale Up vs Scale Out Triggers

- sudden spike: scale out quickly (add instances)
- steady growth: scale out gradually
- predictable peaks: scheduled scaling (cron-based)
- IO bound: concurrency-based scaling often beats CPU-based scaling

---

# 6) Common Pitfalls (Where Stateless Goes to Die)

---

## Hidden State in Memory

Examples:
- in-memory session store
- per-instance rate limiting counters
- local feature flag cache without refresh strategy

Fix:
- store in shared systems (Redis) or use consistent strategies.

---

## “Stateless” but Not Really: Local Disk

Containers and ephemeral VMs lose local disk on:
- reschedule
- restart
- autoscale down

Fix:
- write to shared storage or object store.

---

## Caching Mistakes

If your cache is treated as truth:
- cache eviction becomes data loss

Fix:
- cache is acceleration, DB is truth.

---

## Thundering Herd on Cache Miss

At scale, many instances can miss the same key at once:
- all stampede DB
- DB falls over
- you blame “traffic”, but it was your cache

Fix:
- request coalescing / singleflight
- jittered TTL
- stale-while-revalidate patterns

---

# 7) Kubernetes Perspective (Because Reality)

---

## Stateless = Perfect for K8s

- Deployments + ReplicaSets
- HPA for autoscaling
- rolling updates
- pod disruption budgets

Key requirements:
- readiness probes (only send traffic when ready)
- liveness probes (restart if stuck)
- resource requests/limits (stop noisy neighbor chaos)

If your pods depend on local state, Kubernetes will remind you why that’s a bad idea.

---

# 8) Observability for Stateless Scaling

---

## Metrics You Need

- request rate (RPS)
- error rate (4xx/5xx split)
- p95/p99 latency
- saturation (CPU/memory)
- per-instance concurrency
- queue depth (if async)
- downstream dependency latency (DB/Redis/third-party)

You want to answer:
- “Are we slow because we need more instances?”
- “Or are we slow because dependencies are dying?”

---

## SLO Tie-In

Stateless scaling is one of the best tools to protect:
- availability SLOs (instances can fail safely)
- latency SLOs (scale out reduces queueing)

But scaling can’t fix broken code or a collapsing database. It only buys breathing room.

---

## Interview-Ready Summary

> Stateless service scaling works because instances are interchangeable: they don’t store session or user state locally, so traffic can be distributed across any replica and replicas can be added/removed safely. Scaling is typically horizontal behind a load balancer and driven by user-visible signals like latency and error rate. To remain stateless, services externalize state to shared systems (DB, Redis, object storage, queues), implement idempotency for retries, and use graceful draining during deploys and scale-down.

---

## Final Takeaway

Stateless scaling is the closest thing infrastructure has to a cheat code:
- keep state out of instances
- make instances replaceable
- autoscale safely
- deploy without panic

The hard part is not scaling.
The hard part is resisting the urge to stash “just a little state” in memory because it’s “faster”.
