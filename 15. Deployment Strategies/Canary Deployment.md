# Canary Deployment

---

## What This Means

A **Canary Deployment** releases a new version of an application to a **small percentage of users first**, before rolling it out to everyone.

Instead of:
- flipping traffic 100% at once (Blue/Green)
- gradually replacing all instances (Rolling)

You:
> expose the new version to a tiny slice of real traffic and observe.

If it behaves well, increase traffic.
If it misbehaves, stop immediately.

---

## Why This Exists

Canary exists because:
- staging is not production
- real users behave differently
- edge cases only appear under real traffic
- full rollouts amplify small bugs into big outages

Canary helps you:
- reduce blast radius
- validate behavior in production
- detect performance regressions early
- protect user experience

Core idea:
> Trust production traffic. Just don’t trust it all at once.

---

# 1) How Canary Deployment Works

---

## Basic Flow

Assume:
- v1 = stable
- v2 = new version

1. Deploy v2 alongside v1
2. Route 1–5% of traffic to v2
3. Monitor metrics
4. If healthy → increase to 10%, 25%, 50%, 100%
5. If unhealthy → route back to 0%

Users are unaware of the test.
The system learns before committing.

---

# 2) Traffic Control Methods

---

## A) Load Balancer Weighted Routing
- route X% to v2
- adjust weight gradually
- simple and common

---

## B) Service Mesh (Fine-Grained Control)
- traffic split by percentage
- header-based routing
- user segment routing
- advanced observability

Good for:
- Kubernetes environments
- microservices

---

## C) API Gateway / Ingress Rules
- version-based routing
- header or cookie routing
- geo-based canary

---

## D) Feature Flags
Instead of routing by infrastructure:
- deploy code everywhere
- enable feature for small user group
- gradually increase exposure

This is application-level canary.

---

# 3) What to Monitor During Canary

---

## Critical Metrics

- error rate (5xx)
- p95/p99 latency
- CPU/memory usage
- database query load
- cache hit ratio
- user conversion or business metrics

You’re not just checking:
> “Is it alive?”

You’re checking:
> “Is it better or worse?”

---

## Canary Analysis

Advanced setups use:
- automated metric comparison
- statistical analysis
- SLO-based evaluation
- automatic promotion or rollback

Manual “looks fine” is risky at scale.

---

# 4) Canary vs Other Strategies

---

## Canary vs Rolling

Rolling:
- gradually replaces instances
- no traffic isolation
- eventually 100% unless stopped

Canary:
- explicit traffic control
- controlled exposure
- can pause at any stage

Canary is more controlled.
Rolling is simpler.

---

## Canary vs Blue/Green

Blue/Green:
- 100% switch at once
- easy rollback

Canary:
- gradual exposure
- slower rollout
- deeper validation

Blue/Green optimizes rollback speed.
Canary optimizes detection.

---

# 5) When Canary Is Most Useful

---

## High-Traffic Systems
More traffic means:
- faster detection of anomalies
- better statistical confidence

---

## Risky Changes
- performance-sensitive updates
- core business logic changes
- large refactors
- new infrastructure integrations

---

## Machine Learning or Recommendation Systems
- measure behavior impact
- compare conversion rates
- validate performance differences

---

# 6) Risks and Limitations

---

## Hidden Issues at Scale

A bug that:
- appears only at 80% load
- depends on rare user behavior

may not show at 5%.

Canary reduces risk.
It does not eliminate it.

---

## Shared Dependencies

If:
- v2 overloads DB
- shared cache thrashes
- new queries are inefficient

Even 5% traffic can cause system-wide impact.

---

## Long-Lived Connections

Canary based on connection stickiness can:
- skew traffic distribution
- create misleading metrics

---

# 7) Safe Canary Patterns

---

## Gradual Traffic Ramp

Example:
- 1% → 5% → 10% → 25% → 50% → 100%

Pause at each stage.
Observe metrics.
Do not rush.

---

## Canary with SLO Guardrails

Automate:
- if error rate > threshold → rollback
- if latency regression > threshold → rollback
- if CPU spike > threshold → pause

Automation removes human hesitation.

---

## Canary + Feature Flags

Best combo:
- deploy new code
- enable feature only for internal users
- expand gradually

Infrastructure + application-level safety.

---

# 8) Common Mistakes

---

- skipping monitoring
- ramping traffic too quickly
- testing only synthetic traffic
- ignoring business metrics
- not isolating canary logs
- forgetting rollback plan

Worst mistake:
> “It’s only 5%, so it’s safe.”

Five percent of millions is still a lot of users.

---

## Interview-Ready Summary

> Canary deployment releases a new application version to a small percentage of users before full rollout, allowing real production traffic to validate behavior and performance. Traffic is gradually increased while monitoring key metrics such as error rates, latency, and resource usage. If regressions are detected, traffic can be quickly reduced or reverted. Compared to rolling and blue/green deployments, canary provides more controlled exposure and risk reduction but requires strong observability and traffic management capabilities.

---

## Final Takeaway

Canary deployment is about humility:
- assume your code might break
- test it under real traffic
- expand only when metrics say it’s safe

It’s controlled experimentation.
In production.
Which is both powerful and slightly terrifying.
