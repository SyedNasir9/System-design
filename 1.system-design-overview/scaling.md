# Scaling: Vertical vs Horizontal

## Why Scaling Matters
As systems grow, they eventually exceed their initial capacity.  
Scaling defines how a system handles increased load while maintaining performance and reliability.

There are two primary scaling approaches:
- Vertical scaling
- Horizontal scaling

---

## Vertical Scaling (Scaling Up)
Vertical scaling means increasing the resources of a single machine.

### Characteristics
- Bigger machine (more CPU, RAM, disk)
- Simple to implement
- Limited by hardware constraints
- Creates a single point of failure

Vertical scaling works well for small systems or early-stage applications but does not scale indefinitely.

---

## Horizontal Scaling (Scaling Out)
Horizontal scaling means adding more machines to distribute load.

### Characteristics
- Multiple machines behind a load balancer
- Improved reliability and fault tolerance
- Supports near-linear scaling
- Cloud-friendly and elastic

This approach allows systems to handle failures gracefully and scale with demand.

---

## Trade-offs
Horizontal scaling introduces additional complexity:
- Load balancing
- Stateless service design
- Monitoring and orchestration
- Distributed system challenges

However, this complexity is often worth the gains in availability, resilience, and scalability.

---

## Key Takeaway
Vertical scaling is simple but limited and risky.  
Horizontal scaling adds complexity but is the preferred approach for building reliable, scalable, cloud-native systems.
