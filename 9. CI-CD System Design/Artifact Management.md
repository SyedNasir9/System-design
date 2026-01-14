# Artifact Management (CI/CD + DevOps)

---

## What Is Artifact Management?

**Artifact Management** is the practice of **storing, versioning, securing, and distributing build outputs** (artifacts) across environments.

An **artifact** is the output of a build step, such as:
- container images
- JAR/WAR files
- npm packages
- Python wheels
- binaries
- Helm charts
- Terraform modules (packaged)
- SBOMs and security reports

Core idea:
> Build once. Store it. Promote the same artifact through environments.

If you rebuild for every environment, you’re not doing CI/CD.
You’re doing repeated guessing with extra compute cost.

---

## Why Artifact Management Exists

Without proper artifact management, teams end up with:
- “works in staging, fails in prod” due to different builds
- untraceable deployments (“what version is running?”)
- slow pipelines (rebuild everything constantly)
- security gaps (no scanning, no provenance)
- no reliable rollback (artifact is gone or overwritten)

Artifact management creates:
- reproducibility
- traceability
- controlled promotion
- reliable rollbacks

---

## Artifact vs Source Code (Important Distinction)

| Source Code | Artifact |
|------------|----------|
| Human-edited | Machine-produced |
| Changes often | Immutable per version |
| In Git | In artifact registry |
| Needs build | Ready to deploy |

Git is not an artifact store.
Using Git as one is a cry for help.

---

## What “Good” Looks Like: Build Once, Promote

A mature flow:

1. Build artifact from commit `X`
2. Run tests + scans on that artifact
3. Store artifact immutably (tag + digest)
4. Promote the *same* artifact to:
   - dev → staging → prod

Promotion is a metadata change, not a rebuild.

---

## Types of Artifact Repositories

### Container Registries
Store container images + tags + digests.

Common examples:
- :contentReference[oaicite:0]{index=0}
- :contentReference[oaicite:1]{index=1}
- :contentReference[oaicite:2]{index=2}

---

### Package Repositories
Store language packages.

Examples:
- Maven/Nexus-style repos
- npm registries
- PyPI-style repos

---

### Generic Artifact Stores
Store any file: binaries, build outputs, archives.

Examples:
- S3-backed storage
- artifact repositories (Nexus/Artifactory style)

---

### Helm/Chart Repos
Store deployable Helm charts.

Helm artifacts often pair with image registries:
- chart points to image digest/tag
- chart versioned independently

---

## Versioning Strategy

### Semantic Versioning (SemVer)
- `MAJOR.MINOR.PATCH`
- Great for libraries, stable APIs

### Build/Commit-Based Versioning
- `app-<git-sha>`
- Great for internal services and traceability

Best practice:
- tag with git SHA
- also publish a human-friendly version
- always keep immutable references (digests)

If your “latest” tag is used in production, you enjoy chaos. That’s a lifestyle choice.

---

## Immutability (Non-Negotiable)

Artifacts should be **immutable**:
- same version = same bits
- no overwriting
- no silent changes

Immutability enables:
- reliable rollback
- auditability
- supply-chain integrity

Mutable artifacts are how “hotfixes” become invisible incidents.

---

## Artifact Metadata and Traceability

Every artifact should be traceable to:
- git commit SHA
- build ID
- pipeline run link
- build time
- dependency versions
- SBOM reference

This is the minimum for:
- incident response
- compliance
- security investigations

When an alert says “vulnerable version deployed,” you need to know exactly what that means.

---

## Artifact Security (Supply Chain Basics)

Artifacts are part of the attack surface.

Minimum security controls:
- vulnerability scanning (images, packages)
- signing (provenance)
- SBOM generation
- access control (RBAC)
- retention policies
- immutable tags/digests in prod

If artifacts aren’t scanned and signed, you’re trusting the internet.
And the internet is not your friend.

---

## Promotion Models

### 1) Environment-Based Repos
- dev repo → staging repo → prod repo

Pros:
- clean isolation

Cons:
- duplication of artifacts

---

### 2) Single Repo + Promotion Tags
- one repo
- promote by adding tags/metadata:
  - `candidate`, `staging`, `prod`

Pros:
- single source of truth
- less duplication

Cons:
- must enforce immutability and policy strongly

---

### 3) GitOps Promotion (Recommended Pattern)
- artifact immutable in registry
- Git stores the desired deployed version (image digest/tag)
- promotion happens by PR

Pros:
- auditable
- rollback via Git revert
- aligns with system design discipline

---

## Caching and Efficiency

Artifact management supports pipeline efficiency:
- dependency caching (npm, Maven, pip)
- build caching (layer caches)
- avoid rebuilding identical commits

But beware:
- cache poisoning
- stale dependencies
- non-deterministic builds

Caching is great until it hides reality.

---

## Common Failure Modes

- rebuilding per environment (non-reproducible releases)
- overwriting tags (mutable prod)
- no retention rules (storage explosion)
- no metadata (can’t trace incidents)
- no signing/scanning (supply chain blind spot)
- “latest” deployed everywhere (you enjoy surprise)

Most artifact problems show up during incidents. Convenient.

---

## Mental Model (Remember This)

- Source code is input
- Artifact is output
- Registry is source of deploy truth
- Promotion is moving the same artifact forward
- Rollback is selecting an older artifact

Build once. Promote safely. Roll back instantly.

---

## Interview-Ready Summary

> Artifact management ensures build outputs are versioned, immutable, traceable, and securely stored in registries so the same artifact can be tested and promoted through environments, enabling reproducible deployments, fast rollbacks, and better supply-chain security.

If someone says “we just rebuild in prod to be safe,” they’re describing a new kind of unsafe.

---

## Final Takeaway

Artifact management is what turns CI/CD from:
- “pipeline runs”
into:
- “releases we can trust”

It gives you:
- reproducibility
- auditability
- rollbackability
- supply chain control

Without it, your deployment process is basically ritual + hope.
