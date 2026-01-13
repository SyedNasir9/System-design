# Services, Ingress, ConfigMaps, Secrets (Kubernetes)

---

## What These Objects Are (Quick Map)

Kubernetes workloads are useless unless they are:
- reachable (networking)
- configurable (settings)
- secure (secrets)

This node covers four core building blocks:

- **Service**: stable networking for Pods
- **Ingress**: HTTP(S) routing into the cluster
- **ConfigMap**: non-sensitive configuration
- **Secret**: sensitive configuration (credentials, tokens, keys)

If you misuse these, you get:
- broken traffic
- config drift
- leaked credentials
- outages disguised as “network issues”

---

# 1) Services

---

## What Is a Service?

A **Service** provides a **stable network identity** for a set of Pods.

Pods are ephemeral. IPs change.  
Services exist so clients can reliably connect.

A Service gives you:
- stable DNS name
- stable virtual IP
- load balancing across matching Pods

---

## Why Services Matter

Without Services:
- clients must track Pod IPs (impossible at scale)
- rolling updates break connectivity
- scaling changes endpoints constantly

Service = abstraction between:
> “Who is running” and “How do I reach it”

---

## Service Types (What They Actually Do)

### ClusterIP (Default)
- Exposes app *inside* cluster only
- Typical for internal APIs

### NodePort
- Exposes on every node’s IP + a port
- Simple, but blunt and risky

### LoadBalancer
- Provisions external load balancer (cloud environments)
- Common for external-facing apps

### Headless Service
- No cluster IP
- Returns pod IPs directly via DNS
- Common with StatefulSets (databases, brokers)

---

## Operational Pitfalls (Services)

- Wrong label selectors → service points to nothing
- Readiness probes missing → traffic sent to broken pods
- Stateful workloads behind normal Services → identity problems
- NodePort used casually → security exposure

A Service is only as good as the health checks behind it.

---

# 2) Ingress

---

## What Is Ingress?

**Ingress** manages **HTTP/HTTPS traffic from outside the cluster to Services**.

Think of it as:
> A layer-7 router for your cluster.

Ingress usually provides:
- host-based routing (api.example.com)
- path-based routing (/api, /app)
- TLS termination
- optional rewrites and redirects

Ingress itself is just configuration.
You need an **Ingress Controller** (Nginx, Traefik, cloud LB controller, etc.) to make it real.

---

## Why Ingress Matters

Without Ingress:
- you expose each service individually
- you multiply load balancers
- TLS management becomes chaos

Ingress centralizes:
- entry routing
- TLS policy
- external exposure

---

## Ingress vs Service (Clear Distinction)

| Service | Ingress |
|--------|---------|
| L4 / basic load balancing | L7 HTTP routing |
| Stable endpoint for Pods | Entry routing into cluster |
| Works inside cluster | Designed for external traffic |

Ingress routes to **Services**, not Pods directly.

---

## Operational Pitfalls (Ingress)

- Missing Ingress Controller → config does nothing
- TLS misconfig → insecure or broken endpoints
- Path routing confusion (/ vs /app) → 404s forever
- Single Ingress becoming a bottleneck
- No rate limits / WAF controls → easy abuse target

Ingress is a public door. Treat it like one.

---

# 3) ConfigMaps

---

## What Is a ConfigMap?

A **ConfigMap** stores **non-sensitive configuration data**:
- feature flags
- environment configs
- URLs
- tuning parameters

ConfigMaps can be consumed as:
- environment variables
- mounted files/volumes

---

## Why ConfigMaps Matter

ConfigMaps separate:
- application code (image)
- environment configuration (runtime)

This enables:
- same image across dev/stage/prod
- safe config changes without rebuilds
- GitOps-friendly config management

---

## ConfigMap Pitfalls

- Putting secrets in ConfigMaps (classic mistake)
- Huge configs (slow mounts, hard diffs)
- Expecting pods to reload automatically (often they don’t)
- Changing ConfigMap without restart → no effect

ConfigMaps are not dynamic unless your app is built to reload.

---

# 4) Secrets

---

## What Is a Secret?

A **Secret** stores sensitive data:
- passwords
- API keys
- certificates
- tokens

Secrets can be injected as:
- environment variables
- mounted files

---

## Why Secrets Matter

They prevent:
- hardcoding credentials in images
- storing credentials in plain text config
- accidental leaks in logs/repos

Secrets exist because:
> Humans can’t be trusted with copy-paste.

---

## Important Reality Check About Kubernetes Secrets

Kubernetes “Secrets” are often:
- base64 encoded
- not automatically encrypted at rest (depends on cluster config)

So the real security depends on:
- encryption at rest configuration
- RBAC
- auditing
- external secret managers

A Secret is only secret if your cluster is configured to treat it as one.

---

## Best Practice: External Secrets Management

Common approaches:
- cloud secret managers
- :contentReference[oaicite:0]{index=0}
- sealed secrets / encrypted manifests (GitOps-friendly)

Goal:
- keep secrets out of Git
- rotate safely
- control access tightly

---

## Secret Pitfalls

- Storing secrets in Git (even once)
- Injecting secrets into env vars → easy to leak via logs/debug
- Too-broad RBAC permissions
- No rotation strategy
- Using same secret across environments

Secrets leak through operational laziness, not hacking.

---

# Putting It Together (Typical Flow)

A common production pattern:

External Client  
→ Ingress (TLS + routing)  
→ Service (stable endpoint)  
→ Pods (Deployments/StatefulSets)  
→ ConfigMap/Secret (runtime config)

If any layer is wrong:
- traffic fails
- config is inconsistent
- security breaks

---

## Mental Model (Remember This)

- **Service** = stable internal networking
- **Ingress** = external HTTP entry and routing
- **ConfigMap** = non-sensitive config outside images
- **Secret** = sensitive config under strict control

Networking gives reachability.  
Config gives flexibility.  
Secrets give you a fighting chance at not leaking credentials.

---

## Interview-Ready Summary

> Kubernetes Services provide stable networking and load balancing for Pods, Ingress manages external HTTP(S) routing to Services, ConfigMaps store non-sensitive runtime configuration, and Secrets store sensitive data, ideally integrated with strong RBAC and external secret management for secure operations.

If someone says “we put secrets in ConfigMaps because it’s easier,” they’re volunteering your company for a breach.

---

## Final Takeaway

These four objects form the backbone of how apps:
- become reachable
- stay configurable
- remain secure (if done right)

Use them correctly and the platform behaves predictably.  
Use them casually and you get outages, leaks, and late-night debugging.
