# API Gateways (System Design Fundamentals)

---

## What is an API Gateway?
An **API Gateway** is a **reverse proxy** that sits in front of backend services and acts as a **single entry point** for client requests.

It manages, secures, routes, and observes API traffic before forwarding requests to backend services.

---

## Why API Gateways Exist
As systems evolve into **microservices**, clients should not:
- Call dozens of services directly
- Handle authentication logic
- Manage retries, rate limits, or routing
- Know internal service topology

The API Gateway absorbs this complexity.

---

## Core Responsibilities of an API Gateway

### 1. Request Routing
- Routes requests to appropriate backend services
- Supports path-based, header-based, and method-based routing
- Can route to different service versions

---

### 2. Authentication & Authorization
- API keys
- JWT
- OAuth 2.0
- IAM / identity providers
- Centralized auth logic instead of per-service auth

---

### 3. Rate Limiting & Throttling
- Protects backend services from abuse
- Limits requests per:
  - IP
  - User
  - API key
  - Consumer
- Prevents cascading failures

---

### 4. Load Balancing
- Distributes traffic across multiple service instances
- Supports health checks
- Can integrate circuit breakers

---

### 5. Traffic Management
- Canary releases
- A/B testing
- Percentage-based traffic splits
- Version routing

---

### 6. Observability
- Request/response logging
- Metrics (latency, errors, throughput)
- Tracing correlation IDs
- Centralized visibility

---

### 7. Security
- TLS termination
- Request validation
- WAF integration
- Header sanitization
- Hiding internal architecture

---

## API Gateway Architecture (High-Level)

Client  
→ API Gateway  
→ Backend Services (microservices, Lambda, VMs, containers)

The client **never talks directly** to backend services.

---

## Stateless vs Stateful APIs
API Gateways primarily manage:
- **Stateless HTTP/REST APIs**
- **Stateful WebSocket APIs** (in some platforms)

State should live in backend services, not in the gateway.

---

## API Gateway vs Reverse Proxy

| Aspect | Reverse Proxy | API Gateway |
|-----|--------------|------------|
| Routing | Yes | Yes |
| Authentication | Limited | First-class |
| Rate Limiting | Basic | Advanced |
| API Management | No | Yes |
| Developer Experience | No | Yes |
| Microservices Focus | Partial | Strong |

An API Gateway is a **reverse proxy plus API management**.

---

## Kong Gateway (Conceptual View)

### What is Kong?
Kong is a **cloud-native API Gateway** designed for:
- Microservices
- Hybrid cloud
- Multi-cloud environments

---

### Key Kong Concepts
- **Service**: A backend API
- **Route**: How requests map to services
- **Consumer**: Who is calling the API
- **Plugin**: Extends gateway behavior

---

### Why Kong is Popular
- Plugin-based extensibility
- Works with any RESTful API
- Can run:
  - On-prem
  - Cloud
  - Kubernetes
- Not tied to a single cloud provider

---

## Amazon API Gateway (Conceptual View)

### What is Amazon API Gateway?
A **fully managed AWS service** for:
- REST APIs
- HTTP APIs
- WebSocket APIs

Acts as the **front door** to serverless and cloud workloads.

---

### Key Characteristics
- Deep AWS integration
- Works seamlessly with:
  - AWS Lambda
  - EC2
  - Other AWS services
- Handles massive scale automatically

---

### REST vs HTTP APIs (AWS)
- **REST APIs**: Feature-rich, higher cost
- **HTTP APIs**: Lightweight, lower latency, lower cost

---

### Where It Fits Best
- Serverless architectures
- AWS-centric systems
- Minimal operational overhead

---

## Istio Gateways (Service Mesh Perspective)

### Is Istio an API Gateway?
Not exactly.

Istio is a **service mesh**, not a traditional API Gateway.

---

### Istio Gateway Role
- Manages **ingress and egress traffic**
- Built on **Envoy proxies**
- Separates:
  - L4–L6 concerns (Gateways)
  - L7 routing (VirtualServices)

---

### Key Difference
- API Gateway: **north-south traffic** (clients → services)
- Istio: **east-west traffic** (service → service)

---

### Istio Traffic Management Includes
- Fine-grained routing
- Retries
- Timeouts
- Circuit breakers
- Fault injection
- Canary deployments

---

## API Gateway vs Service Mesh

| Aspect | API Gateway | Service Mesh |
|-----|------------|--------------|
| Traffic Type | Client → Service | Service → Service |
| Scope | Edge | Internal |
| Auth | External | Internal |
| Observability | Entry-level | Deep |
| Examples | Kong, AWS API Gateway | Istio, Linkerd |

They **complement**, not replace, each other.

---

## Common Real-World Architecture

Client  
→ **API Gateway**  
→ **Service Mesh (Istio)**  
→ Microservices

- Gateway handles external concerns
- Mesh handles internal reliability

---

## When NOT to Use an API Gateway
- Very small monoliths
- Low traffic internal tools
- Simple direct-service architectures

Gateways add power, but also **latency and complexity**.

---

## System Design Takeaways
- API Gateway = control plane for APIs
- Centralizes cross-cutting concerns
- Reduces client complexity
- Essential for microservices at scale
- Not a replacement for good backend design

---

## Final Thought
If microservices are **organs**,  
the API Gateway is the **immune system**.

Invisible when healthy.  
Catastrophic when missing.
