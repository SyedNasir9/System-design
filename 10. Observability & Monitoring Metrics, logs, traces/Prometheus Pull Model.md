# Prometheus Pull Model

---

## What Is the Prometheus Pull Model?

Prometheus uses a **pull-based** metrics collection model.

Meaning:
> Prometheus periodically scrapes metrics endpoints from targets.

Instead of services pushing metrics to a central collector, Prometheus says:
- “Expose `/metrics`”
- “I’ll fetch it on an interval”

This is the core design choice behind Prometheus monitoring.

---

## Why Pull Instead of Push?

Distributed systems are messy:
- instances scale up/down
- IPs change constantly
- networks are unreliable
- services fail silently

Pull-based scraping exists because it makes monitoring:
- simpler to operate
- easier to secure and debug
- more reliable under dynamic infrastructure

Push systems can work, but they require more coordination and trust.
Prometheus assumes neither.

---

## How Pull Scraping Works (High Level)

Typical flow:

1. Service exposes metrics endpoint (usually HTTP)
2. Prometheus discovers targets (static or dynamic)
3. Prometheus scrapes targets every `N` seconds
4. Prometheus stores time-series data locally
5. Queries and alerts run on stored data

Core idea:
> Targets are passive. Prometheus is active.

---

## What a “Target” Looks Like

A target is anything that can expose metrics:
- application
- node exporter
- kube-state-metrics
- databases exporters
- ingress/controllers

Most targets expose:
- `/metrics` endpoint
- Prometheus text exposition format

If a target is down:
- Prometheus marks scrape as failed
- you get missing data (which is itself a signal)

---

## Service Discovery (Why Pull Works in Kubernetes)

Pull only works well if target discovery works well.

In Kubernetes, Prometheus typically discovers targets via:
- labels/selectors
- service endpoints
- annotations
- custom resources (e.g., ServiceMonitor in Prometheus Operator setups)

This lets Prometheus adapt automatically as Pods scale or move.

Without discovery:
- you’re doing static monitoring
- which does not survive Kubernetes

---

## Advantages of the Pull Model

### 1) Works Great With Ephemeral Infrastructure
Pods come and go.
Prometheus discovers and scrapes automatically.

### 2) Simpler Debugging
If metrics look wrong:
- curl the target endpoint
- check scrape status
- inspect relabeling rules

You can reason about the pipeline.

### 3) Centralized Control Over Collection
Prometheus controls:
- scrape interval
- timeouts
- target inclusion/exclusion
- label normalization

No need to coordinate config across every app team.

### 4) Better Failure Visibility
If a service can’t be scraped, it is visible immediately:
- target down
- scrape errors
- timeouts

Push systems can fail silently (drop metrics) unless engineered carefully.

---

## Trade-offs and Limitations

### 1) Targets Must Be Reachable
Prometheus must have network access to targets.
Cross-network scrapes need:
- routing
- firewall policies
- service discovery support

### 2) Short-Lived Jobs Are Harder
If a job runs for 2 seconds, Prometheus may never scrape it.

Solutions:
- pushgateway
- job instrumentation to emit to a collector
- longer-lived exporter pattern

### 3) Large-Scale Scraping Pressure
Prometheus must:
- maintain many scrape connections
- store high-cardinality time series

Scaling requires:
- sharding/federation
- remote write
- careful metric design

Pull is simple, not infinitely free.

---

## Pushgateway (The Special Case)

Prometheus is pull-based, but supports pushing via **Pushgateway** for:
- batch jobs
- short-lived tasks

Important:
> Pushgateway is not a general push system.
It’s a workaround for the “too short to scrape” problem.

Common mistake:
- using Pushgateway for everything

That defeats Prometheus’s design benefits.

---

## Security Implications (DevOps View)

Pull model simplifies security:
- Prometheus is the only component that needs scrape permissions
- targets only need to expose metrics internally

Still required:
- authentication/authorization where appropriate
- TLS for sensitive environments
- network policies to limit who can scrape

Also:
- never expose `/metrics` publicly
Metrics often contain labels that reveal internal structure.

---

## Operational Best Practices

- keep scrape intervals reasonable (avoid over-scraping)
- set timeouts to prevent hung scrapes
- limit cardinality (labels) aggressively
- standardize labels (service, env, version)
- monitor Prometheus itself:
  - scrape success rate
  - TSDB size
  - query latency
  - ingestion rate

Prometheus can become a victim of “observability overload”.

---

## Common Failure Modes

- high-cardinality labels causing memory blow-ups
- bad service discovery rules scraping everything
- missing network access to targets
- scraping too frequently under scale
- assuming missing data means “healthy”

In Prometheus, missing data is often a failure signal, not “zero”.

---

## Mental Model (Remember This)

- Targets expose metrics
- Prometheus pulls them on a schedule
- Discovery keeps targets current
- Missing scrapes are signals
- Cardinality determines pain

Prometheus pull model is “central control + passive endpoints”.

---

## Interview-Ready Summary

> Prometheus uses a pull-based model where it discovers targets and scrapes their metrics endpoints at fixed intervals, providing centralized collection control and strong compatibility with dynamic environments like Kubernetes, while requiring reachable targets and careful handling of short-lived jobs and metric cardinality.

If someone says “push is always better,” they’re ignoring operations. Which is very on-brand for people who don’t operate things.

---

## Final Takeaway

Prometheus’s pull model works because:
- infrastructure is dynamic
- failure is normal
- central control reduces coordination cost

It’s not perfect:
- short-lived jobs need special handling
- scaling requires discipline
- cardinality is a constant threat

But as monitoring designs go, it’s refreshingly honest:
> “Expose your metrics. I’ll collect them. If I can’t, we’ll know.”
