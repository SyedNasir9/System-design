# IAM Boundaries (Permission Boundaries)

---

## What This Means

**IAM boundaries (permission boundaries)** are a **maximum permission limit** that an IAM identity (user or role) can ever have, no matter what policies are attached to it.

They do NOT grant permissions.
They only say:
> “Even if someone tries to give you more power, this is the absolute ceiling.”

Think of boundaries as a **seatbelt for IAM**.

---

## Why This Exists

Without boundaries:
- developers accidentally grant admin access
- CI/CD roles slowly become god-mode
- least privilege erodes over time
- one misconfigured policy becomes a security incident

IAM boundaries exist to:
- enforce least privilege at scale
- protect against over-permissioning
- limit blast radius of mistakes
- enable safe delegation (teams manage roles, security sets limits)

Core idea:
> Boundaries protect you from future mistakes, not current intent.

---

# 1) How IAM Boundaries Work

---

## The Permission Evaluation Model

For an action to be allowed, **all** of these must allow it:

1. Identity-based policy (attached to user/role)
2. **Permission boundary**
3. Resource-based policy (if any)
4. No explicit deny anywhere

Effective permission =
Identity Policy ∩ Permission Boundary

Rule:
> If the boundary doesn’t allow it, it doesn’t matter what the role policy says.

---

## What Boundaries Are (and Aren’t)

### Boundaries ARE:
- a maximum permission guardrail
- attached to users or roles
- enforced automatically
- invisible to application code

### Boundaries are NOT:
- a permission grant
- a replacement for IAM policies
- a runtime security control
- a network control

Boundaries are guardrails, not keys.

---

# 2) Common Use Cases (Where Boundaries Shine)

---

## A) CI/CD Roles (Very Common)

Problem:
- pipelines slowly gain permissions “just to make it work”
- no one removes them later

Solution:
- apply a boundary that only allows:
  - deploy actions
  - read-only infra state
  - limited service scope

Even if someone attaches `AdministratorAccess`, the boundary blocks it.

---

## B) Delegated IAM Management

Scenario:
- platform/security team defines boundaries
- application teams create roles within those limits

This allows:
- self-service IAM
- without security chaos

Rule:
> Delegate safely, or don’t delegate at all.

---

## C) Protecting Production Accounts

Boundaries ensure:
- dev roles cannot touch prod
- automation roles can’t escalate privileges
- human roles don’t accidentally gain destructive permissions

---

## D) Limiting Privilege Escalation Paths

Boundaries can block:
- IAM policy creation
- role assumption
- permission pass-through (`iam:PassRole`)
- access to security services

This stops attackers from escalating even after initial access.

---

# 3) Boundary Design Patterns

---

## Pattern 1: Environment-Based Boundary

Example:
- Dev boundary allows dev resources only
- Prod boundary allows prod resources only

This enforces:
- hard separation between environments
- zero chance of “oops, wrong account”

---

## Pattern 2: Service-Specific Boundary

Example:
- ECS task role boundary only allows:
  - logs
  - metrics
  - S3 read/write for specific buckets

Even if someone adds extra permissions later, the boundary blocks them.

---

## Pattern 3: No-IAM-Write Boundary

Boundary explicitly denies:
- `iam:*`
- `kms:*`
- `organizations:*`

This prevents:
- privilege escalation
- security control tampering

Very common for application and CI roles.

---

# 4) Boundaries vs Other IAM Controls

---

## Boundary vs IAM Policy

| Aspect | IAM Policy | Permission Boundary |
|----|-----------|--------------------|
| Grants permissions | Yes | No |
| Limits permissions | Indirectly | Yes (hard cap) |
| Used daily by apps | Yes | No |
| Prevents future overreach | No | Yes |

---

## Boundary vs SCP (Service Control Policy)

| Aspect | Boundary | SCP |
|----|-------|-----|
| Scope | User / Role | Account / OU |
| Purpose | Limit role permissions | Limit entire accounts |
| Granularity | Fine-grained | Coarse |
| Typical use | App & CI roles | Org-wide guardrails |

Rule:
> SCPs protect accounts. Boundaries protect identities.

---

# 5) Practical Example (Conceptual)

---

## CI/CD Role Without Boundary
- role has deploy permissions
- someone adds `iam:PassRole`
- attacker escalates privileges
- game over

## CI/CD Role With Boundary
- boundary blocks `iam:*`
- escalation attempt fails
- blast radius contained

Same role.
Very different outcome.

---

# 6) Common Mistakes

---

- assuming boundaries grant permissions
- forgetting to attach boundaries to new roles
- overly permissive boundaries (“*:*” defeats the point)
- mixing boundaries and SCPs without understanding precedence
- no documentation of boundary intent
- not testing boundary effects (debugging becomes painful)

Worst mistake:
> “We’ll add boundaries later.”

Later is when incidents happen.

---

# 7) When to Use IAM Boundaries

---

## Use Boundaries When:
- multiple teams create IAM roles
- CI/CD automation exists
- least privilege must be enforced long-term
- you want protection from future mistakes

## You Might Skip Boundaries When:
- very small environment
- single admin team
- no delegation at all

But even then, boundaries age better than trust.

---

## Interview-Ready Summary

> IAM permission boundaries define the maximum permissions an IAM user or role can have, regardless of attached policies. They do not grant access but act as a guardrail that restricts future privilege expansion. Effective permissions are the intersection of identity policies and the boundary. Boundaries are commonly used to secure CI/CD roles, enable safe delegation of IAM management, prevent privilege escalation, and enforce least privilege at scale. They complement IAM policies and organizational SCPs by operating at the identity level.

---

## Final Takeaway

IAM boundaries are how you protect yourself from:
- rushed changes
- future developers
- temporary “just make it work” decisions

They don’t stop smart attackers.
They stop **avoidable mistakes**, which is most incidents anyway.
