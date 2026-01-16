# Metrics, Logs, Traces (Observability Signals)

---

## What Are Metrics, Logs, and Traces?

Metrics, logs, and traces are the three primary signals used to understand system behavior.

They answer different questions:

- **Metrics**: “Is something wrong?”
- **Logs**: “What happened?”
- **Traces**: “Where did it happen and why is it slow/failing?”

Core idea:
> Observability is not having data. It’s being able to explain behavior.

Having all three without correlation is like having three maps in different languages.

---

## Why These Signals Matter (DevOps View)

Modern systems fail in distributed, partial, and weird ways:
- latency spikes without clear errors
- one dependency degrading the whole system
- failures hidden by retries
- cascading timeouts
- “only happens sometimes” bugs

Metrics, logs, and traces exist to:
- detect issues quickly
- reduce time-to-diagnose
- enable safe releases and scaling
- support SLOs and incident response

If you can’t observe it, you can’t operate it.

---

# 1) Metrics

---

## What Are Metrics?

**Metrics** are numeric measurements aggregated over time.

Examples:
- request rate (RPS)
- error rate (4xx/5xx)
- latency (p50/p95/p99)
- CPU/memory usage
- queue depth
- DB connections
- cache hit ratio

Metrics are best for:
- dashboards
- alerting
- trend analysis

They tell you symptoms, not the full story.

---

## Strengths of Metrics

- cheap to store and query
- great for alerting and SLOs
- easy to visualize over time
- good for capacity planning

Metrics answer:
> “Is the system behaving normally?”

---

## Weaknesses of Metrics

- lack detail for debugging
- aggregated numbers hide edge cases
- can mislead if labels are wrong
- “healthy metrics” can hide user pain (bad SLI selection)

Metrics often tell you *that* something is wrong, not *why*.

---

## Golden Signals (Most Useful First)

If you don’t know where to start, measure:

- **Latency**
- **Traffic**
- **Errors**
- **Saturation**

These apply across most services.

---

# 2) Logs

---

## What Are Logs?

**Logs** are discrete, timestamped events emitted by systems.

Examples:
- request received
- user login attempt
- error stack trace
- warning conditions
- business events (order placed, payment failed)

Logs are best for:
- debugging specific failures
- audits and forensics
- understanding edge cases

Logs answer:
> “What exactly happened?”

---

## Structured Logging (Non-Negotiable)

Plain text logs don’t scale.

Good logs are:
- structured (JSON)
- consistent fields
- include context:
  - request_id / trace_id
  - user/session identifiers (careful with PII)
  - service name, version, environment
  - error codes and stack traces

Unstructured logs are emotional support logs.

---

## Strengths of Logs

- high detail
- best for root-cause analysis
- captures rare failure paths
- enables auditing

---

## Weaknesses of Logs

- expensive storage at scale
- noisy without discipline
- easy to leak secrets/PII
- searching logs without correlation is slow and painful

Logging everything is not observability. It’s hoarding.

---

# 3) Traces

---

## What Are Traces?

**Distributed tracing** tracks a request across multiple services and components.

A trace is made of **spans**:
- each span represents work done in a service/component
- spans include timing, metadata, and status

Traces are best for:
- debugging latency
- identifying bottlenecks
- understanding request paths in microservices

Traces answer:
> “Where did time go, and where did it fail?”

---

## Trace Context Propagation

Tracing only works if context is propagated:
- trace_id and span_id passed across service boundaries
- headers maintained across gateways, queues, async jobs

If trace context is lost, the trace becomes useless.

---

## Strengths of Traces

- shows end-to-end request flow
- pinpoints latency hotspots
- highlights dependency issues
- connects failures across services

---

## Weaknesses of Traces

- overhead if sampled poorly
- requires instrumentation and propagation discipline
- async flows are harder (queues, events)
- sampling can miss rare failures

Tracing is powerful, but only if you wire it correctly.

---

## Correlation: The Real Superpower

Observability isn’t three separate tools.
It’s correlation between signals.

Good correlation lets you:
1. see an error spike in metrics
2. jump to related traces
3. open the logs for that trace/span
4. identify the failing dependency quickly

Bad correlation forces:
- grep across logs
- guessing which service is responsible
- reproducing production issues locally (pain)

Correlation requires:
- shared IDs (trace_id/request_id)
- consistent labels (service, env, version)
- time sync (NTP)

---

## Sampling (Practical Reality)

At scale, you don’t store everything.

Typical approach:
- keep all metrics (cheap)
- store logs with retention tiers
- sample traces:
  - tail-based sampling for errors/slow requests
  - head-based sampling for general traffic

If you sample wrong, you lose the only traces you needed.

---

## Observability Anti-Patterns

- metrics with no labels (can’t segment by service/route)
- logs without request_id/trace_id
- traces without meaningful span names
- dashboards with 200 graphs nobody checks
- alerts on everything (alert fatigue)
- logging secrets accidentally

Most “observability problems” are data design problems.

---

## How to Use These in Incidents (Runbook Flow)

A sane incident workflow:

1. **Metrics**: detect and scope (what’s broken, how big)
2. **Traces**: locate bottleneck/failure path (where/why)
3. **Logs**: confirm root cause (what happened exactly)
4. Mitigate, then verify with metrics

Metrics get you to the fire.
Traces tell you which room.
Logs show you what started it.

---

## Mental Model (Remember This)

- Metrics = symptoms at scale
- Logs = details for specific events
- Traces = request story across services
- Correlation = speed of diagnosis

Missing one signal = incomplete truth.

---

## Interview-Ready Summary

> Metrics provide aggregated health and performance indicators, logs provide detailed event records for debugging, and traces provide end-to-end visibility of request flows across distributed services; effective observability depends on correlating these signals with shared identifiers and consistent context.

If someone says “we have logs so we’re observable,” they’re lying to themselves.

---

## Final Takeaway

Metrics, logs, and traces are complementary, not interchangeable.

Used correctly, they:
- reduce downtime
- speed incident response
- enable safe scaling and releases

Used poorly, they:
- generate noise
- hide root causes
- waste engineering time

Observability is not a tool stack.
It’s a system design decision.
