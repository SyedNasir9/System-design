# Network Segmentation

---

## What This Means

**Network segmentation** is the practice of splitting a network into smaller, isolated zones (segments) and controlling traffic between them using explicit rules.

Core idea:
> If everything can talk to everything, eventually something will. Usually something malicious.

Segmentation is how you reduce blast radius and make lateral movement harder.

---

## Why This Exists

Without segmentation, a single compromise becomes:
- full internal network access
- easy lateral movement (pivoting)
- noisy outages spreading across systems
- compliance nightmares
- “why is the database reachable from the internet?” moments

Segmentation exists to:
- protect critical systems (DBs, secrets, control planes)
- isolate environments (dev/qa/prod)
- limit lateral movement during breaches
- enforce least privilege at the network layer
- improve reliability by isolating failure domains

---

# 1) Segmentation Levels (Where You Can Segment)

---

## Level A: Physical / Data Center
- separate switches/VLANs
- separate racks, separate links

Usually for on-prem or hybrid setups.

---

## Level B: Cloud Network (VPC/VNet Segmentation)
Common structures:
- multiple **VPCs/VNets** (strong isolation)
- multiple **subnets** inside a VPC (public/private)
- route tables controlling how subnets connect
- gateways (Internet Gateway, NAT, Transit Gateway equivalents)

Typical split:
- **Public subnet**: load balancers, bastions (carefully)
- **Private subnet**: app services
- **Isolated subnet**: databases, internal-only systems (no direct internet route)

---

## Level C: Host / Instance Level
- security groups / firewalls
- OS firewall rules (iptables/nftables)
- instance-level policies

---

## Level D: Kubernetes / Service Mesh
- **Kubernetes NetworkPolicies**
- service mesh policies (mTLS + authorization)
- ingress/egress control per namespace/workload

This is segmentation inside the cluster, where things can otherwise become a free-for-all.

---

# 2) Core Concepts

---

## Security Zones (Trust Boundaries)

Typical zones:
- **Edge / DMZ**: internet-facing entry points (LB, API gateway, WAF)
- **App tier**: internal services
- **Data tier**: DBs, caches, queues
- **Management**: CI/CD runners, admin tools, bastion, monitoring
- **Third-party**: partner integrations, SaaS connectivity

Rule:
> The data tier should be the hardest place to reach, not the easiest.

---

## North-South vs East-West Traffic

- **North-South**: traffic in/out of your network (internet ↔ services)
- **East-West**: internal traffic between services

Most breaches spread via east-west movement.
Segmentation is how you put walls inside the building, not just at the front door.

---

## Allowlist vs Denylist

- **Allowlist (default deny)**: block everything unless explicitly allowed
- **Denylist (default allow)**: allow everything unless blocked

For security, prefer:
> default deny + explicit allow.

---

# 3) How It’s Implemented (Common Controls)

---

## Subnets + Route Tables
- decide which subnets have internet routes
- isolate subnets with no route to internet gateway
- control NAT usage for outbound-only needs

---

## Security Groups / Firewalls
- allow app → DB on specific ports only
- restrict admin access to management networks
- limit inbound paths to a small set of entry points

---

## Network ACLs (Optional Extra Layer)
- stateless subnet-level rules
- useful for coarse boundaries
- dangerous if mismanaged because they’re blunt instruments

---

## Private Connectivity
Reduce exposure by using private links:
- private endpoints to managed services
- peering between networks
- transit gateway/hub-and-spoke routing

Goal:
> Keep internal traffic off the public internet.

---

# 4) Segmentation in DevOps Workflows

---

## Typical DevOps “Segmentation-Aware” Architecture

1. **Internet → WAF/CDN → Load Balancer (DMZ)**
2. LB forwards to **app services** in private subnets
3. app services access **data services** in isolated subnets
4. admin access only via **management segment** (bastion/SSM/VPN)
5. logging/monitoring in separate segment with controlled inbound rules

DevOps responsibilities:
- define segments as code (Terraform)
- enforce rules (security groups, policies)
- implement CI/CD that can deploy without broad network access
- monitor and audit network flows

---

## Environment Segmentation (Dev/QA/Prod)

Best practice:
- separate accounts/projects (strongest)
- or separate VPCs/VNets
- at minimum separate subnets + strict IAM + strict routing

Never do:
- “dev can reach prod DB” because “testing”
That’s how prod becomes your test environment (and users become your QA team).

---

# 5) Microsegmentation and Zero Trust (Concept Level)

---

## Microsegmentation
Instead of large zones, you segment down to:
- service-to-service rules
- workload-level policies

In Kubernetes:
- namespace segmentation
- NetworkPolicies per service
- service mesh authz policies

---

## Zero Trust Basics
Assume the network is hostile:
- authenticate every request
- authorize every request
- encrypt traffic (mTLS)
- log decisions

Network segmentation supports zero trust, but it’s not the whole thing.

---

# 6) Common Mistakes

---

- flat networks (“everything in one subnet”)
- exposing databases publicly (yes, people still do this)
- relying only on perimeter security (no east-west controls)
- too many exceptions (“temporary allow all”, permanently)
- unclear ownership of firewall rules (rules never removed)
- no visibility (no flow logs, no monitoring)
- mixing dev/prod in the same segment

---

# 7) Observability and Verification

---

## What to Monitor

- VPC/VNet flow logs (who talked to whom)
- firewall/security group changes (audit)
- denied traffic spikes (possible scanning/misconfig)
- unusual east-west patterns (lateral movement)
- egress traffic (data exfil signals)

Also:
- periodic connectivity tests (can service A reach DB? should it?)

Rule:
> If you can’t *prove* segmentation works, you’re just drawing boxes in diagrams.

---

## Interview-Ready Summary

> Network segmentation divides infrastructure into isolated zones and controls traffic between them to reduce blast radius, prevent lateral movement, and enforce least privilege at the network layer. It’s implemented using constructs like VPC/VNet boundaries, public/private/isolated subnets with route tables, security groups/firewalls, and in Kubernetes via NetworkPolicies and service mesh controls. Effective segmentation separates edge, app, data, and management planes, isolates environments (dev/qa/prod), limits egress, and is validated through flow logs, audits, and connectivity testing.

---

## Final Takeaway

Network segmentation is how you stop a small incident from becoming a full-company disaster.
It’s not paranoia. It’s just acknowledging that computers are untrustworthy and humans are even worse at clicking links.
