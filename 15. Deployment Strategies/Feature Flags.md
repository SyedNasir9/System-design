# Feature Flags (Feature Toggles)

---

## What This Means

**Feature Flags** allow you to control application behavior at runtime without redeploying code.

Instead of:
- deploying new feature → instantly live for everyone

You:
> deploy code dark, then decide who sees it.

Feature flags separate:
- **deployment** (shipping code)
- **release** (exposing functionality)

---

## Why This Exists

Without feature flags:
- every deploy is risky
- rollback requires redeploy
- experimentation is difficult
- partial rollouts are complex
- business teams depend entirely on engineering timing

Feature flags exist to:
- reduce release risk
- enable gradual exposure
- run experiments (A/B tests)
- decouple deploy from release
- enable fast rollback

Core idea:
> Code can exist without being active.

---

# 1) Basic Types of Feature Flags

---

## A) Release Flags
Used to gradually enable new features.

Example:
- Enable new checkout UI for 10% of users
- Increase to 50%
- Eventually 100%

---

## B) Experiment Flags (A/B Testing)
Used to test variations.

Example:
- Version A: control group
- Version B: new layout
- Measure conversion impact

---

## C) Operational Flags
Used to control system behavior during incidents.

Example:
- Disable expensive recommendation engine
- Switch to read-only mode
- Turn off background processing

These are often lifesavers during outages.

---

## D) Permission / Role-Based Flags
Enable features only for:
- admins
- beta users
- specific tenants
- internal users

---

# 2) How Feature Flags Work

---

## Runtime Evaluation

Application checks:
if (feature_flag_enabled):
execute new logic
else:
execute old logic


Flags are usually:
- stored in a config service
- cached in memory
- evaluated per request or per session

---

## Control Plane vs Data Plane

Control plane:
- UI/dashboard to toggle flags
- defines rules (percentage, users, regions)

Data plane:
- app reads evaluated flag
- executes logic accordingly

Good flag systems separate these cleanly.

---

# 3) Rollout Strategies Using Feature Flags

---

## Gradual Rollout

Example:
- 1% → 5% → 10% → 25% → 50% → 100%

This mimics canary deployment at application level.

---

## Targeted Rollout

Enable feature for:
- internal users
- specific geography
- specific customer tier

This reduces blast radius.

---

## Instant Kill Switch

If feature causes issue:
- disable flag
- effect is immediate
- no redeploy needed

This is the real power.

---

# 4) Benefits of Feature Flags

---

- safer deployments
- fast rollback
- experimentation support
- controlled exposure
- business flexibility
- decoupled release cycle

In mature teams:
> Every risky change goes behind a flag.

---

# 5) Risks and Downsides

---

## Flag Debt

Over time:
- flags accumulate
- old code paths remain
- complexity increases
- testing becomes harder

Flags must be removed once fully rolled out.

---

## Hidden Behavior

If:
- different users see different logic
- flags interact in complex ways

Debugging becomes confusing.

---

## Performance Overhead

- flag evaluation adds slight latency
- poorly implemented flag checks can hurt performance

Usually small, but at scale matters.

---

# 6) Feature Flags vs Deployment Strategies

---

## Feature Flags vs Canary

Canary:
- infrastructure-level traffic split

Feature flag:
- application-level logic control

Often combined:
- deploy via rolling
- enable feature via flag gradually

---

## Feature Flags vs Blue/Green

Blue/Green:
- environment-level switch

Feature flags:
- logic-level switch

Feature flags give finer control.

---

# 7) Best Practices

---

## Keep Flags Small and Focused
- one feature per flag
- avoid multi-purpose flags

---

## Default to Safe State
- new feature disabled by default
- explicitly enable

---

## Monitor Flag Impact
- error rates
- latency
- business metrics
- resource usage

---

## Remove Flags After Use
- delete dead code
- reduce complexity
- prevent tech debt

---

## Avoid Long-Term Permanent Flags
Flags are for transition, not permanent architecture.

---

# 8) Observability with Feature Flags

---

Track:
- which users have which flags
- flag evaluation errors
- metrics by flag state
- performance differences

Without observability:
> You’re experimenting blindly.

---

# 9) Common Mistakes

---

- leaving flags forever
- nesting flags inside flags
- no monitoring after enabling
- using flags instead of proper configuration
- not testing both code paths
- enabling 100% instantly without staged rollout

Worst mistake:
> Treating feature flags as a substitute for good testing.

They reduce risk.
They don’t remove responsibility.

---

## Interview-Ready Summary

> Feature flags allow runtime control over application behavior, decoupling deployment from release. They enable gradual rollouts, A/B testing, operational kill switches, and targeted feature exposure without redeploying code. While they significantly reduce release risk and improve flexibility, they introduce complexity and technical debt if not managed carefully. Best practices include gradual rollout, strong observability, safe defaults, and removal of flags once fully adopted.

---

## Final Takeaway

Feature flags are controlled power:
- ship early
- enable carefully
- disable instantly if needed
- clean up afterward

They turn releases from high-stakes events into adjustable experiments.

And experiments are far safer than surprises.
