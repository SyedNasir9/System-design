# Use of Spot Instances

---

## What This Means

**Spot instances** are unused cloud compute capacity offered at a **huge discount** compared to on-demand pricing, with one catch:
- they can be taken away by the cloud provider with short notice.

Core idea:
> Spot instances are cheap because they come with commitment issues.

If your system can tolerate interruptions, spot is one of the biggest cost wins in cloud.

---

## Why This Exists

Cloud providers have spare capacity.
Instead of letting it sit idle, they sell it cheaply with the rule:
- “We can reclaim it when we need it.”

Spot instances exist to:
- drastically reduce compute cost
- allow flexible workloads to scale cheaply
- improve utilization of cloud infrastructure

---

# 1) How Spot Instances Actually Work

---

## The Interruption Model

- spot instances can be interrupted (terminated or stopped)
- you usually get **~2 minutes notice**
- interruption can happen anytime
- no SLA on availability

You must design for:
- sudden loss of capacity
- retries and rescheduling
- graceful shutdown (if possible)

Rule:
> If losing a node breaks your system, don’t use spot for that part.

---

## Pricing Model (Conceptual)

- price is lower than on-demand (often 60–90% cheaper)
- price can vary based on:
  - instance type
  - region/AZ
  - overall demand

Modern spot usage is about **capacity pools**, not manual bidding.

---

# 2) Good Workloads for Spot

---

## Ideal Candidates

- stateless services
- batch jobs
- background workers
- CI/CD runners
- data processing (ETL)
- big parallel workloads
- autoscaled microservices with redundancy

These workloads can:
- retry
- reschedule
- lose instances without user-visible impact

---

## Bad Candidates

- single-instance systems
- stateful databases (unless very carefully designed)
- workloads with long startup times and no checkpointing
- systems requiring guaranteed capacity
- tightly coupled legacy apps

Rule:
> Spot loves elasticity. Rigid systems hate it.

---

# 3) Spot in Autoscaling Environments

---

## Mixed Instance Strategy

Best practice:
- combine **spot + on-demand**
- maintain a stable baseline on on-demand
- use spot for burst capacity

Example:
- 30–50% on-demand (baseline)
- 50–70% spot (elastic layer)

This balances:
- cost savings
- availability

---

## Instance Diversity (Critical)

Use:
- multiple instance types
- multiple AZs
- multiple capacity pools

Why:
> One instance type disappearing shouldn’t wipe out your fleet.

---

# 4) Handling Interruptions Gracefully

---

## Interruption-Aware Design

Do these:
- listen for interruption notices
- stop accepting new work
- checkpoint progress if possible
- release locks
- shut down cleanly

For containers:
- use preStop hooks
- keep shutdown time short
- make workloads idempotent

---

## Job Design for Spot

Best patterns:
- short-lived jobs
- chunked work units
- retryable tasks
- externalized state (DB/object storage)

Worst pattern:
- “one job runs for 6 hours with no checkpoints”

---

# 5) Spot in Kubernetes (Conceptual)

---

## Common Patterns

- separate node groups for spot and on-demand
- taints/tolerations to control placement
- pod disruption budgets (carefully)
- topology spread constraints
- priority classes (important pods prefer on-demand)

Important:
> PDBs don’t stop spot termination. They only control voluntary disruptions.

---

## Scheduling Strategy

- critical workloads → on-demand nodes
- scalable workloads → spot nodes
- mixed workloads → weighted scheduling

---

# 6) Cost vs Reliability Trade-Off

---

## Cost Benefits
- massive compute savings
- enables higher scale for same budget
- makes burst capacity affordable

## Reliability Costs
- interruptions
- capacity unpredictability
- more complex autoscaling behavior

The question is not:
- “Is spot reliable?”
But:
- “Is my system designed to be interrupted?”

---

# 7) Observability and Controls

---

## What to Monitor

- spot interruption rate
- instance churn
- job retry rate
- pod eviction reasons
- capacity shortfall events
- cost savings vs baseline

If spot interruptions cause visible user impact, you misused them.

---

# 8) Common Mistakes

---

- running critical single-instance services on spot
- using only one instance type
- no interruption handling
- assuming PDBs protect against spot loss
- no on-demand fallback capacity
- chasing 100% spot to “save more” and losing availability

Worst mistake:
> Treating spot like discounted on-demand.

It’s not. It’s discounted *uncertainty*.

---

## Interview-Ready Summary

> Spot instances provide significantly cheaper compute by using spare cloud capacity, but can be interrupted with short notice. They are best suited for fault-tolerant, stateless, and batch workloads that can retry or reschedule work. Effective use of spot requires mixed fleets with on-demand baseline capacity, instance type and AZ diversity, interruption-aware application design, and autoscaling strategies that absorb sudden capacity loss without user impact.

---

## Final Takeaway

Spot instances are one of the easiest ways to cut cloud bills dramatically.

But they demand maturity:
- stateless design
- retries everywhere
- autoscaling done right
- acceptance that instances are temporary

Treat spot as **cheap, disposable compute**.
The moment you get emotionally attached to a spot node, it will disappear.
