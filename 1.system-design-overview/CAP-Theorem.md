# CAP Theorem

## The Trade-off
The CAP Theorem states that in a distributed system, it is impossible to simultaneously guarantee all three of the following:

- **Consistency**: Every read returns the most recent write
- **Availability**: Every request receives a response
- **Partition Tolerance**: The system continues to operate despite network failures

In real-world distributed systems, **partition tolerance is mandatory** because network failures are unavoidable.  
As a result, system designers must choose between **Consistency (C)** and **Availability (A)** during network partitions.

---

## When to Choose Consistency
Consistency ensures that all users see the same, up-to-date data, even if it means temporarily rejecting requests.

### Common Use Cases
- Payments and financial transactions
- Inventory management
- Authentication and authorization
- Security-critical data

In these systems, serving incorrect or stale data is more harmful than temporary unavailability.

---

## When to Choose Availability
Availability ensures that the system continues to respond, even if some data may be stale.

### Common Use Cases
- News feeds and timelines
- Caches
- Logging and analytics systems

For these workloads, uninterrupted access and responsiveness matter more than immediate correctness.

---

## Mixing CAP Choices in a System
Most real-world systems are not purely CP or AP.  
Different components can make different CAP trade-offs based on their responsibilities.

For example:
- A browsing feed may favor availability
- A checkout or payment service may favor consistency

---

## Key Takeaway
CAP choices are **context-dependent**.  
Different parts of the same system can and often should make different consistency and availability trade-offs.
