# Autoscaling Strategies

---

## What This Means

**Autoscaling** is the practice of automatically increasing or decreasing system capacity based on demand, instead of humans panic-scaling during incidents.

Capacity can mean:
- compute instances / pods
- tasks or workers
- throughput units
- connections handled

Core idea:
> Scale before users complain, not after dashboards turn red.

---

## Why This Exists

Without autoscaling:
- traffic spikes cause outages
- quiet periods waste money
- manual scaling is slow and error-prone
- teams overprovision “just in case” (and still get paged)

Autoscaling exists to:
- keep latency and error rates stable
- control infrastructure cost
- react faster than humans
- support unpredictable workloads

---

# 1) Types of Autoscaling

---

## A) Horizontal Scaling (Scale Out / In)
Add or remove instances.

Examples:
- add more pods
- add more EC2 instances
- add more workers

Best for:
- stateless services
- most modern cloud-native apps

Rule:
> Horizontal scaling is the default. Vertical scaling is the exception.

---

## B) Vertical Scaling (Scale Up / Down)
Change instance size (CPU/memory).

Examples:
- move from small VM → larger VM
- increase memory limits

Pros:
- simple conceptually
Cons:
- limited ceiling
- often requires restart
- bigger blast radius

Best for:
- databases (carefully)
- legacy systems
- short-term fixes

---

## C) Hybrid Scaling
Use both:
- horizontal for demand changes
- vertical for baseline capacity

Common in real systems because perfection is rare.

---

# 2) Scaling Signals (What Triggers Scaling)

---

## Good Scaling Metrics (User-Focused)

- **request rate per instance**
- **p95 / p99 latency**
- **error rate**
- **queue depth / backlog**
- **concurrency**

These reflect user experience.

---

## Acceptable but Imperfect Signals

- **CPU usage**
- **memory usage**

CPU is easy, but:
- CPU can be low while latency is high
- IO-bound services often lie via CPU

Rule:
> Scale on what users feel, not what machines feel.

---

## Bad Signals

- average CPU only
- node-level metrics for app scaling
- reactive alerts instead of predictive signals

---

# 3) Reactive vs Predictive Autoscaling

---

## Reactive Autoscaling
Scale after demand increases.

Examples:
- CPU > 70% for 5 minutes → scale out

Pros:
- simple
Cons:
- reacts late
- users may feel the spike

---

## Predictive / Scheduled Autoscaling
Scale before demand hits.

Examples:
- scale up every weekday at 9 AM
- scale before known events/releases

Pros:
- smoother performance
- fewer cold starts
Cons:
- needs traffic knowledge
- risk of overprovisioning

Best practice:
> Combine predictive scaling with reactive safety nets.

---

# 4) Autoscaling Patterns by Workload Type

---

## Stateless APIs
- scale horizontally
- use request rate or latency-based scaling
- fast scale-out, slower scale-in
- aggressive scale-in causes flapping

---

## Background Workers / Queues
- scale on **queue depth**
- scale on **processing lag**
- workers should be idempotent

Rule:
> Queue depth is one of the cleanest autoscaling signals in existence.

---

## Batch Jobs
- scale per job or job group
- often scale to zero when idle
- careful with startup latency

---

## Stateful Services
- limited autoscaling
- often manual or scheduled
- replicas help reads, not writes

Autoscaling does not magically fix databases.

---

# 5) Cooldowns, Stabilization, and Flapping

---

## Why Cooldowns Matter

Without cooldowns:
- scale up
- scale down
- scale up again
- chaos ensues

Use:
- scale-out quickly
- scale-in slowly

Rule:
> Scale-in is where outages are born.

---

## Stabilization Windows
- wait before acting on metric drops
- prevents reacting to short-lived dips

---

# 6) Capacity Buffers and Headroom

---

## Headroom Strategy
Always keep some spare capacity:
- absorb sudden spikes
- handle instance failures
- allow rolling deployments

Common approaches:
- minimum instance count
- target utilization (e.g., 60–70%)

Autoscaling without headroom is just optimism.

---

# 7) Autoscaling in Kubernetes (Conceptual)

---

## Common Tools
- **HPA**: scales pods based on metrics
- **Cluster Autoscaler**: scales nodes
- **KEDA**: event-driven scaling (queues, streams)

Key requirements:
- correct resource requests/limits
- readiness probes
- fast startup time

If your pods take 3 minutes to start, autoscaling will feel useless.

---

# 8) Failure Modes and Safeguards

---

## Scale Storms
When scaling causes more load:
- new instances trigger cache misses
- DB gets hammered
- latency spikes

Mitigations:
- warm caches
- rate limit
- gradual scale-out
- connection limits

---

## Cost Explosions
Autoscaling can happily scale your bill.

Safeguards:
- max instance/pod limits
- budget alerts
- rate limits before scaling
- graceful degradation

---

## Dependency Bottlenecks
Scaling frontend without scaling:
- DB
- cache
- third-party APIs

Result:
- more instances, same bottleneck

Rule:
> Scale the bottleneck, not just the easiest thing.

---

# 9) Observability for Autoscaling

---

## Metrics to Watch
- scaling events over time
- instance/pod count
- latency before/after scaling
- error rates during scale events
- queue depth trends
- cost vs load correlation

You want to know:
- did scaling help?
- or did it just make graphs busier?

---

# 10) Common Mistakes

---

- scaling on CPU alone
- aggressive scale-in
- no max limits
- ignoring startup time
- autoscaling stateful systems blindly
- assuming autoscaling replaces capacity planning
- not testing scaling behavior

Worst mistake:
> “We have autoscaling, so we’re safe.”

No. You have automation. Safety requires thought.

---

## Interview-Ready Summary

> Autoscaling automatically adjusts system capacity based on demand to maintain performance and control cost. Strategies include horizontal scaling for stateless workloads, limited vertical scaling for stateful or legacy systems, and hybrid approaches. Effective autoscaling relies on user-centric signals such as latency, request rate, and queue depth rather than CPU alone, uses fast scale-out and conservative scale-in with cooldowns, maintains headroom, and includes safeguards against cost explosions and dependency bottlenecks. Autoscaling complements but does not replace capacity planning.

---

## Final Takeaway

Autoscaling is not about infinite scale.
It’s about **controlled reaction**:
- react fast to growth
- react slowly to decline
- protect dependencies
- cap the damage when traffic (or bugs) explode

Done right, autoscaling keeps systems boring.
Boring is success.
