# Distributed Tracing (Jaeger, Zipkin)

---

## What Is Distributed Tracing?

**Distributed tracing** tracks a single request as it flows through multiple services and components, producing an end-to-end timeline of what happened.

A trace answers:
- **Where did time go?**
- **Which service caused the failure?**
- **What dependency is slowing us down?**

In microservices:
> If you don’t trace, you guess.

And guessing is expensive.

---

## Core Concepts

### Trace
A trace represents the full journey of a request across services.

### Span
A span is a timed unit of work within a service:
- DB query
- HTTP call
- cache lookup
- internal processing

Spans form a tree:
- parent span
- child spans

### Trace Context
A set of identifiers that must travel with the request:
- `trace_id`
- `span_id`
- parent relationships
- sampling flags

No context propagation = no distributed tracing.

---

## Why Tracing Is Critical in Microservices

Microservices fail in ways monoliths rarely do:
- latency adds up across hops
- retries hide failures
- partial failures degrade silently
- one dependency slows everything

Metrics tell you:
> “Latency is up.”

Tracing tells you:
> “Service B is waiting 800ms on DB, and 30% of calls retry.”

That difference is the difference between fixing and flailing.

---

## How Tracing Works (High Level)

Typical flow:

1. A request enters system (gateway/service)
2. A trace is started (root span created)
3. Trace context is propagated across calls (headers/messages)
4. Each service creates spans for work it performs
5. Spans are exported to a collector
6. Collector stores and indexes traces
7. UI shows the trace timeline

Tracing is not “one tool”.
It’s instrumentation + context propagation + backend storage + UI.

---

## Context Propagation (The Real Hard Part)

Propagation usually happens via HTTP headers.

Common formats:
- W3C `traceparent` / `tracestate`
- legacy formats (Zipkin B3, Jaeger)

Propagation must happen across:
- HTTP calls
- gRPC
- message queues (producer → broker → consumer)
- background jobs

If context breaks at a boundary:
> Your trace becomes a set of unrelated fragments.

---

## Sampling (Because You Can’t Store Everything)

At scale, tracing is expensive.

Common sampling types:

### Head-based sampling
- decide at request start
- cheaper and simpler
- may miss rare errors

### Tail-based sampling
- decide after seeing the whole trace
- can keep only slow/error traces
- more complex and resource-heavy

Good tracing systems keep:
- more errors
- more slow traces
- fewer boring successful fast calls

---

## Tracing + Metrics + Logs (Correlation)

Tracing becomes much more powerful when correlated with:
- metrics (latency, error spikes)
- logs (exact error details)

Best practice:
- logs include `trace_id`
- metrics label by service/version (carefully)
- dashboards link to trace views

Tracing alone shows “where”.
Logs confirm “what”.
Metrics show “how bad”.

---

## Jaeger

### :contentReference[oaicite:0]{index=0}

Jaeger is a widely used distributed tracing system originally built at Uber.

Key characteristics:
- strong UI for trace exploration
- supports multiple storage backends
- integrates well with Kubernetes
- commonly used with OpenTelemetry

Best for:
- production tracing at scale
- teams needing flexible backend storage and good UI

Operational concerns:
- storage and indexing costs grow fast
- sampling strategy matters
- collector sizing matters

---

## Zipkin

### :contentReference[oaicite:1]{index=1}

Zipkin is an older but stable tracing system originally from Twitter.

Key characteristics:
- simpler architecture
- classic adoption, especially in older stacks
- supports common propagation formats

Best for:
- smaller systems
- simpler setups
- teams already using Zipkin instrumentation

Operational concerns:
- feature set can be narrower compared to Jaeger ecosystems
- scalability depends heavily on backend setup

---

## Jaeger vs Zipkin (High Level Comparison)

| Aspect | Jaeger | Zipkin |
|-------|--------|--------|
| Ecosystem | Strong modern adoption | Mature, older adoption |
| UI/Features | Rich exploration | Simpler |
| Scale Focus | Common at larger scale | Works well for smaller/medium |
| Integrations | Strong OpenTelemetry alignment | Broad compatibility |

In 2026 reality:
- both can work
- your instrumentation (OpenTelemetry) often matters more than backend choice

---

## Tracing in Kubernetes (DevOps View)

Key places tracing breaks in K8s:
- sidecars/gateways not propagating headers
- async processing losing context
- ingress rewriting headers unexpectedly
- missing service identity labels

Also important:
- add service version labels
- add environment/cluster labels
- annotate deploy events in dashboards to correlate trace changes

Tracing is not “install Jaeger”.
Tracing is “make propagation reliable”.

---

## Common Distributed Tracing Mistakes

- no propagation across service boundaries
- tracing only some services (gaps ruin the story)
- no sampling strategy (storage explosion)
- too much cardinality in span tags
- not tagging spans with meaningful names
- forgetting async boundaries (queues, jobs)

Partial tracing is better than none, but it still leaves blind spots.

---

## What to Trace (Practical Guidance)

Trace:
- ingress/gateway requests
- service-to-service calls
- DB/cache calls
- external API calls
- queue publish/consume

Don’t trace:
- extremely high-frequency internal loops
- ultra-noisy events with no diagnostic value

Trace what helps you answer:
> “Why is this slow or failing?”

---

## Mental Model (Remember This)

- Metrics tell you something is wrong
- Traces tell you where and why
- Logs tell you what happened
- Context propagation is the entire game
- Sampling is how you survive scale

Distributed tracing is storytelling for requests.

---

## Interview-Ready Summary

> Distributed tracing tracks requests across microservices using propagated trace context and spans, enabling root-cause analysis of latency and failures; systems like Jaeger and Zipkin collect and visualize traces, but effective tracing depends primarily on consistent instrumentation, context propagation, and sampling strategy.

If someone says “we installed Jaeger so tracing is done,” they’ve built a dashboard for missing data.

---

## Final Takeaway

Distributed tracing is essential because microservices hide problems across boundaries.

Jaeger and Zipkin provide the backend and UI, but success depends on:
- propagation discipline
- meaningful spans
- smart sampling
- correlation with logs and metrics

Tracing doesn’t eliminate complexity.
It just makes complexity visible enough to fix.
