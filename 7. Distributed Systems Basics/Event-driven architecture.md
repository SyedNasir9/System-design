# Event-Driven Architecture

---

## What Is Event-Driven Architecture?

**Event-Driven Architecture (EDA)** is a system design approach where services **communicate by producing and reacting to events**, rather than calling each other directly.

An **event** is a fact:
> “Something happened.”

Not:
- “Please do this”
- “Are you alive?”
- “Can you respond right now?”

In EDA, services:
- Emit events
- Subscribe to events
- React independently

No tight coupling. No waiting on responses. No begging another service to be up.

---

## Why Event-Driven Architecture Exists

Synchronous systems break under scale and uncertainty.

Common pain points:
- Cascading failures
- Tight coupling between services
- Slow response chains
- Hard-to-scale workflows

EDA exists to:
- Decouple services
- Improve resilience
- Enable asynchronous processing
- Scale independently

In short:
> Systems stop asking for permission and start reacting to reality.

---

## Core Idea: Producers, Events, Consumers

Every event-driven system has three basic roles:

### Producer
- Detects something happened
- Emits an event
- Does not care who consumes it

### Event
- Immutable fact
- Describes *what happened*
- Contains context, not logic

### Consumer
- Subscribes to events
- Reacts independently
- Can fail without breaking the producer

This separation is the whole point.

---

## Event-Driven vs Request-Driven Systems

| Request-Driven | Event-Driven |
|---------------|-------------|
| Tight coupling | Loose coupling |
| Synchronous | Asynchronous |
| Caller waits | Fire-and-forget |
| Cascading failures | Isolated failures |
| Harder to scale | Easier horizontal scaling |

EDA trades **immediacy** for **resilience and flexibility**.

---

## Messaging Infrastructure (The Backbone)

Events need a transport layer.

Common categories:

### Message Queues
- Point-to-point
- One consumer per message
- Good for background jobs

Examples:
- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}

---

### Event Streams
- Append-only logs
- Multiple consumers
- Replayable events

Example:
- :contentReference[oaicite:2]{index=2}

Choosing the wrong backbone creates pain that no architecture diagram fixes.

---

## Event Flow (Typical)

Service A
└─ emits Event X
↓
Broker / Stream
↓
Service B
Service C
Service D

yaml
Copy code

Key properties:
- Producers don’t know consumers
- Consumers don’t block producers
- New consumers can be added without redeploying producers

This is why EDA scales.

---

## Delivery Semantics (Reality Check)

Events are not magical. Delivery has rules.

### At-Most-Once
- Fast
- Possible data loss

### At-Least-Once
- Most common
- Duplicates possible
- Requires idempotency

### Exactly-Once
- Expensive
- Complex
- Often misunderstood

In practice:
> You design for duplicates, not perfection.

---

## Idempotency (Required, Not Optional)

In EDA:
- Consumers *will* see duplicates
- Retries *will* happen
- Crashes *will* occur

Consumers must be **idempotent**.

Meaning:
> Processing the same event twice does not corrupt state.

If your consumer assumes “this will run only once,” it is already broken.

---

## Eventual Consistency (Accept It)

EDA systems are **eventually consistent**.

That means:
- State updates propagate over time
- Temporary inconsistency is normal
- Strong consistency is traded for availability and scale

If you need:
- Immediate global consistency
- Strong transactional guarantees

EDA may not be the right default.

---

## Schema and Versioning (The Silent Killer)

Events are contracts.

Bad practices:
- Changing event structure casually
- Reusing event types for new meanings
- Breaking old consumers silently

Good practices:
- Versioned schemas
- Backward compatibility
- Explicit event ownership

Breaking events is worse than breaking APIs.  
APIs fail fast. Events fail quietly.

---

## Observability in Event-Driven Systems

EDA is harder to observe.

Challenges:
- No single request path
- Asynchronous flows
- Delayed failures

Requirements:
- Correlation IDs
- Structured logs
- Lag metrics
- Dead-letter queues

If you can’t trace events, you can’t debug outcomes.

---

## Failure Handling Patterns

Common patterns:
- Retries with backoff
- Dead-letter queues (DLQ)
- Poison message handling
- Consumer isolation

Ignoring failure handling turns EDA into:
> Distributed data loss with extra steps.

---

## When Event-Driven Architecture Makes Sense

Use EDA when:
- Workflows are asynchronous
- Services must scale independently
- Failure isolation matters
- Near-real-time processing is acceptable

Avoid EDA when:
- You need immediate responses
- Transactions must be atomic
- System complexity is already high

EDA reduces coupling, not complexity.

---

## Mental Model (Remember This)

- APIs ask for action
- Events announce facts
- Consumers decide what to do
- Time becomes a first-class concern

EDA is about **reacting**, not **requesting**.

---

## Interview-Ready Summary

> Event-driven architecture is a design pattern where systems communicate by emitting and consuming events asynchronously, enabling loose coupling, scalability, and resilience at the cost of increased complexity and eventual consistency.

If someone says “events make everything simpler,” they’ve never debugged one in production.

---

## Final Takeaway

Event-driven architecture is powerful because it **removes assumptions**:
- About timing
- About availability
- About consumers

But it demands:
- Strong discipline
- Idempotent design
- Serious observability

EDA doesn’t eliminate problems.  
It changes *which problems you are responsible for*.