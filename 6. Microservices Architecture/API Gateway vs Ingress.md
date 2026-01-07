# API Gateway vs Ingress

---

## The Core Difference (In One Line)

- **Ingress** manages **traffic *into* a Kubernetes cluster**
- **API Gateway** manages **APIs *as products***  

Same word “gateway”, very different jobs.

---

## What Is an Ingress?

An **Ingress** is a Kubernetes-native resource that controls **external access to services inside a cluster**, usually over HTTP/HTTPS.

Think:
- “How does traffic enter my cluster?”
- “Which service gets this path or host?”

---

### Ingress Responsibilities

- Host-based routing  
- Path-based routing  
- TLS termination  
- Basic load balancing  

That’s it. No magic.

---

### Ingress Architecture

Client  
→ Load Balancer  
→ Ingress Controller  
→ Kubernetes Service  
→ Pod

Ingress does **routing**, not governance.

---

### What Ingress Is NOT Good At

- Authentication & authorization  
- Rate limiting per consumer  
- API keys  
- Request transformation  
- Monetization  
- Versioning APIs  

Trying to force these into Ingress is how YAML files become unreadable crimes.

---

## What Is an API Gateway?

An **API Gateway** sits **in front of backend services** and treats APIs as **managed interfaces**, not just traffic routes.

Think:
- “Who can call my API?”
- “How often?”
- “With what credentials?”
- “How do I protect my backend?”

---

### API Gateway Responsibilities

- Authentication (JWT, OAuth, API keys)
- Rate limiting & throttling
- Request/response transformation
- API versioning
- Quotas per client
- Observability and analytics
- Sometimes caching

Ingress doesn’t even try to do this properly.

---

### API Gateway Architecture

Client  
→ API Gateway  
→ Backend Service (Kubernetes, VM, serverless, anything)

Notice: **API Gateway does not care where your service runs**.

---

## Feature Comparison

| Feature | Ingress | API Gateway |
|------|--------|------------|
| Purpose | Traffic routing | API management |
| Scope | Cluster-level | Platform-level |
| Auth | Very limited | First-class |
| Rate limiting | Basic / add-ons | Native |
| API versioning | Manual | Built-in |
| Request transformation | Minimal | Powerful |
| Client awareness | None | Strong |
| Protocol support | Mostly HTTP | HTTP, gRPC, WebSocket |

---

## Where Each One Fits

---

### Use Ingress When

- You are inside Kubernetes
- You need simple routing
- Traffic is mostly internal or trusted
- You already handle auth elsewhere

Ingress is a **plumber**, not a security guard.

---

### Use API Gateway When

- You expose APIs to external consumers
- You need security, quotas, and governance
- You want API-level observability
- You run microservices across platforms

API Gateway is a **bouncer + accountant + traffic cop**.

---

## Can They Work Together?

Yes. And this is the **correct setup** in real systems.

Client  
→ API Gateway  
→ Ingress  
→ Services  

Why?
- API Gateway handles **who & how**
- Ingress handles **where**

Clean separation of concerns.

---

## Common Anti-Patterns

- Using Ingress as a full API Gateway  
- Putting auth logic inside every service  
- Exposing services directly via Ingress to the internet  
- Managing API versions using URL hacks only  

These scale badly and fail loudly.

---

## Mental Model (Remember This)

- **Ingress** = Cluster traffic router  
- **API Gateway** = API control plane  

Ingress answers:
> “Where should this request go?”

API Gateway answers:
> “Should this request exist at all?”

---

## Key Takeaways

- They solve different problems
- Ingress is infrastructure-focused
- API Gateway is product & security-focused
- Serious systems use **both**

---

## Interview-Grade Summary

> *Ingress controls traffic into Kubernetes.*  
> *API Gateways control access to APIs.*

If someone says “they’re the same,” they haven’t operated either.
