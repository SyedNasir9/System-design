# Secure Pipelines: SAST, DAST, Vulnerability Scanning

---

## What Is a Secure CI/CD Pipeline?

A **secure pipeline** is a CI/CD pipeline that enforces security controls throughout the software delivery lifecycle, not as a last-minute scan before release.

Core idea:
> Security is a quality gate, not a ceremony.

A secure pipeline aims to:
- detect vulnerabilities early
- prevent insecure code/config from reaching production
- provide traceability for compliance and incident response
- reduce blast radius of mistakes

---

## Why Secure Pipelines Exist

Modern delivery is fast. Attackers are faster.

Without secure pipelines, teams rely on:
- manual reviews
- best intentions
- “we’ll fix it later”
- production monitoring as the first alarm

Secure pipelines exist to shift security left:
- earlier detection
- cheaper fixes
- fewer production incidents

---

## Security Signals in Pipelines (Three Big Buckets)

This node focuses on:
- **SAST** (Static Application Security Testing)
- **DAST** (Dynamic Application Security Testing)
- **Vulnerability Scanning** (dependencies, images, IaC)

They complement each other.
None replaces the others.

---

# 1) SAST (Static Application Security Testing)

---

## What Is SAST?

**SAST** analyzes code **without running it** to find security issues.

It typically detects:
- insecure coding patterns
- injection risks
- auth mistakes (basic ones)
- hardcoded secrets (sometimes)
- unsafe functions/flows

SAST runs early because it doesn’t need a deployed environment.

---

## Where SAST Fits in the Pipeline

Best placement:
- on pull requests (fast feedback)
- on merge to main (enforced gate)

You want SAST to fail fast on:
- high-confidence, high-severity findings

---

## Strengths of SAST

- early detection
- cheap to run
- works before deployment
- good for preventing repeat mistakes

---

## Weaknesses of SAST

- false positives
- limited runtime context
- often misses configuration/runtime issues

SAST is useful, but it’s not “security done”.

---

# 2) DAST (Dynamic Application Security Testing)

---

## What Is DAST?

**DAST** tests a running application from the outside, like an attacker would.

It typically detects:
- runtime injection issues
- auth/session problems
- misconfigurations
- exposed endpoints
- security headers issues

DAST requires a deployed environment.

---

## Where DAST Fits in the Pipeline

Best placement:
- after deploy to a staging / preview environment
- as a gated step before production promotion (for critical apps)

DAST is slower, so it usually runs:
- nightly
- on release branches
- on staging/prod candidates

---

## Strengths of DAST

- sees the system as it реально behaves
- catches runtime and config flaws
- less dependent on language/framework internals

---

## Weaknesses of DAST

- needs stable test environment
- can be noisy/flaky if environment changes
- limited code-level visibility
- can miss issues behind auth flows if not configured well

DAST is great at catching “it’s deployed wrong” issues.

---

# 3) Vulnerability Scanning

---

## What Is Vulnerability Scanning?

Vulnerability scanning checks known vulnerabilities in:
- dependencies (libraries)
- container images (OS packages + libs)
- infrastructure definitions (IaC)
- Kubernetes manifests (misconfigs)

This is usually based on CVE databases and policy rules.

---

## Where Vulnerability Scanning Fits in the Pipeline

Typical placements:
- dependency scan during build/test
- image scan after image build (before push or before deploy)
- IaC scan during PR validation
- cluster config scan periodically (continuous)

This works best when paired with:
- artifact immutability
- signed images
- SBOM generation

---

## Strengths of Vulnerability Scanning

- catches known bad versions fast
- easy to automate
- works well as a gate for critical vulns

---

## Weaknesses of Vulnerability Scanning

- doesn’t detect unknown vulns
- false positives (especially with “unreachable” code paths)
- noisy without good policies and exceptions

Raw scanning without governance becomes alert spam.

---

## How These Three Work Together

- **SAST** finds insecure patterns in code
- **DAST** finds runtime/system issues from outside
- **Vulnerability scanning** finds known vulnerable components and misconfigs

A secure pipeline uses all three because attackers don’t limit themselves to one category.

---

## Gating Strategy (What Should Block What)

You need tiers, otherwise your pipeline becomes unusable.

Common approach:
- Block merge on: high-confidence critical SAST findings, secrets, policy violations
- Block deploy on: critical/high vulns in prod artifacts, failed DAST on major issues
- Warn only on: low severity findings, noisy categories, informational signals

Security gates must be:
- strict where it matters
- flexible where it’s noisy
- consistently enforced

If devs learn “ignore security failures”, you’ve trained the pipeline to be useless.

---

## Secure Pipeline Design Patterns

### Shift-Left + Progressive Enforcement
- start with reporting only
- fix baseline issues
- then enforce gates gradually

### Policy-as-Code
- define security rules as code
- version and review them
- avoid manual “exceptions” with no traceability

### Build Once, Scan the Artifact
- scan the same immutable artifact you’ll deploy
- do not rebuild between scans and deploy

Security on one artifact and deployment of another is theater.

---

## Supply Chain Security Add-ons (High Value)

While not the focus, secure pipelines often include:
- SBOM generation (software bill of materials)
- signing artifacts (provenance)
- secret scanning (pre-commit + CI)
- least-privilege CI credentials

These reduce both accidental leaks and targeted attacks.

---

## Common Failure Modes

- scanning only before production (too late)
- blocking everything (pipeline unusable, teams bypass it)
- allowing too much (pipeline meaningless)
- no exception handling process (people turn off scanners)
- scanning but not fixing (security debt pile-up)
- deploying mutable artifacts (scans don’t match runtime)

Secure pipelines fail when security is treated as optional or punitive.

---

## Observability for Secure Pipelines

Track:
- vulnerability trend over time
- mean time to remediate (MTTR) per severity
- false positive rate
- gate failure reasons
- exceptions and their expiry

A secure pipeline is measurable. If it’s not measured, it’s vibes.

---

## Mental Model (Remember This)

- SAST = code-level risks early
- DAST = runtime risks after deploy
- Vuln scanning = known bad components and misconfigs
- Gates = risk management rules
- Discipline = what makes it real

Security is not one scan. It’s a delivery property.

---

## Interview-Ready Summary

> Secure CI/CD pipelines integrate SAST for code-level vulnerability detection, DAST for runtime security testing, and vulnerability scanning for dependencies, container images, and infrastructure, using policy-based gates and artifact immutability to prevent insecure builds from being promoted to production.

If someone says “we have a scanner so we’re secure,” they’re confusing tools with outcomes.

---

## Final Takeaway

Secure pipelines exist because:
- developers move fast
- systems are complex
- attacks are cheap

SAST, DAST, and vulnerability scanning cover different failure surfaces.

Do it right and you get:
- fewer incidents
- faster fixes
- safer releases

Do it lazily and you get:
- noise, bypasses, and false confidence

Security isn’t a checkbox.
It’s a pipeline habit.
