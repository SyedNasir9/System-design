# Rolling Updates, Blue-Green, Canary (Deployment Strategies)

---

## What Are Deployment Strategies?

A **deployment strategy** defines **how new versions of a service are released** while managing:
- availability
- risk
- rollback speed
- user impact

In other words:
> How you change production without gambling the whole system.

---

## Why Deployment Strategy Matters

Deployments are a reliability event.

Bad deployment strategy leads to:
- downtime
- partial outages
- corrupted state
- panic rollbacks
- “it worked in staging” excuses

Good deployment strategy gives you:
- controlled risk
- measurable outcomes
- fast recovery
- safer iteration

---

## 1) Rolling Updates

---

### What Is a Rolling Update?

A **rolling update** gradually replaces old instances with new ones.

Typical flow:
- take down a small number of old Pods
- bring up a small number of new Pods
- repeat until complete

In Kubernetes, Deployments support rolling updates by default.

---

### Why Rolling Updates Are Popular

Because they’re:
- simple
- cheap (no full duplicate environment)
- built into Kubernetes

This makes rolling updates the default for stateless services.

---

### Key Requirements

Rolling updates depend heavily on:
- readiness probes (don’t send traffic too early)
- proper resource requests (avoid scheduling failures)
- graceful shutdown (avoid dropped requests)
- backward-compatible changes (for a period of mixed versions)

Mixed versions are unavoidable during rollout.

---

### Risks / Failure Modes

- slow rollouts hide bugs longer
- mixed-version incompatibility breaks requests
- DB migrations can break old versions mid-rollout
- rollback isn’t always clean if state changed

Rolling updates are safe only when your system supports version overlap.

---

## 2) Blue-Green Deployments

---

### What Is Blue-Green?

Blue-Green means running two production environments:
- **Blue** = current stable version
- **Green** = new version

Traffic is switched from Blue → Green in a controlled cutover.

---

### Why Blue-Green Exists

It optimizes for:
- fast rollback
- clean cutover
- reduced mixed-version complexity

If Green fails:
- switch traffic back to Blue

Rollback is instant, not “wait for Pods to recreate”.

---

### Where Blue-Green Fits

Best for:
- systems requiring fast rollback
- services with strict compatibility needs
- major upgrades

Trade-offs:
- doubles infrastructure temporarily
- requires solid traffic switching (LB, service routing)
- data migrations are still hard

Blue-Green solves deployment risk, not data correctness.

---

### Failure Modes

- traffic switch exposes hidden dependency issues
- session/state not shared (sticky sessions problems)
- databases not compatible across versions
- “Green works in isolation” but not under real load

Blue-Green is powerful when the system is truly environment-independent.

---

## 3) Canary Releases

---

### What Is a Canary Release?

A **canary** release sends a **small percentage of traffic** to the new version first.

If healthy:
- gradually increase traffic
If unhealthy:
- rollback quickly

This is risk management through controlled exposure.

---

### Why Canary Is Valuable

It reduces blast radius.

Instead of:
> “All users experience the bug”

You get:
> “Only 5% suffer briefly, then we stop”

Canary is the best match for:
- high-traffic services
- frequent deployments
- measurable SLO-driven release gating

---

### What Canary Requires

Canary demands serious maturity:
- traffic splitting (service mesh, ingress, LB)
- strong observability (metrics, logs, traces)
- clear success criteria (latency, error rate, saturation)
- automation for promotion/rollback

Canary without observability is just rolling updates with extra steps.

---

### Failure Modes

- low traffic canary gives false confidence
- metrics too noisy to judge safely
- canary users are not representative
- hidden issues appear only at scale

Canaries reduce risk, they don’t remove it.

---

## Rolling vs Blue-Green vs Canary (Comparison)

| Strategy | Risk | Cost | Rollback Speed | Complexity | Best For |
|---------|------|------|----------------|------------|----------|
| Rolling | Medium | Low | Medium | Low | Stateless services |
| Blue-Green | Low-Med | High (temp) | Fast | Medium | Fast rollback needs |
| Canary | Low | Medium | Fast | High | High maturity orgs |

No strategy is “best”.  
Only “best for your constraints”.

---

## Operational Considerations (DevOps View)

### Database Changes
The real deployment problem is data.

Safe patterns:
- backward-compatible schema changes
- expand/contract migrations
- feature flags
- dual writes (carefully)

Danger pattern:
> Deploy app + breaking migration together

That’s how you invent outages.

---

### Readiness & Health Checks
These are not optional.
They decide whether traffic hits a healthy instance.

Bad probes = bad deployments, regardless of strategy.

---

### Rollback Strategy
Always define:
- what rollback means
- how fast it happens
- what state remains changed

Some rollbacks are impossible if state is already mutated.
That’s why “rollback” needs design, not hope.

---

## How Kubernetes Typically Implements These

- Rolling: Deployment update strategy
- Blue-Green: two Deployments + traffic switch (Service selector swap) or GitOps approach
- Canary: weighted routing via service mesh or ingress controller support

The mechanism varies. The principles don’t.

---

## Mental Model (Remember This)

- Rolling = gradual replacement
- Blue-Green = two environments + switch
- Canary = controlled traffic % + observe

The goal is always:
> reduce blast radius and increase recovery speed

---

## Interview-Ready Summary

> Rolling updates replace instances gradually, blue-green deployments run two environments and switch traffic for fast rollback, and canary releases shift a small percentage of traffic to a new version first, using observability-driven promotion to reduce risk.

If someone says “we deploy and monitor manually,” they’re describing a ritual, not a strategy.

---

## Final Takeaway

Deployment strategies are not about shipping faster.  
They’re about **breaking less** while shipping often.

- Rolling is the default workhorse
- Blue-Green is the rollback king
- Canary is the risk-control gold standard (if you can observe properly)

Pick based on:
- traffic patterns
- failure tolerance
- operational maturity
- data migration risk

Production doesn’t care about your release notes.
It cares about your strategy.
