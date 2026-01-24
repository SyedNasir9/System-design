# Zero Trust Basics

---

## What This Means

**Zero Trust** is a security model that assumes:
- the network is not safe by default
- identities can be compromised
- internal traffic is not automatically trustworthy

So every access must be:
- **authenticated**
- **authorized**
- **continuously evaluated**
- **logged**

Core idea:
> “Inside the network” is not a permission level.

---

## Why This Exists

Traditional security was “castle and moat”:
- strong perimeter defenses
- inside = trusted

That fails because:
- phishing exists
- credentials leak
- laptops get compromised
- cloud networks are dynamic
- attackers move laterally once inside

Zero Trust exists to:
- reduce blast radius
- prevent lateral movement
- enforce least privilege everywhere
- make access auditable and policy-driven
- work in cloud + hybrid + remote work realities

---

# 1) The Core Principles

---

## Principle A: Never Trust, Always Verify
Every request must prove identity, even internally.

Examples:
- service-to-service calls require identity (mTLS, JWT)
- admins must use SSO + MFA
- APIs validate tokens on every request

---

## Principle B: Least Privilege Access
Give the minimum access needed:
- minimal actions (what)
- minimal resources (where)
- minimal time (how long)

Use:
- short-lived credentials
- scoped roles/policies
- time-bound access (JIT)

---

## Principle C: Assume Breach
Design as if compromise will happen:
- segment networks
- isolate workloads
- monitor continuously
- make containment fast

Goal:
> One compromised pod should not become “welcome to the entire company”.

---

## Principle D: Continuous Monitoring and Policy Enforcement
Trust is not permanent.
Decisions can change based on:
- device posture
- geo/IP anomalies
- unusual behavior
- risk scoring

This is where “security” becomes an ongoing system, not a checkbox.

---

# 2) What Zero Trust Looks Like in System Design

---

## Identity as the New Perimeter

Instead of trusting IP ranges and subnets, you trust:
- user identity (SSO)
- workload identity (service accounts, IAM roles)
- device identity (managed devices)

Then enforce policies based on identity + context.

---

## East-West Traffic Security (Inside the Network)

This is a major focus:
- authenticate service-to-service calls
- authorize per service/API method
- encrypt in transit (mTLS)
- restrict network paths (network policies)

Because attackers love east-west movement.

---

# 3) Key Components (Practical Building Blocks)

---

## A) Strong Authentication (AuthN)
For humans:
- SSO (OIDC/SAML)
- MFA (mandatory)
- conditional access (device compliance, location)

For workloads:
- service accounts / workload identity
- short-lived tokens
- no hardcoded long-lived keys

---

## B) Fine-Grained Authorization (AuthZ)
- RBAC/ABAC policies
- per-service and per-endpoint permissions
- policy engines (OPA/Gatekeeper, etc.)
- IAM conditions (tags, time, source identity)

Rule:
> “Allowed because internal” is the old world. Stop doing that.

---

## C) Network Segmentation + Microsegmentation
- VPC/VNet segmentation (edge/app/data/management)
- Kubernetes NetworkPolicies
- service mesh authorization policies

Network rules become enforcement, not trust.

---

## D) Encryption in Transit (mTLS)
- encrypt internal traffic
- verify both sides (client and server identity)
- prevents sniffing and some impersonation

Service mesh often helps implement this at scale.

---

## E) Device and Endpoint Posture
For humans accessing systems:
- managed devices
- patch level checks
- EDR/AV status
- certificate-based access

This prevents “stolen password from random laptop” from being enough.

---

## F) Observability and Audit
- centralized logs (auth, access, policy decisions)
- flow logs (network)
- anomaly detection
- alerting on suspicious behavior
- traceability: who did what, from where, when

If you can’t audit it, you can’t trust it (ironically).

---

# 4) Zero Trust in DevOps Workflows

---

## CI/CD Access (Modern Approach)
Good:
1. CI job gets identity via **OIDC**
2. assumes role with minimal permissions
3. access is short-lived
4. fully auditable

Bad:
- long-lived cloud keys in CI variables forever
- “deploy” user with admin permissions

---

## Kubernetes Runtime Access
Good:
- pods use workload identity
- least privilege IAM to cloud resources
- network policies restrict east-west traffic
- secrets delivered via secure mechanisms (Vault/CSI/KMS-backed encryption)

---

## Admin Access to Production
Good:
- SSO + MFA
- just-in-time privileged access
- break-glass with alerts
- audited sessions

Bad:
- SSH keys shared in a group chat
- open bastion to the world “temporarily”

---

# 5) Common Misconceptions

---

## “Zero Trust means zero access”
No. It means **verified and controlled access**.

## “We have a VPN, so we’re Zero Trust”
VPN gives network entry. Zero Trust still requires:
- per-request auth
- least privilege
- segmentation
- continuous checks

## “It’s just a tool we install”
No. It’s an architecture + policy model that tools help enforce.

---

# 6) Common Mistakes

---

- trusting internal networks too much
- relying only on IP allowlists (fragile in cloud)
- not having workload identity (using static keys)
- broad IAM policies (“admin to make it work”)
- no segmentation (flat networks)
- not logging policy decisions
- treating Zero Trust as a one-time project

---

# 7) Mental Model (Remember This)

---

Old model:
- inside network = trusted
- outside = untrusted

Zero Trust model:
- every request = untrusted until proven otherwise
- identity + context decides access
- verify continuously
- limit blast radius

---

## Interview-Ready Summary

> Zero Trust is a security model that assumes no implicit trust based on network location and requires every access request to be authenticated, authorized, and audited. It relies on strong identity for humans and workloads, least privilege policies, segmentation and microsegmentation to prevent lateral movement, encryption in transit (often mTLS), and continuous monitoring and risk-based access decisions. In DevOps, Zero Trust is implemented through short-lived credentials (OIDC/assumable roles), workload identity in Kubernetes, strict network policies, and auditable, just-in-time privileged access for production.

---

## Final Takeaway

Zero Trust is what you build when you accept reality:
- credentials get stolen
- networks get breached
- internal traffic is not magically safe

So you stop trusting the network and start trusting **verified identity + policy**.
