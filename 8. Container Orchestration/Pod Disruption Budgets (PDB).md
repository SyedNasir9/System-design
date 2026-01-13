# Pod Disruption Budgets (PDB)

---

## What Is a Pod Disruption Budget?

A **Pod Disruption Budget (PDB)** defines **how many Pods of a workload are allowed to be unavailable during voluntary disruptions**.

In human terms:
> “You may break *some* of my Pods, but not too many at once.”

PDBs do not prevent failures.  
They control how graceful *planned* disruption is allowed to be.

---

## Why Pod Disruption Budgets Exist

Kubernetes frequently disrupts Pods on purpose:
- node drains
- cluster upgrades
- autoscaler scale-downs
- maintenance operations

Without PDBs:
- all replicas can be evicted at once
- “highly available” apps go down instantly
- upgrades become outages

PDBs exist to **protect availability during controlled operations**.

---

## Voluntary vs Involuntary Disruptions

This distinction is critical.

### Voluntary Disruptions (PDB applies)
- `kubectl drain`
- node upgrades
- cluster autoscaler removing nodes

### Involuntary Disruptions (PDB does NOT apply)
- node crashes
- kernel panics
- OOM kills
- hardware failures

PDBs are guardrails, not airbags.

---

## How a PDB Works

A PDB defines one of:
- `minAvailable`
- `maxUnavailable`

The scheduler and eviction logic must respect this budget **before evicting Pods**.

If evicting a Pod would violate the budget:
> The eviction is blocked or delayed.

Availability is preserved. Progress slows.

---

## `minAvailable` vs `maxUnavailable`

### `minAvailable`
- Minimum number of Pods that must stay running
- Best when you know how many healthy replicas you need

Example mindset:
> “I always need at least 3 Pods alive.”

---

### `maxUnavailable`
- Maximum number of Pods that can be unavailable
- Best when scaling is dynamic

Example mindset:
> “At most 1 Pod can be down at a time.”

---

## PDB and Replicas (Important Relationship)

PDBs make sense only if:
- your workload has **multiple replicas**
- your application is horizontally scalable

Common mistake:
- replicas = 1
- PDB = minAvailable: 1

Result:
> Node drains hang forever.

PDBs cannot invent availability you didn’t design for.

---

## PDBs and Autoscaling

PDBs interact directly with:
- Horizontal Pod Autoscaler
- Cluster Autoscaler

Key effects:
- PDB can block node scale-down
- Cluster autoscaler may skip nodes
- Scaling slows to protect availability

This is expected behavior.
Availability > speed.

---

## Common PDB Use Cases

### Stateless APIs
- Protect rolling updates
- Avoid full outages during node drain

### Critical System Components
- DNS
- Ingress controllers
- Monitoring backends

### Multi-Replica Stateful Services
- Ensure quorum isn’t lost accidentally

PDBs are especially valuable when humans are involved.

---

## What PDBs Do NOT Do

Important limitations:
- Do not protect against crashes
- Do not guarantee performance
- Do not prevent bad deploys
- Do not fix poor replica counts

PDBs assume your application can survive losing a Pod or two.

---

## Dangerous PDB Misconfigurations

### Overly Strict Budgets
- `minAvailable` too high
- `maxUnavailable` too low

Result:
- node drains blocked
- upgrades stuck
- autoscaling frozen

---

### Too Loose Budgets
- allowing too many Pods down
- effectively no protection

Result:
- availability loss during maintenance

PDBs require intentional tuning, not copy-paste.

---

## PDBs vs Rolling Update Strategy

These are complementary, not redundant.

| Rolling Update | PDB |
|---------------|-----|
| Controls deploy behavior | Controls evictions |
| App-level rollout | Cluster-level disruption |
| Applies during updates | Applies during maintenance |

You usually need both.

---

## Observability for PDBs

You should monitor:
- blocked evictions
- stalled node drains
- PDB violations
- unavailable replicas

If upgrades stall mysteriously, PDBs are often the reason.

---

## Design Guidelines

- Use PDBs for critical workloads
- Match PDB values to real availability needs
- Ensure enough replicas exist
- Test node drains in non-prod
- Revisit PDBs when scaling changes

PDBs encode **availability policy**. Treat them seriously.

---

## Mental Model (Remember This)

- PDBs protect against planned disruption
- They slow operations to preserve availability
- They do nothing for sudden failure
- They assume redundancy already exists

PDBs don’t create uptime.  
They preserve it during maintenance.

---

## Interview-Ready Summary

> Pod Disruption Budgets define how many Pods of a workload can be unavailable during voluntary disruptions, ensuring availability is preserved during node drains, upgrades, and autoscaler operations, but they do not protect against unexpected failures.

If someone says “PDBs prevent outages,” they’ve misunderstood what they guard against.

---

## Final Takeaway

PDBs are a contract:
- Kubernetes promises not to evict too many Pods
- You promise to run enough replicas

Break either side, and availability suffers.

Use PDBs to make maintenance boring.  
Boring is the goal.
