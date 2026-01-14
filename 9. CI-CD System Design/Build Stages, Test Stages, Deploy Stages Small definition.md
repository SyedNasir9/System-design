# CI/CD Stages: Build, Test, Deploy

---

## What Are Pipeline Stages?

A CI/CD pipeline is split into **stages** to control:
- quality gates
- speed vs confidence
- promotion across environments
- rollback safety

The three core stages are:
- **Build**: produce an artifact
- **Test**: validate the artifact
- **Deploy**: release the artifact

Core principle:
> Build once, test thoroughly, deploy the same artifact.

If you rebuild per stage or per environment, you lose reproducibility and gain mystery bugs.

---

## Why Stages Matter (System Design + DevOps)

Stages exist to prevent:
- broken code reaching production
- untraceable deployments
- slow and fragile pipelines
- teams deploying “whatever was built last”

Stages create:
- separation of concerns
- controlled promotion
- fast feedback loops
- auditability and compliance

Pipelines are production systems. Treat them like it.

---

# 1) Build Stage

---

## Goal of Build Stage

The build stage transforms source code into a **deployable artifact**.

Artifacts can be:
- container image
- binary
- JAR/WAR
- npm package
- Helm chart

Outputs must be:
- versioned (git SHA/tag)
- immutable
- stored in an artifact registry

---

## Typical Build Stage Steps

- checkout code
- resolve dependencies
- compile / bundle
- build container image
- generate SBOM (optional but smart)
- push artifact to registry

Common extras:
- linting (fast checks)
- formatting validation

---

## Build Stage Best Practices

- make builds reproducible (pin dependencies)
- use caching safely (build cache, layer cache)
- tag artifacts with git SHA
- avoid secrets in build context
- keep build fast (shift heavy tests later)

Build stage is about producing a reliable artifact, not proving it’s correct.

---

## Build Stage Failure Modes

- “latest” tag overwrite
- building different artifacts for different envs
- non-deterministic builds (floating dependencies)
- slow builds from no caching
- secrets baked into images

If your artifact cannot be traced back to a commit, you’re deploying vibes.

---

# 2) Test Stage

---

## Goal of Test Stage

Test stage validates the artifact and prevents regressions.

Key idea:
> Tests should target the same artifact you’ll deploy.

Testing source code is useful.
Testing the artifact is essential.

---

## Test Pyramid (Practical View)

### Unit Tests
- fast
- high coverage
- validate logic in isolation

### Integration Tests
- test service with dependencies (DB, cache)
- catch API/contract breaks

### End-to-End (E2E)
- test user workflows
- expensive and slower
- high confidence but limited coverage

A good pipeline runs:
- unit tests always
- integration tests where relevant
- E2E on main or nightly (depending on cost)

---

## Security Tests as Part of “Test”

Security is not a separate universe. It’s part of quality.

Common test additions:
- SAST (static analysis)
- dependency scanning
- container image scanning
- IaC scanning
- DAST (where applicable)

Don’t block all merges on noisy scanners.
Do block releases on confirmed criticals.

---

## Test Stage Best Practices

- parallelize tests
- fail fast (quick checks first)
- keep flaky tests quarantined
- measure test duration and stability
- use test environments that resemble prod

Flaky tests destroy trust in the pipeline faster than any bug.

---

## Test Stage Failure Modes

- running tests only in dev environment
- skipping integration tests “for speed”
- relying on manual QA as the gate
- treating security scans as optional
- ignoring flaky tests

A fast pipeline that ships bugs is just automation of failure.

---

# 3) Deploy Stage

---

## Goal of Deploy Stage

Deploy stage takes a verified artifact and releases it into an environment.

Key idea:
> Deploy should be controlled, observable, and reversible.

Deploy is where risk becomes real.

---

## Common Deploy Stage Steps

- select artifact (by digest/tag/SHA)
- update deployment manifests (GitOps) or run deployment tool
- rollout strategy execution (rolling/blue-green/canary)
- post-deploy verification (smoke tests)
- monitoring and alert checks

---

## Deployment Strategies (High Level)

- **Rolling updates**: gradual replacement
- **Blue-green**: switch traffic between two environments
- **Canary**: route small % traffic to new version, then expand

Deploy stage should choose strategy based on:
- risk tolerance
- observability maturity
- traffic patterns
- rollback requirements

---

## Deploy Stage Best Practices

- use immutable image digests in production
- gate deploys with approvals or policies (as needed)
- validate readiness/liveness probes
- include rollback automation
- integrate with monitoring/SLOs

If you can’t roll back fast, you’re not deploying. You’re gambling.

---

## Deploy Stage Failure Modes

- deploying untested artifacts
- no health checks (deploy "succeeds" while app is dead)
- no rollback plan
- config drift between environments
- database migrations breaking old versions mid-rollout

Most “deployment issues” are actually lack of deployment design.

---

## Putting It Together: A Mature Pipeline Flow

A robust flow looks like:

1. **Build**
   - build artifact once
   - push to registry
2. **Test**
   - run unit + integration tests
   - scan artifact (security)
3. **Deploy**
   - promote same artifact
   - progressive rollout
   - verify + observe
   - rollback if needed

Promotion should be:
- explicit
- traceable
- repeatable

---

## CI vs CD (Where These Stages Live)

- **CI** typically covers Build + Test on PRs
- **CD** covers Deploy on merge/release

You can run deploy to:
- ephemeral preview environments for PRs
- dev automatically on merge
- staging on promotion
- prod on approval + SLO gates

Maturity is controlling risk, not pushing buttons faster.

---

## Mental Model (Remember This)

- Build creates the thing
- Test proves the thing
- Deploy releases the thing
- Promotion moves the same thing forward

If you rebuild, you changed the thing.

---

## Interview-Ready Summary

> CI/CD pipelines are structured into build, test, and deploy stages to produce immutable artifacts, validate them with automated quality and security checks, and release them via controlled rollout strategies with observability and rollback support, enabling reproducible and reliable deployments.

If someone says “we test in prod,” they’re confusing bravery with negligence.

---

## Final Takeaway

Stages exist because software delivery is risk management.

- Build for reproducibility
- Test for confidence
- Deploy for control

A pipeline that combines all three into one script is not “simple”.
It’s a single point of failure with extra steps.
