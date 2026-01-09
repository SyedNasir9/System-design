Alright. The concept everyone claims to understand right up until their system processes the same message twice and quietly ruins data.

Same MD node style, same DevOps + system design lens, zero mercy for hand-wavy explanations.

Drop this in as idempotency.md.

# Idempotency

---

## What Is Idempotency?

**Idempotency** means that **performing the same operation multiple times results in the same final state as performing it once**.

In system design terms:
> “Doing it again doesn’t make things worse.”

This is not about avoiding retries.  
This is about **surviving retries safely**.

If your system assumes:
> “This request will happen only once”

It is already broken. The only question is *when* you’ll notice.

---

## Why Idempotency Exists

Distributed systems retry by default.

Retries happen because:
- Networks fail
- Timeouts lie
- Consumers crash
- Load balancers guess wrong

Idempotency exists because:
> You cannot prevent retries, but you can prevent damage.

Without idempotency:
- Duplicate charges happen
- Counters inflate
- Data corrupts quietly
- Debugging turns forensic

---

## Where Idempotency Is Required

Idempotency is mandatory in any system that is:

- Distributed
- Asynchronous
- Retry-based
- Event-driven
- Message-queue-backed

Which is most modern systems, whether people admit it or not.

---

## Idempotency vs Deduplication

These are related, but not the same.

| Idempotency | Deduplication |
|------------|---------------|
| Safe repeated execution | Detecting duplicates |
| Design principle | Implementation technique |
| Focuses on outcome | Focuses on input |

Deduplication is one way to *achieve* idempotency.  
It is not the only way.

---

## Common Scenarios That Demand Idempotency

### Message Queues
- At-least-once delivery
- Consumer restarts
- Redelivered messages

This applies to:
- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}

---

### HTTP APIs
- Client retries
- Load balancer retries
- Network timeouts

Especially dangerous for:
- Payments
- Resource creation
- State mutations

---

### Event-Driven Architectures
- Events replayed
- Consumers added later
- Failure recovery reprocessing

Events don’t care if you already handled them.

---

## Non-Idempotent vs Idempotent Operations

### Non-Idempotent


POST /charge-user
→ Charges user every time


### Idempotent


POST /charge-user
Idempotency-Key: abc-123
→ Charge happens once
→ Replays return same result


Same request.  
Different outcomes.  
One causes incidents.

---

## Common Idempotency Patterns

### 1. Idempotency Keys

- Client generates a unique key
- Server stores request outcome
- Replays return stored result

Common in:
- Payment systems
- Order processing
- APIs with retries

Failure mode if done badly:
- Key storage expires too early
- Partial writes corrupt state

---

### 2. Natural Idempotency (State-Based)

Operations that overwrite state instead of mutating it.

Example:


SET user_status = "ACTIVE"


Safe to repeat.  
Hard to mess up.

---

### 3. Deduplication Tables

- Store processed event IDs
- Reject or ignore duplicates

Trade-offs:
- Storage growth
- Cleanup complexity
- Latency impact

Still better than corrupted data.

---

### 4. Exactly-Once Illusions

Some systems advertise “exactly-once”.

Reality:
- It’s usually “effectively-once”
- Achieved through idempotent consumers
- Costs performance and complexity

Design for **at-least-once + idempotency**.  
It scales better and fails more honestly.

---

## Idempotency in Databases

Databases don’t magically solve this.

Common mistakes:
- Blind INSERTs
- Auto-increment abuse
- No uniqueness constraints

Helpful techniques:
- Unique indexes
- Conditional updates
- Transactional writes
- Compare-and-set logic

Idempotency often lives **at the data layer**, whether you like it or not.

---

## Idempotency