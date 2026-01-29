# Principle of Least Privilege

---

## What This Means

The **Principle of Least Privilege (PoLP)** means:
- give identities **only the permissions they need**
- **only for the resources they need**
- **only for the time they need**

Nothing more.

Core idea:
> Every extra permission is a future incident waiting patiently.

---

## Why This Exists

Without least privilege:
- compromised credentials cause massive damage
- bugs have wider blast radius
- “temporary” access becomes permanent
- audits become painful
- security reviews turn into archaeology

Least privilege exists to:
- reduce blast radius
- limit lateral movement
- protect critical systems
- make incidents survivable instead of catastrophic

---

# 1) What Least Privilege Applies To

---

## Identities
- humans (admins, developers)
- workloads (apps, services, pods)
- automation (CI/CD, scripts)

If it has an identity, it needs least privilege.

---

## Resources
- APIs
- databases
- storage
- secrets
- network access
- infrastructure controls

Least privilege is not just IAM. It’s system-wide.

---

# 2) The Three Dimensions of Least Privilege

---

## A) Scope (What Can Be Done)
- restrict actions (`read` vs `write` vs `delete`)
- avoid wildcards (`*`)
- block dangerous admin APIs unless required

Example:
- read-only access to S3 bucket
- not full S3 admin

---

## B) Resource (Where It Can Be Done)
- limit to specific resources
- avoid global access

Example:
- access to `prod-logs-bucket`
- not all buckets in the account

---

## C) Time (How Long Access Exists)
- short-lived credentials
- just-in-time access
- automatic expiration

Example:
- 1-hour admin role
- not permanent admin user

Rule:
> Permanent access is almost always unnecessary.

---

# 3) Least Privilege in Practice (Common Patterns)

---

## Human Access
- SSO + MFA
- role-based access (assume role)
- no long-lived access keys
- separate dev and prod roles
- break-glass access with approval

Humans should not be running around with admin forever.

---

## Application Access
- one role per service
- one policy per service
- no shared credentials
- workload identity (no static keys)
- restrict cloud + internal APIs

If multiple apps share one role, you’ve lost clarity.

---

## CI/CD Access
- deploy-only roles
- scoped permissions per pipeline
- permission boundaries
- short-lived tokens (OIDC)

Pipelines don’t need admin. They need exactly what deploys need.

---

# 4) Least Privilege Across Layers

---

## IAM Layer
- granular policies
- permission boundaries
- resource-based policies
- deny dangerous APIs explicitly

---

## Network Layer
- segmentation
- allowlists
- east-west traffic restrictions

Network least privilege limits who can even talk to whom.

---

## Data Layer
- DB users per service
- read vs write separation
- limited schemas/tables
- row-level security (where applicable)

---

## Secrets Layer
- apps read only their secrets
- no “global secret reader”
- short-lived secret access
- audit secret reads

---

# 5) Designing Least Privilege (Step-by-Step)

---

## Step 1: Start With No Access
Default deny.

## Step 2: Add Permissions Incrementally
- run the app
- see what breaks
- grant only what’s needed

Yes, this is annoying.
It’s also effective.

---

## Step 3: Observe and Refine
- review access logs
- remove unused permissions
- tighten over time

Least privilege is iterative, not a one-time event.

---

# 6) Common Challenges (Reality)

---

## “It’s Slowing Us Down”
Yes.
So do seatbelts.

Automation and templates reduce friction over time.

---

## Over-Permissioning “Just in Case”
This is how least privilege dies quietly.

---

## Third-Party Integrations
Often require broad permissions.
Mitigate with:
- scoped tokens
- network isolation
- monitoring

---

# 7) Measuring Least Privilege

---

## Signals of Poor Least Privilege
- `*:*` permissions
- shared roles across services
- unused permissions for months
- no audit of access usage
- production access from dev tools

---

## Improvement Techniques
- access analyzer tools
- permission usage reports
- periodic access reviews
- automated policy generation (carefully)

---

# 8) Common Mistakes

---

- confusing “works” with “secure”
- granting admin during debugging and forgetting to remove it
- not separating environments
- ignoring network and data layers
- no time-bound access
- assuming least privilege is “set once”

Worst mistake:
> “We’ll tighten permissions later.”

Later is when incidents happen.

---

## Interview-Ready Summary

> The Principle of Least Privilege requires granting identities only the minimum permissions necessary to perform their function, scoped to specific actions, resources, and time. It applies across IAM, network, data, and secrets layers and reduces blast radius, lateral movement, and incident impact. Practical implementation includes role-based access for humans, workload identity for applications, short-lived credentials, permission boundaries, environment isolation, and continuous review and refinement of permissions.

---

## Final Takeaway

Least privilege is not about distrust.
It’s about accepting reality:
- credentials leak
- bugs happen
- humans forget to clean up access

Design systems so when that happens, the damage is small.

Security isn’t about stopping all failures.
It’s about making failures boring.

::contentReference[oaicite:0]{index=0}
