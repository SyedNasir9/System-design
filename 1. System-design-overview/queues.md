# Queues (Async & Traffic Spikes)

## What Are Queues?
A queue is a buffering mechanism that temporarily stores requests or tasks so they can be processed asynchronously.  
Instead of forcing services to handle all requests immediately, queues allow systems to process work at a controlled rate.

Queues help decouple request intake from request processing.

---

## Why Queues Matter
Queues are essential for building resilient systems, especially under unpredictable load:

- Handle sudden traffic spikes without dropping requests
- Enable asynchronous and background processing
- Decouple producer services from consumer services
- Allow independent scaling of producers and consumers

Without queues, systems often fail by returning errors when backend services are overloaded.

---

## Common Use Cases
- Background jobs (emails, notifications)
- Media processing (image/video encoding)
- Order processing
- Logging and event ingestion
- Build and deployment pipelines

---

## Trade-offs and Challenges
Queues introduce new considerations:

- **Added latency**: results are not returned immediately
- **Queue backlog risk**: slow consumers can cause unbounded growth
- **Operational complexity**: monitoring, retries, and failure handling are required

A growing queue is often a signal that the system is under-provisioned or poorly balanced.

---

## Key Takeaway
Queues protect systems from overload and enable asynchronous workflows, but they must be carefully monitored and scaled to avoid becoming bottlenecks.
