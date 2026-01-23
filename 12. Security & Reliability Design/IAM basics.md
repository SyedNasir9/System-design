# IAM Basics (Identity and Access Management)

---

## What This Means

**IAM (Identity and Access Management)** is how you control:
- **Who** can access your systems (identity)
- **What** they can do (permissions)
- **Under what conditions** (context like IP, time, MFA, device, tags)

Core idea:
> “It worked on my account” is not a security strategy. IAM is the rulebook.

---

## Why This Exists

Without IAM, you get:
- shared passwords (gross)
- unclear ownership (“who deleted prod?”)
- excessive permissions (“admin because faster”)
- no audit trail
- easy breaches from leaked keys

IAM exists to:
- enforce **least privilege**
- prevent unauthorized access
- separate duties (dev != prod god-mode)
- enable auditing and compliance
- make access manageable at scale

---

# 1) The Core Building Blocks

---

## Identities

### Users (Humans)
- individual accounts (developers, admins)
- should be tied to a real person

### Groups (Collections of Humans)
- attach policies to groups instead of individuals
- e.g., `devs`, `ops`, `security`, `interns`

### Roles (Assumable Identities)
- not tied to one human
- assumed temporarily by:
  - users (e.g., “assume prod deploy role”)
  - services (e.g., an app pod gets a role)
  - external identities (OIDC/SAML)

Roles are how you avoid long-lived access keys everywhere.

### Service Accounts / Workload Identities
- identities for applications and automation
- in Kubernetes: `ServiceAccount` + cloud IAM mapping (IRSA / Workload Identity)

---

## Authentication vs Authorization

- **AuthN (Authentication)**: prove who you are  
  (password, MFA, SSO, key, certificate)
- **AuthZ (Authorization)**: what you’re allowed to do  
  (policies, permissions, roles)

People mix these up constantly. Computers don’t forgive it.

---

# 2) Policies and Permissions

---

## What a Policy Is

A **policy** is a set of rules that say:
- allowed actions
- on which resources
- sometimes with conditions

Typical structure (conceptual):
- **Effect**: Allow / Deny
- **Action**: what can be done
- **Resource**: what it applies to
- **Condition**: extra rules (MFA, IP range, tags, time)

Key rule:
> Explicit **Deny** beats **Allow**.

---

## Least Privilege (The Non-Negotiable Principle)

Grant only what’s needed:
- minimal actions
- minimal resources
- minimal time

“Admin for convenience” is how breaches become case studies.

---

# 3) Common IAM Patterns in DevOps

---

## Pattern A: Human Access via SSO + MFA

Workflow:
1. user logs in via SSO (Google/Okta/Azure AD)
2. MFA enforced
3. user assumes a role (dev/prod) with time-limited access
4. actions are logged

Benefits:
- no shared credentials
- central user lifecycle (join/leave changes access quickly)
- strong auditing

---

## Pattern B: CI/CD Role for Deployments

Workflow:
1. CI/CD system gets identity (OIDC preferred)
2. pipeline assumes deploy role
3. role has restricted permissions (only deploy actions)
4. logs show which pipeline run did what

Avoid:
- long-lived access keys stored in secrets forever

---

## Pattern C: App-to-Cloud Access (Workload Identity)

Workflow:
1. app runs with a workload identity (service account)
2. cloud maps it to an IAM role
3. app gets short-lived credentials automatically
4. app accesses only required resources

This is the clean way to do “pods need S3” (or equivalent).

---

## Pattern D: Break-Glass Access

Emergency access is needed, but controlled:
- separate break-glass role
- MFA required
- strong approvals + alerts
- short duration
- audited and reviewed

Because production will eventually break at 2:13 AM.

---

# 4) IAM in System Design (Where It Fits)

---

## IAM Controls the “Trust Boundary”

IAM is part of:
- API gateways (authz decisions)
- service-to-service security (mTLS + identity)
- data access (DB, object storage)
- admin actions (infra changes)

Good system design includes:
- who can call what
- what identity a service runs as
- how secrets/credentials rotate
- how permissions are reviewed

---

# 5) Common Mistakes

---

## Over-Permissioning
- `*:*` permissions (aka “I gave up”)
- broad resource access instead of scoped resources

## Long-Lived Credentials
- access keys checked into repos
- keys that never expire

## No Separation of Environments
- dev credentials can touch prod

## Weak Auditing
- no logs, or logs not monitored

## Manual Permission Management
- clicking in console instead of IaC
- no review process

---

# 6) Practical Checklist (DevOps-Friendly)

---

## Strong Defaults
- MFA on all humans
- SSO for workforce identities
- roles over users for automation
- short-lived credentials (OIDC, STS-style)
- least privilege policies
- tagging + conditions for scoped access
- centralized logging + alerts

## Operational Hygiene
- periodic access reviews
- remove unused permissions
- rotate secrets if any long-lived remain
- detect leaked credentials (secret scanning)

---

## Interview-Ready Summary

> IAM is the system that manages identities and permissions, defining who can access what resources and under which conditions. It separates authentication (proving identity) from authorization (granting permissions) and is implemented via users/groups/roles and policies enforcing least privilege. In DevOps, best practice is to use SSO+MFA for humans and short-lived, assumable roles (often via OIDC) for CI/CD and workloads, with strong auditing, environment separation, and break-glass access controls.

---

## Final Takeaway

IAM is boring on purpose.
It’s the seatbelt of cloud and infrastructure:
- annoying when you’re in a hurry
- priceless when something goes wrong
