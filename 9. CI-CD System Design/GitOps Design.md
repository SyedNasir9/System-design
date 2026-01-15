# GitOps Design

---

## What Is GitOps?

**GitOps** is an operational model where **Git is the single source of truth for desired system state**, and an automated agent continuously reconciles the running environment to match what’s in Git.

In simple terms:
> If it’s not in Git, it doesn’t exist.

GitOps replaces:
- manual kubectl apply
- console clicking
- undocumented hotfixes

with:
- versioned declarations
- pull requests
- automated reconciliation

---

## Why GitOps Exists

Modern systems are too complex for manual operations.

Without GitOps, you get:
- config drift
- untraceable changes
- inconsistent environments
- painful rollbacks
- “who changed this?” incidents

GitOps exists to make operations:
- auditable
- repeatable
- reversible
- automated

It’s basically CI/CD but with discipline and fewer surprises.

---

## Core GitOps Principles

A proper GitOps design usually follows these principles:

- **Declarative desired state** (YAML/Helm/Kustomize/Terraform)
- **Version control** as the source of truth
- **Reconciliation loop** (continuous enforcement)
- **Pull-based deployment** (agent pulls from Git, not CI pushing into cluster)
- **Auditability** via PR history

If you’re still running “deploy scripts,” you’re doing CD, not GitOps.

---

## Pull-Based vs Push-Based Deployments

This is the key design difference.

### Push-Based (Traditional CD)
CI pipeline pushes changes into the cluster:
- kubectl apply
- helm upgrade
- terraform apply from CI

Pros:
- simple mental model

Cons:
- CI needs cluster credentials (high risk)
- harder to ensure drift correction
- less clean audit trail for runtime state

---

### Pull-Based (GitOps)
An in-cluster agent pulls desired state from Git and applies it.

Pros:
- cluster credentials stay inside the cluster
- drift correction is automatic
- Git is authoritative

Cons:
- requires reconciliation tooling and discipline

GitOps is essentially “least privilege CD”.

---

## GitOps Architecture (High Level)

Typical setup:

1. Developer opens PR to change desired state (manifests/charts)
2. CI validates:
   - linting
   - policy checks
   - security scans
3. Merge happens
4. GitOps agent detects change
5. Agent syncs cluster state to match Git
6. Monitoring verifies rollout health

The cluster becomes self-managing, assuming Git is correct.

---

## GitOps Controllers

Common controllers:

### :contentReference[oaicite:0]{index=0}
- popular in Kubernetes
- UI and sync management
- supports Helm/Kustomize/manifests

### :contentReference[oaicite:1]{index=1}
- GitOps-native design
- strong automation and Helm support
- good for multi-tenant patterns

Controller choice matters less than the design discipline.

---

## Repository Design Patterns

---

### 1) Single Repo (App + Manifests Together)
Pros:
- simple linkage between code and deployment
- easy traceability

Cons:
- access control can get messy
- many teams collide in one repo

---

### 2) Separate Repos (App Repo + GitOps Repo)
App repo builds artifact.
GitOps repo controls deployment.

Pros:
- separation of concerns
- strong access controls
- clean environment-specific overlays

Cons:
- extra coordination step

This is common in mature teams.

---

### 3) Monorepo with Environment Folders
Example structure:
- `/apps/service-a`
- `/apps/service-b`
- `/envs/dev`
- `/envs/prod`

Pros:
- centralized control
- consistent patterns

Cons:
- can grow huge
- requires repo governance

---

## Environment Management (Overlays)

GitOps often uses:
- Helm values files per env
- Kustomize overlays per env
- separate branches per env (less ideal)

Preferred approach:
- same base manifests
- env-specific overlays in Git

This prevents:
- “prod is special” configuration drift

---

## Promotion Strategies in GitOps

Promotion means updating Git to point to a new artifact.

Common methods:
- update image tag/digest in env overlay
- PR-based promotion (dev → staging → prod)
- automated promotion gated by SLOs

Best practice:
- deploy by **image digest** in prod (immutable)
- tags in dev are okay, but risky in prod

---

## Drift Detection and Self-Healing

GitOps controllers continuously reconcile:
- if someone changes something manually, it is reverted
- if resources drift, controller fixes them

This is the big win:
> GitOps turns drift into a temporary glitch, not a permanent state.

But it also means:
- manual hotfixes are overwritten
- emergency procedures must be designed properly

---

## Security Model (Why GitOps Is Safer)

GitOps improves security by:
- reducing direct human access to clusters
- keeping credentials out of CI
- enforcing changes through PR workflows
- enabling policy-as-code checks before merge

Still required:
- strong RBAC
- secret management (don’t store secrets in Git)
- signed commits/artifacts where needed

GitOps doesn’t secure your cluster automatically.  
It just removes a major attack surface: people.

---

## Common GitOps Failure Modes

- treating GitOps as “auto-deploy on merge” with no gates
- storing secrets in Git (classic)
- no policy checks (bad YAML reaches prod)
- too many manual overrides (fighting reconciliation)
- poor repo structure (nobody understands what to change)

GitOps amplifies both good and bad practices.

---

## Observability and Operations in GitOps

You must monitor:
- sync status (in-sync / out-of-sync)
- drift events
- deployment health
- rollback frequency
- time-to-sync and reconciliation latency

GitOps failure is subtle:
- cluster is “running”
- desired state isn’t applied
- you discover it during an incident

---

## Mental Model (Remember This)

- Git is desired state
- Controller is the reconciler
- Cluster is the runtime
- PRs are change control
- Rollback is Git revert

GitOps turns operations into version control workflows.

---

## Interview-Ready Summary

> GitOps is a deployment and operations model where declarative desired state is stored in Git and continuously reconciled by in-cluster controllers like Argo CD or Flux, enabling auditable, reproducible, and self-healing deployments with pull-based security and PR-driven change control.

If someone says “GitOps is just CI/CD,” they’re missing the reconciliation and security model.

---

## Final Takeaway

GitOps is how you make “infrastructure as code” actually enforced.

It gives you:
- drift control
- clean audit trails
- safer deployments
- easy rollbacks

But it demands:
- disciplined repo structure
- policy gates
- proper secret handling
- good observability

GitOps isn’t magic.
It’s just what happens when you stop letting production be a free-for-all.
