# Zero-Downtime Updates

---

## What This Means

**Zero-downtime updates** ensure that users can continue using your system **without interruption** while a new version is deployed.

No:
- service restarts visible to users
- connection drops
- “Service Unavailable” screens

Core idea:
> Deploy while the system is live, and make users unaware anything changed.

---

## Why This Exists

Downtime causes:
- revenue loss
- user frustration
- broken transactions
- cascading system failures
- embarrassing status pages

Modern systems aim for:
- continuous delivery
- high availability
- seamless upgrades

Zero-downtime is not magic.
It’s careful coordination.

---

# 1) Core Requirements

---

## Multiple Instances

You cannot achieve zero downtime with:
- one server
- one replica
- one process

You need:
- redundancy
- load balancing
- horizontal scaling

Rule:
> Single-instance systems cannot do zero downtime. They can only do fast restarts.

---

## Load Balancer

A load balancer:
- routes traffic only to healthy instances
- removes instances during updates
- ensures traffic continuity

Health checks are mandatory.

---

## Health Checks

Two critical probes:

- **Readiness** → can this instance serve traffic?
- **Liveness** → is this instance alive?

During update:
- mark instance not-ready
- wait for traffic to drain
- then terminate safely

---

# 2) Deployment Strategies That Enable Zero Downtime

---

## Rolling Deployment
Gradually replace instances.

- old and new versions coexist
- no global shutdown
- traffic shifts smoothly

---

## Blue/Green Deployment
Switch traffic between full environments.

- instant switch
- immediate rollback
- no mixed versions

---

## Canary Deployment
Gradually expose users to new version.

- small percentage first
- monitor before expanding

All can support zero downtime when done correctly.

---

# 3) Graceful Shutdown (Critical)

---

Before terminating an instance:

1. Stop accepting new requests
2. Finish in-flight requests
3. Close connections cleanly
4. Release resources

Without graceful shutdown:
- users see 5xx errors
- partial transactions fail

Implement:
- termination hooks
- connection draining
- short but sufficient shutdown timeout

---

# 4) Database and Schema Strategy

---

## The Hard Part

App updates often depend on:
- database schema changes
- migrations
- new columns or fields

If schema breaks old version:
- zero downtime becomes impossible.

---

## Safe Migration Pattern (Expand → Migrate → Contract)

1. **Expand** schema (add new columns/tables)
2. Deploy new app version
3. Migrate data
4. Remove old fields later

Never:
- remove required columns before all old instances are gone.

Rule:
> Backward compatibility first. Cleanup later.

---

# 5) Stateful Systems

---

Zero downtime is harder for:

- databases
- cache clusters
- message brokers

Strategies:
- replication
- leader election
- rolling node replacement
- connection pooling

State must survive version transitions.

---

# 6) Avoiding Hidden Downtime

---

## Warm-Up New Instances

Before adding to traffic:
- preload caches
- establish DB connections
- complete startup routines

Cold instances cause latency spikes.

---

## Manage Connection Draining

- allow existing requests to finish
- configure load balancer drain timeout
- avoid abrupt connection resets

---

## Monitor During Deployment

Track:
- error rate
- p95/p99 latency
- active connections
- CPU/memory
- DB load

Deployments are controlled experiments.
Observe them.

---

# 7) Failure Scenarios

---

## Partial Deployment Failure

If new instances fail health checks:
- stop rollout
- keep old instances running
- rollback safely

---

## Dependency Bottleneck

New version may:
- increase DB load
- change query pattern
- increase cache misses

Zero downtime for app does not mean zero impact system-wide.

---

# 8) When Zero Downtime Is Hard

---

- single-node systems
- monoliths without redundancy
- tightly coupled DB changes
- long-running connections
- legacy infrastructure

In those systems:
> Aim for minimal downtime, not perfection.

---

# 9) Common Mistakes

---

- deploying with only one replica
- skipping readiness probes
- not draining connections
- incompatible DB migrations
- no rollback plan
- ignoring startup latency
- no monitoring during rollout

Worst mistake:
> “It’s just a small change.”

Small changes break big systems regularly.

---

## Interview-Ready Summary

> Zero-downtime updates ensure that application deployments do not interrupt user traffic by leveraging redundancy, load balancers, health checks, and controlled deployment strategies such as rolling, blue/green, or canary releases. Key requirements include multiple instances, readiness and liveness probes, graceful shutdown handling, backward-compatible database migrations, and careful observability during rollout. True zero downtime depends on both application and data-layer compatibility, not just deployment automation.

---

## Final Takeaway

Zero-downtime updates are not about speed.
They’re about discipline:

- redundancy
- compatibility
- controlled rollout
- graceful shutdown
- monitoring everything

When done right, users never know you deployed.

Which is exactly the point.
