# SLIs, SLOs, Error Budgets

---

## What Are SLIs, SLOs, and Error Budgets?

These three concepts define reliability in measurable terms:

- **SLI (Service Level Indicator)**: a metric that measures service behavior (latency, availability, correctness)
- **SLO (Service Level Objective)**: a target value for an SLI (e.g., 99.9% availability)
- **Error Budget**: the allowed amount of unreliability (the gap between perfect and your SLO)

Core idea:
> You can’t manage reliability with vibes. You need measurable goals.

---

## Why This Exists

Teams often struggle with:
- “Move fast” vs “Be stable”
- endless debates about what “good” means
- reactive firefighting and blame
- alert noise without impact focus

SLIs/SLOs/error budgets exist to:
- define reliability as a contract
- align engineering priorities with user impact
- create a rational release policy
- reduce pointless alerts

This is reliability engineering with accountability.

---

# 1) SLIs (Service Level Indicators)

---

## What Is an SLI?

An **SLI** is a quantifiable measure of some aspect of service performance as experienced by users.

Common SLIs:
- **Availability**: successful requests / total requests
- **Latency**: p95/p99 response time
- **Error rate**: 5xx rate, failed transactions
- **Correctness**: valid results / total results
- **Freshness**: data age / staleness (for pipelines)

SLIs should represent **user experience**, not internal feelings.

---

## Good SLIs vs Bad SLIs

### Good SLIs
- tied to user journeys
- measurable objectively
- stable and meaningful

Examples:
- “% of checkout requests successful”
- “p95 latency for login endpoint”
- “% of messages processed within 2 minutes”

### Bad SLIs
- internal-only and misleading
- easy to game
- not tied to user impact

Examples:
- “CPU under 80%”
- “pod count is high”
- “we feel stable today”

---

## Where SLIs Come From

Typically from:
- metrics (Prometheus/CloudWatch)
- logs (structured events)
- traces (latency breakdown)
- synthetic monitoring (probes)

Best practice:
- measure at the service boundary (ingress/API)
- define success criteria clearly (what counts as “good”?)

---

# 2) SLOs (Service Level Objectives)

---

## What Is an SLO?

An **SLO** is the target reliability level you aim to meet for an SLI over a period.

Examples:
- “99.9% availability over 30 days”
- “p95 latency < 300ms for 95% of requests over 7 days”
- “99% of jobs complete within 5 minutes”

SLOs are internal goals.
SLAs are external promises (and lawyers get involved).

---

## How to Choose SLOs

Choose SLOs based on:
- user expectations
- business criticality
- technical feasibility
- historical baselines
- cost trade-offs

Important reality:
> Higher SLOs cost more, both in engineering and infrastructure.

99.99% is not just “one more 9”.
It’s a different lifestyle.

---

## SLO Windows (Why Time Matters)

SLOs are measured over time windows:
- 7 days
- 28/30 days
- rolling windows

Short windows:
- detect fast regression
- can be noisy

Long windows:
- better stability measure
- slower feedback

Pick windows based on how quickly you need to react.

---

# 3) Error Budgets

---

## What Is an Error Budget?

An **error budget** is the acceptable amount of failure allowed under your SLO.

If your SLO is 99.9% over 30 days, your error budget is:
- 0.1% of requests can fail
- or an equivalent amount of downtime/latency violation

Core idea:
> Error budgets turn reliability into a resource you can spend.

---

## Why Error Budgets Are Powerful

They create a rational balance between:
- shipping features
- maintaining stability

Policy pattern:
- If error budget is healthy → release normally
- If error budget is burning fast → slow down, fix reliability
- If error budget is exhausted → freeze releases, prioritize stability

This stops endless arguments and replaces them with:
> “What does the budget say?”

---

## Burn Rate (How Fast You’re Spending Reliability)

Burn rate measures how fast you're consuming the error budget.

Examples:
- slow burn: steady minor issues
- fast burn: major outage or widespread regression

Burn rate alerting is superior to simple thresholds because it reflects:
- severity
- duration
- user impact

This is why SLO-based alerting is considered best practice.

---

## Error Budget Policies (Operational Design)

Common policies tied to error budgets:
- deployment gating (canary promotion depends on SLOs)
- release freeze if budget is low
- mandatory reliability work after incidents
- postmortems and action items tied to budget impact

Error budgets make reliability a shared responsibility, not just SRE suffering.

---

## Common Mistakes

- choosing SLIs that don’t match user experience
- setting SLOs too high without budget for engineering effort
- measuring SLIs at the wrong place (inside service, not at boundary)
- ignoring partial failures (timeouts, retries)
- using averages instead of percentiles
- treating SLOs as compliance checkboxes

Worst mistake:
> Having SLOs and doing nothing with them.

That’s just documentation.

---

## Practical Examples (Conceptual)

### Availability SLI
- Success = HTTP 2xx/3xx responses
- Total = all requests (excluding known bad clients if justified)

SLO:
- 99.9% success over 30 days

Error budget:
- 0.1% failures allowed

---

### Latency SLI
- Success = requests under 300ms
- Total = all requests

SLO:
- 95% of requests under 300ms (p95)

Error budget:
- 5% of requests may exceed 300ms

---

## Mental Model (Remember This)

- SLI = what you measure
- SLO = the target you aim for
- Error budget = how much failure you can tolerate
- Burn rate = how fast you’re consuming tolerance

This turns reliability into an engineering control system.

---

## Interview-Ready Summary

> SLIs are metrics that quantify user-visible service behavior, SLOs are target objectives for those indicators over a time window, and error budgets represent the allowable unreliability; error budgets enable data-driven trade-offs between feature delivery and reliability work, and burn-rate alerting helps detect when reliability is being consumed too quickly.

If someone says “we want 100% uptime,” they’re describing a fantasy, not an SLO.

---

## Final Takeaway

SLIs, SLOs, and error budgets are how you stop reliability discussions from becoming emotional.

They let teams:
- define what “good” means
- prioritize based on user impact
- ship safely without guessing
- recover faster after incidents

Reliability is not perfection.
It’s a managed budget with clear trade-offs.
