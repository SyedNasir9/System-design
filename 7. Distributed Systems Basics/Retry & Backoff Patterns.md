# Retry & Backoff Patterns

---

## What Are Retry & Backoff Patterns?

**Retry** is the act of attempting an operation again after failure.  
**Backoff** is the strategy that controls **when** and **how often** retries happen.

Together, they answer a critical question:
> “When something fails, how do we try again *without making things worse*?”

Retries are inevitable.  
Uncontrolled retries are disasters waiting for traffic.

---

## Why Retry Patterns Exist

Failures are normal in distributed systems.

Common causes:
- Network timeouts
- Transient service overload
- Cold starts
- Leader elections
- Short-lived outages

Retry patterns exist because:
> Immediate failure is bad, but blind retry is worse.

Retries are meant to **absorb transient failure**, not amplify it.

---

## The Dark Side of Retries

Retries feel safe. They are not.

Poor retry design causes:
- Retry storms
- Cascading failures
- Thundering herds
- Self-inflicted denial of service

If every failing request retries immediately:
> The system collapses faster than if it had failed once.

---

## Retry vs Backoff (Clear Distinction)

| Retry | Backoff |
|-----|--------|
| Decision to try again | Timing between retries |
| Logical choice | Control mechanism |
| Necessary | Protective |

Retries without backoff are reckless.  
Backoff without retries is just giving up slowly.

---

## Common Backoff Strategies

---

### 1. Fixed Backoff

Retry after a constant delay.

Example:
Retry every 1 second


Pros:
- Simple
- Predictable

Cons:
- Causes synchronized retries
- Amplifies load during outages

Acceptable only at very small scale.

---

### 2. Linear Backoff

Delay increases linearly.

Example:
1s → 2s → 3s → 4s

yaml
Copy code

Pros:
- Better than fixed
- Simple to implement

Cons:
- Still predictable
- Still risky under heavy load

---

### 3. Exponential Backoff (Default Choice)

Delay doubles with each retry.

Example:
1s → 2s → 4s → 8s

Pros:
- Reduces pressure on failing services
- Widely adopted
- Scales well

Cons:
- Needs caps and jitter
- Can increase tail latency

This is the industry default for a reason.

---

### 4. Exponential Backoff with Jitter (Correct Choice)

Adds randomness to retry delays.

Example:
Random(0, 2^n seconds)

Pros:
- Prevents retry synchronization
- Reduces thundering herd effects
- Production-safe

Cons:
- Harder to reason about timing
- Slightly more complex

If you remember only one strategy, remember this one.

---

## Retry Limits (Always Required)

Retries must be **bounded**.

Unbounded retries cause:
- Infinite loops
- Resource exhaustion
- Hidden failures

Typical limits:
- Max retry count
- Max total retry duration
- Circuit breaker interaction

A retry policy without limits is a bug, not a feature.

---

## Retry Patterns in Practice

---

### HTTP APIs

Retries should happen when:
- Timeout occurs
- 5xx errors appear
- Connection resets

Retries should *not* happen when:
- 4xx client errors
- Validation failures
- Authorization errors

Retrying a bad request just wastes time.

---

### Message Queues

Retry behavior is fundamental in:
- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}

Common patterns:
- Visibility timeout retries
- Delayed retries
- Dead-letter queues (DLQ)

Message systems assume failure.  
Your consumers must too.

---

### Event-Driven Systems

In event-driven architectures:
- Consumers retry on failure
- Events may be replayed
- Duplicate processing is normal

This makes **idempotency mandatory**, not optional.

Retries without idempotency corrupt data quietly.

---

## Retry + Circuit Breaker (They Belong Together)

Retries alone are reactive.  
Circuit breakers are protective.

Together they:
- Retry during transient failure
- Stop retries during sustained failure
- Allow systems to recover gracefully

Retry without a circuit breaker is optimism.  
Circuit breaker without retry is pessimism.

Balanced systems use both.

---

## Backoff vs Latency (Trade-off)

Retries improve reliability at the cost of latency.

Trade-offs:
- More retries → higher success rate
- More retries → higher tail latency

This trade-off must be:
- Measured
- Tuned
- Revisited regularly

Hardcoded retry values age badly.

---

## Observability for Retry Behavior

If you don’t observe retries, they will surprise you.

You must track:
- Retry counts
- Retry latency
- Failure reasons
- DLQ growth
- Circuit breaker trips

Silent retries hide systemic problems until they explode.

---

## Common Retry Mistakes

- Retrying everything
- No backoff
- No jitter
- No retry limits
- Retrying non-idempotent operations
- Hiding retries from metrics

Retries should be visible, controlled, and intentional.

---

## When NOT to Retry

Do not retry when:
- Errors are permanent
- Data is invalid
- Side effects are irreversible
- Load is already overwhelming

Sometimes failing fast is the most reliable behavior.

---

## Mental Model (Remember This)

- Failures are normal
- Retries are dangerous
- Backoff is protection
- Jitter prevents stampedes
- Idempotency makes retries safe

Retries are a tool, not a reflex.

---

## Interview-Ready Summary

> Retry and backoff patterns handle transient failures by reattempting operations with controlled delays, typically using exponential backoff with jitter, while enforcing limits and requiring idempotent operations to prevent cascading failures.

If someone says “we just retry three times,” they’re describing hope, not design.

---

## Final Takeaway

Retries keep systems alive during failure.  
Bad retries kill them faster.

Good retry design:
- Assumes failure
- Protects downstream services
- Works with idempotency and circuit breakers

Distributed systems don’t need more retries.  
They need **smarter ones**.