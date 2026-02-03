# Shadow Deployment (Traffic Mirroring)

---

## What This Means

A **Shadow Deployment** (also called **traffic mirroring**) sends a **copy of real production traffic** to a new version of your service without affecting actual users.

Users:
- send requests to v1 (stable)
- receive responses from v1

Meanwhile:
- a copy of that request is sent to v2 (shadow)
- v2 processes it
- its response is ignored

Core idea:
> Test the new system with real traffic, but don’t let it touch users.

---

## Why This Exists

Some problems only appear:
- under real traffic volume
- with real user behavior
- at real production scale

Shadow deployments help you:
- validate performance
- test scalability
- detect logic differences
- verify infrastructure changes
- test risky migrations safely

Without risking user impact.

---

# 1) How Shadow Deployment Works

---

## Basic Flow

1. Production traffic hits v1 (stable)
2. A copy of each request is sent to v2
3. v2 processes the request independently
4. v2 response is logged or analyzed
5. Users only see v1 response

This allows:
- real production inputs
- safe validation
- zero user-visible risk

---

## Where Mirroring Happens

- Load balancer
- API gateway
- Service mesh (very common in Kubernetes)
- Reverse proxy (NGINX, Envoy)

Modern service meshes make this easier.

---

# 2) What You Validate in Shadow Mode

---

## Functional Correctness

Compare:
- v1 output
- v2 output

Check for:
- response differences
- missing fields
- incorrect calculations
- edge case behavior

You can log mismatches for analysis.

---

## Performance and Scalability

Measure:
- latency
- CPU/memory
- DB queries
- cache behavior
- throughput handling

Shadow traffic lets you test real load before switching users.

---

## Infrastructure Changes

Examples:
- new database
- new cache cluster
- new query engine
- new runtime version
- new microservice architecture

Shadowing helps validate before committing.

---

# 3) Important Design Considerations

---

## Avoid Side Effects

Shadow traffic must not:
- write to production DB
- trigger payments
- send emails
- modify state

Solutions:
- disable side effects in shadow environment
- use read-only mode
- point to separate test DB
- stub external services

Rule:
> Shadow systems should observe, not change reality.

---

## Data Consistency

If shadow uses:
- separate DB
- separate cache

Then:
- you must replicate relevant data
- or mock dependencies

Otherwise results won’t match production.

---

## Idempotency Matters

Shadow systems may:
- reprocess requests
- handle duplicates
- receive mirrored traffic with slight delay

Design must tolerate duplicates safely.

---

# 4) Shadow vs Canary vs Blue/Green

---

## Shadow vs Canary

Canary:
- small % of real users see new version
- user-visible

Shadow:
- 0% of users see new version
- completely invisible

Shadow validates behavior.
Canary validates user experience.

---

## Shadow vs Blue/Green

Blue/Green:
- full switch when ready

Shadow:
- no switch
- pure observation

Shadow often happens before canary.

---

# 5) Typical Shadow Workflow

---

## Step 1: Deploy v2 (Shadow Mode)
- no user traffic yet
- connected to mirrored traffic only

## Step 2: Mirror Traffic
- 100% or selected routes
- read-only mode

## Step 3: Compare Results
- log response differences
- measure latency differences
- check resource usage

## Step 4: Fix Issues
- tune performance
- correct logic mismatches

## Step 5: Promote to Canary
- begin controlled exposure to users

Shadow → Canary → Full rollout is common in mature systems.

---

# 6) Observability in Shadow Deployments

---

## Metrics to Monitor

- latency difference (v1 vs v2)
- error rate difference
- resource consumption
- DB load impact
- response diff count
- log anomalies

Shadow systems must not overload shared dependencies.

---

# 7) Risks and Limitations

---

## Increased Load

Mirroring doubles:
- request processing
- DB reads (if shared)
- CPU usage

If not careful:
- you accidentally DDoS yourself

---

## False Confidence

Shadow might:
- not replicate side effects
- use different dependencies
- miss user-facing edge cases

So shadow is validation, not proof of perfection.

---

## Complex Setup

Requires:
- traffic routing logic
- logging comparison tools
- observability maturity

Not trivial in simple systems.

---

# 8) When to Use Shadow Deployments

---

## High-Risk Changes
- rewriting core logic
- migrating databases
- replacing frameworks
- major refactors

---

## Performance Testing Under Real Load
- validating scaling
- verifying resource behavior

---

## Machine Learning Systems
- compare prediction outputs
- evaluate model accuracy

---

# 9) Common Mistakes

---

- allowing shadow system to write to production
- not isolating external integrations
- ignoring increased infrastructure cost
- not comparing outputs programmatically
- skipping observability
- assuming shadow = safe production-ready

Worst mistake:
> “It worked in shadow, so it’s perfect.”

It worked without users. That’s different.

---

## Interview-Ready Summary

> Shadow deployment, or traffic mirroring, duplicates real production traffic to a new version of a service without affecting users. The shadow version processes requests independently, allowing validation of functionality, performance, and infrastructure changes under real traffic conditions. It is commonly used before canary or full rollout for high-risk changes. Proper design must prevent side effects, handle idempotency, and monitor performance and response differences. Shadow deployments provide safe validation but increase load and require strong observability.

---

## Final Takeaway

Shadow deployment is like letting the new system practice in front of a mirror:
- it sees real traffic
- it makes real decisions
- but no one depends on it yet

It’s controlled rehearsal.
And rehearsals save you from embarrassing live performances.
