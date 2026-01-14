# Infrastructure-as-Code Integration (CI/CD + DevOps)

---

## What Is Infrastructure-as-Code (IaC) Integration?

**IaC integration** means embedding infrastructure changes (networks, compute, IAM, Kubernetes resources, etc.) into the same disciplined delivery lifecycle as application code.

In practice:
- infra definitions live as code (Git)
- pipelines validate, plan, and apply changes
- changes are reviewed and audited
- environments become reproducible

Core idea:
> Infrastructure changes should be as reviewable and repeatable as software changes.

If your infra is “whoever has AWS console access today,” you don’t have infrastructure. You have accidents.

---

## Why IaC Integration Matters in System Design

Modern systems fail because of infrastructure drift, not just code bugs.

IaC integration enables:
- predictable environments
- safer changes via review gates
- traceability for audits/incidents
- fast rebuild for disaster recovery
- consistent security posture (IAM, networking)

Infrastructure is part of the system design, not a separate universe.

---

## IaC Tools (Common Stack)

Typical split:

### Provisioning / Cloud Resources
- Terraform, CloudFormation, Pulumi

### Configuration Management
- Ansible, Puppet, Chef

### Kubernetes Resources
- manifests, Helm, Kustomize
- GitOps controllers (Argo CD / Flux)

This isn’t “tool choice.”
It’s “layering correctly.”

---

## Integration Models (How Teams Do It)

---

### 1) Single Repo (Monorepo) Integration
App + IaC together.

Pros:
- one PR can change infra + app together
- easy coordination

Cons:
- tighter coupling
- can increase blast radius if poorly gated

Best when:
- you have mature review + policy controls

---

### 2) Separate Repos (Infra Repo + App Repo)
Infra and app changes flow independently.

Pros:
- stronger separation of concerns
- cleaner access control

Cons:
- cross-repo coordination friction
- harder to tie infra change to app release

Best when:
- you have strict compliance
- platform team owns infra

---

### 3) Platform Modules + App Consumption
Platform team provides Terraform/Helm modules. App teams consume.

Pros:
- standardization
- reduces reinvention
- guardrails by design

Cons:
- module versioning and governance needed

This is the “grown-up” model if you have multiple teams.

---

## CI/CD Pipeline Flow for IaC

A safe, common flow:

1. **Validate**
   - format/lint
   - static checks
   - policy checks
2. **Plan**
   - generate change plan (diff)
   - attach to PR for review
3. **Review + Approve**
   - human approval (for sensitive envs)
   - automated policy gate
4. **Apply**
   - apply in controlled order:
     dev → staging → prod
5. **Verify**
   - smoke checks
   - drift detection
   - monitor alerts

Key rule:
> Don’t apply in prod from a laptop.

---

## Environments and State Management

IaC needs state. State is both power and risk.

### Terraform State Basics
- state must be centralized and locked
- access must be controlled tightly
- state should be per environment

Common patterns:
- separate state per env (dev/stage/prod)
- workspace-based separation (with care)

If state is corrupted or shared incorrectly, your pipeline becomes a demolition tool.

---

## Secrets and IaC

IaC frequently touches sensitive material:
- IAM credentials
- database passwords
- TLS certs

Best practice:
- never store secrets in Git
- use secret managers and inject at runtime
- encrypt state (because state can contain secrets)

IaC without secrets discipline is a breach waiting for timing.

---

## Policy-as-Code (Guardrails)

IaC integration should include policy enforcement:
- naming standards
- allowed regions
- security baselines (no public S3, no wide-open SGs)
- required tags/labels

Examples of policy categories:
- preventative (block bad changes)
- detective (alert on drift/violations)

Policy-as-code turns “best practices” into actual rules.

---

## Drift Detection (Day-2 Reality)

Even with IaC, drift happens:
- emergency console changes
- “temporary” edits
- unmanaged resources created manually

Drift detection should:
- detect mismatch between desired vs actual
- alert or auto-remediate depending on risk

If drift is allowed silently, IaC becomes documentation, not control.

---

## Ordering and Dependencies (Big System Design Point)

Infra changes often require ordering:
- network first
- IAM policies before workloads
- databases before apps
- DNS and certificates before exposing endpoints

Bad ordering causes:
- broken deploys
- partial rollouts
- downtime

Mature pipelines manage infra and app deployment as a coordinated graph, not a linear script.

---

## Rollback and Recovery

Infra rollback is not always clean.

Examples:
- deleting resources can cause data loss
- IAM changes can lock you out
- network changes can isolate services

Best approach:
- treat infra like product changes
- plan carefully, apply gradually
- use feature flags / toggles where possible
- prefer reversible changes (expand/contract patterns)

Rollback is a strategy, not a button.

---

## Common Failure Modes

- applying Terraform without locking
- using one shared state for all envs
- skipping plan review
- no policy checks (security regressions)
- manual changes in console causing drift
- pipelines with overly broad credentials

IaC pipelines should be constrained. The pipeline should not have god-mode access by default.

---

## Mental Model (Remember This)

- IaC defines desired infrastructure state
- CI validates and plans changes
- CD applies changes safely with gates
- Policies prevent unsafe infrastructure
- Drift detection protects day-2 operations

IaC integration is how infra becomes a controlled system, not a manual craft.

---

## Interview-Ready Summary

> Infrastructure-as-Code integration embeds provisioning and configuration changes into CI/CD pipelines using validation, planning, policy gates, controlled applies, and drift detection, enabling reproducible environments, secure change management, and reliable operations across dev, staging, and production.

If someone says “infra is separate, we’ll do it later,” they’re describing future outages.

---

## Final Takeaway

IaC integration is not just automation.
It is **operational governance**.

It gives you:
- repeatability
- auditability
- safer changes
- faster recovery

Without it:
- environments drift
- fixes are manual
- outages are harder to resolve
- security becomes inconsistent

Infrastructure is part of your product.
So deliver it like one.
