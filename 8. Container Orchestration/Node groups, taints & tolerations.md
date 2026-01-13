# Node Groups, Taints & Tolerations (Kubernetes Scheduling)

---

## What This Topic Covers

When you operate Kubernetes at scale, you can’t treat all nodes the same.

You need ways to:
- group nodes by purpose/capacity
- keep certain workloads off certain nodes
- reserve nodes for critical or special workloads

This is where:
- **Node Groups**
- **Taints**
- **Tolerations**

come in.

---

## Node Groups (What They Are)

A **node group** is a set of nodes that share common characteristics:
- instance type / CPU / memory
- GPU availability
- spot vs on-demand
- networking / security constraints
- workload purpose (system vs apps)

In managed Kubernetes, node groups usually map to:
- ASGs / node pools
- separate scaling policies
- different pricing models

Node groups let you build clusters like this:
- small general pool for normal apps
- large compute pool for heavy services
- GPU pool for ML
- system pool for cluster agents

---

## Why Node Groups Matter (DevOps Perspective)

Node groups help with:
- cost control (spot for non-critical workloads)
- performance isolation (no noisy neighbors)
- security separation (restricted nodes for sensitive workloads)
- predictable scaling (different autoscaling rules)

Without node groups:
- critical services land on cheap nodes
- batch jobs steal resources from APIs
- scaling becomes unpredictable
- one workload type ruins everything

---

## Taints (The “Keep Out” Sign)

A **taint** is applied to a node and tells the scheduler:
> “Do not schedule Pods here unless they explicitly tolerate this.”

Taints protect nodes from random workloads.

Structure:
- key=value:effect

Effects matter:

### NoSchedule
- Pods that don’t tolerate it will not schedule here.

### PreferNoSchedule
- Scheduler tries to avoid it, but may schedule if needed.

### NoExecute
- Existing Pods without toleration get evicted.

Taints are node-side policy.

---

## Tolerations (The “I’m Allowed Here” Pass)

A **toleration** is set on a Pod and means:
> “This Pod is allowed to schedule onto nodes with matching taints.”

Tolerations do not force placement.
They only allow it.

Think of it like:
- Taint = lock on the door
- Toleration = key

---

## Taints & Tolerations: What They Solve

They solve these common operational problems:

- Reserve nodes for system components
- Keep GPU nodes free for GPU workloads
- Prevent batch jobs from landing on latency-sensitive nodes
- Isolate risky workloads onto dedicated pools
- Support multi-tenant clusters safely (with other controls)

Without them, scheduling is basically “hope-driven engineering”.

---

## Taints/Tolerations vs Node Selectors/Affinity

These are different tools:

| Tool | Purpose |
|------|---------|
| Taints/Tolerations | Keep Pods off nodes unless allowed |
| NodeSelector | Simple “must run on nodes with label” |
| NodeAffinity | Advanced placement rules (preferred/required) |

Important:
- Tolerations allow, they don’t choose
- Affinity/Selectors choose, they don’t protect

In real clusters, you often combine them:
- taint nodes to protect them
- add toleration + affinity to place the right workloads there

---

## Common Node Group Patterns

### 1) System Node Group
Purpose:
- core components (CNI, logging agents, monitoring, ingress controller)

Protected via:
- taints (so app workloads don’t land there)

### 2) General Application Pool
Purpose:
- normal stateless workloads

Often:
- no taints
- default scheduling

### 3) Batch / Spot Pool
Purpose:
- background jobs, non-critical workloads

Protected via:
- taints + tolerations so only batch workloads land there

### 4) High-Memory / High-CPU Pool
Purpose:
- heavy services (search, analytics)

### 5) GPU Pool
Purpose:
- ML inference/training

Protected strongly:
- taints, plus device plugin constraints

---

## Failure Modes and Operational Risks

### “We tainted nodes but nothing schedules”
Cause:
- Pods do not have tolerations

Fix:
- Add tolerations (and often affinity) to the right workloads

---

### “Pods tolerate, but still don’t land there”
Cause:
- Toleration allows placement, but scheduler still chooses other nodes

Fix:
- Add node affinity/selectors to steer placement

---

### “NoExecute evicted half our cluster”
Cause:
- Applied NoExecute taints without proper tolerations/timeouts

Fix:
- Use carefully; add tolerationSeconds when appropriate

---

### “Spot pool evicted critical services”
Cause:
- Critical workloads tolerated spot taints unintentionally

Fix:
- Tight toleration scopes, separate namespaces/policies

---

## Scheduling Design Guidelines

- Use node groups to separate **cost/perf profiles**
- Use taints to **protect** special node groups
- Use tolerations only for workloads that truly need those nodes
- Pair tolerations with node affinity when placement must be intentional
- Avoid broad tolerations like “tolerate everything”
- Keep system workloads isolated from user workloads

If everything tolerates everything, you’ve built nothing. Just labels.

---

## Observability and Day-2 Ops

What to monitor:
- unschedulable Pods (Pending)
- node pool utilization per group
- eviction events (especially NoExecute)
- autoscaler behavior by node group

Common operational win:
- predictability: you know which workloads run where
- faster incident isolation: noisy neighbor issues reduce dramatically

---

## Mental Model (Remember This)

- Node groups define **what nodes exist**
- Taints define **who is blocked**
- Tolerations define **who is allowed**
- Affinity/selectors define **who is guided**

Scheduling is policy + placement.
Not luck.

---

## Interview-Ready Summary

> Node groups allow cluster nodes to be organized by cost, capacity, and purpose; taints prevent Pods from scheduling onto protected nodes unless they have matching tolerations; tolerations permit scheduling but don’t guarantee placement, often used with node affinity for deliberate workload isolation.

If someone says “we don’t need taints, the scheduler is smart,” they’re outsourcing architecture to luck.

---

## Final Takeaway

Node groups, taints, and tolerations are how you turn a cluster from:
- “everything runs everywhere”
into:
- “the right workloads run in the right places”

This is essential for:
- reliability
- cost control
- performance isolation
- safer multi-tenancy

Kubernetes will schedule Pods wherever it can.  
Your job is to make sure “wherever it can” is not “where it shouldn’t”.
