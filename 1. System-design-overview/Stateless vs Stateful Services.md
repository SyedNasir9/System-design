# Stateless vs Stateful Services

## Stateless Services
Stateless services do not store any session or user-specific data on the server between requests.  
Each request contains all the information required for processing.

### Characteristics
- No session data stored on the server
- Each request is independent
- Easy to scale horizontally
- Simple to recover from failures

Because servers do not depend on in-memory state, any instance can handle any request.

---

## Stateful Services
Stateful services retain information about previous interactions, such as session data or in-memory state.

### Characteristics
- Store session or in-memory data
- Harder to scale horizontally
- Require sticky sessions or coordination between servers
- More complex failure recovery

If a server crashes, in-memory state may be lost unless explicitly replicated.

---

## Why Stateless Services Are Preferred
In modern, cloud-native architectures, stateless services are the default choice because they:
- Scale easily using load balancers
- Allow rapid instance replacement
- Simplify deployments and autoscaling
- Improve system reliability

State is usually offloaded to external systems like databases or caches, allowing application servers themselves to remain stateless.

---

## When Stateful Services Make Sense
Stateful services may be justified when:
- Extremely low latency is required
- Maintaining in-memory context is critical
- The workload is tightly coupled to a single process

These cases are the exception, not the norm.

---

## Key Takeaway
Default to stateless services for most systems.  
Choose stateful designs only when there is a clear and justified need.
