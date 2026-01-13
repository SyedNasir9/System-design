# Requirements in System Design

## What Are Requirements?
Requirements define **what a system should do** and **how well it should perform**.  
They act as constraints that guide architectural decisions and prevent unnecessary complexity.

In system design, requirements are broadly divided into:
- **Functional requirements**
- **Non-functional requirements**

---

## Functional Requirements
Functional requirements describe **what the system does from a user’s perspective**.  
They define the core features and behaviors of the system.

### Examples
- Users can create and view posts
- Users can like or comment on content
- Users can transfer money between accounts
- The system should generate a feed for each user

Functional requirements help narrow the scope of the design and clarify what problem is actually being solved.

---

## Non-Functional Requirements
Non-functional requirements describe **how the system should behave under certain conditions**.  
They influence architecture far more than features do.

### Common Non-Functional Requirements
- **Scalability**: Number of users, requests per second (TPS)
- **Latency**: Expected response time
- **Availability**: Uptime requirements (e.g., 99.9%, 99.99%)
- **Consistency vs Availability**: CAP trade-off expectations
- **Durability**: Data loss tolerance
- **Security & Compliance**: Authentication, authorization, encryption
- **Observability**: Logging, metrics, alerting

Two systems with identical features can require completely different architectures due to differing non-functional requirements.

---

## Why Requirements Matter
Starting system design without clear requirements often leads to:
- Over-engineering
- Incorrect technology choices
- Poor scalability or reliability
- Expensive re-architecture later

Requirements act as **hard constraints** that shape decisions around databases, caching, scaling, and consistency models.

Good engineers clarify requirements *before* drawing architecture diagrams.

---

## Practical Approach
When gathering requirements, focus on asking questions such as:
- What scale are we targeting?
- What latency is acceptable to users?
- Is downtime acceptable?
- Which data must be strongly consistent?
- Are traffic spikes expected?

These answers directly influence design trade-offs.

---

## Key Takeaway
Good system design starts with **questions, not diagrams**.  
Clear requirements lead to simpler, more reliable, and more scalable architectures.