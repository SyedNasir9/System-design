# Secret Management (Vault, Kubernetes Secrets, KMS)

---

## What This Means

**Secret management** is how you store, access, rotate, and audit sensitive values like:
- API keys
- database passwords
- TLS private keys
- tokens and certificates
- encryption keys

Tools you mentioned:
- **Vault**: dedicated secrets platform (dynamic secrets, leasing, auth, audit)
- **Kubernetes Secrets**: cluster-native secret objects (needs hardening)
- **KMS**: managed key service for encryption keys (encrypt/decrypt, envelope encryption)

Core idea:
> Secrets don’t leak because you’re unlucky. They leak because humans love convenience.

---

## Why This Exists

Without proper secret management you get:
- secrets in GitHub repos
- secrets in Docker images
- secrets in CI logs
- long-lived credentials never rotated
- lateral movement after one credential leak
- no audit trail of who accessed what

Secret management exists to:
- reduce blast radius of leaks
- enable short-lived access
- enforce least privilege
- automate rotation
- provide auditability
- integrate with CI/CD and runtime safely

---

# 1) What Counts as a Secret (and What Doesn’t)

---

## Secrets
- passwords, API tokens, private keys
- session signing keys
- database connection strings (usually)
- client secrets, OAuth secrets

## Not Secrets (But Still Important)
- config values (feature flags, URLs)
- public certificates
- non-sensitive environment settings

Don’t shove everything into “secret storage” and call it security. Classify it.

---

# 2) Kubernetes Secrets (What They Are and What They Aren’t)

---

## What K8s Secrets Provide

Kubernetes `Secret` is an object that stores data (base64-encoded) and can be:
- mounted into pods as files
- injected as environment variables

Important reality:
> Base64 is not encryption. It’s just “ASCII cosplay”.

By default, K8s Secrets are stored in **etcd**. You must secure etcd + access controls.

---

## Hardening Kubernetes Secrets (Minimum)

Do these or accept the consequences:
- **RBAC**: restrict who can read secrets
- **Encrypt secrets at rest in etcd** (using encryption providers)
- restrict API server access
- **namespace isolation**
- prevent secrets from landing in logs
- prefer mounting as **files** over env vars (env vars can leak via process dumps)
- scan manifests for secrets (pre-commit, CI)

K8s Secrets are okay for many orgs, but only when hardened.

---

# 3) KMS (Key Management Service) Basics

---

## What KMS Is

A **KMS** manages encryption keys and performs cryptographic operations:
- encrypt/decrypt
- generate data keys
- key rotation policies
- access control and auditing for key usage

KMS is not “a secrets store”.
KMS is:
> A controlled key vault for encryption operations.

---

## Envelope Encryption (How KMS Is Used)

Typical pattern:
1. KMS protects a **master key**
2. You generate a **data key** (DEK) via KMS
3. You encrypt the secret/data with the DEK locally
4. Store encrypted data anywhere (DB/object store)
5. DEK is encrypted (wrapped) by KMS master key

Benefits:
- limits direct exposure of master keys
- central auditing of decrypt usage
- easy rotation at the master-key layer

This is also how many managed “secret managers” work internally.

---

# 4) Vault Basics (Why People Use It)

---

## What Vault Provides

Vault is a platform for:
- **static secrets** (store and retrieve)
- **dynamic secrets** (generate on demand)
- **leases + TTL** (secrets expire automatically)
- **auth methods** (K8s auth, OIDC, AppRole, etc.)
- **audit logs**
- **PKI** (issue certificates)
- **encryption as a service** (transit engine)

Vault’s killer feature:
> Don’t store credentials. Generate them, expire them, move on.

---

## Dynamic Secrets (The Big Win)

Example:
- app asks Vault for DB creds
- Vault creates a DB user with limited permissions
- creds expire after 1 hour
- Vault revokes them automatically

So even if creds leak:
- the window is limited
- revocation is possible
- auditing exists

---

# 5) Workflows (How These Are Used in Real Systems)

---

## Workflow A: Vault + Kubernetes (Runtime Secrets)

**Goal:** pods get secrets without hardcoding them.

Flow:
1. pod uses Kubernetes ServiceAccount
2. Vault authenticates pod via K8s auth
3. Vault issues a token with policies
4. pod retrieves secrets (or sidecar injects them)
5. secrets rotate/expire based on TTL

Typical patterns:
- **Vault Agent sidecar** injects secrets as files
- **CSI driver** mounts secrets into pods
- app reads secrets from files, reloads when rotated

This is clean because your app never needs a long-lived secret to get secrets.

---

## Workflow B: Kubernetes Secrets + KMS (Safer K8s Secrets)

**Goal:** keep using K8s Secrets but encrypted properly.

Flow:
1. enable encryption at rest for secrets in etcd
2. encryption provider uses KMS key
3. RBAC restricts access
4. secrets mounted into pods

This reduces risk for “someone stole etcd backup”.

---

## Workflow C: CI/CD Secrets

**Goal:** pipelines deploy without leaking secrets.

Good flow:
1. CI gets identity via OIDC (short-lived)
2. CI assumes role / gets Vault token
3. pipeline fetches deploy-time secrets
4. secrets never stored in repo, never printed in logs
5. revoke/expire after job

Avoid:
- storing long-lived cloud keys in CI variables forever

---

# 6) Rotation and Revocation

---

## Rotation Types

- **Scheduled rotation** (every N days)
- **event-based rotation** (after incident)
- **lease-based rotation** (automatic expiry, fetch new secrets)

Vault makes rotation easier with dynamic secrets.
K8s Secrets usually require external automation (operators, pipelines).

---

## Revocation

You want to be able to:
- kill a secret quickly
- invalidate tokens
- rotate keys with minimal downtime

Design your apps to reload secrets without restart when possible (file watch, SIGHUP, hot reload).

---

# 7) Common Mistakes

---

- secrets in Git (even once, history exists)
- secrets baked into Docker images
- secrets passed via env vars and logged accidentally
- over-broad access (everyone can read all secrets)
- no rotation policy
- “one secret to rule them all” used across environments
- storing secrets in plaintext in etcd (no encryption at rest)
- using KMS as a secrets store (it’s not, by itself)

Worst mistake:
> Treating secrets as config because you’re tired.

Security incidents love tired people.

---

# 8) Practical Guidance (When to Use What)

---

## Use Kubernetes Secrets When
- you have strong RBAC
- etcd encryption at rest is enabled (preferably backed by KMS)
- you need simple, cluster-local secrets
- rotation needs are basic

## Use Vault When
- you need dynamic secrets, leasing, revocation
- multiple platforms/clusters need shared secret control
- you want strong audit trails
- you need PKI/cert issuance at scale

## Use KMS When
- you need a root-of-trust for encryption keys
- you want envelope encryption for stored data/secrets
- you need centralized key usage auditing and access control

Most mature setups combine them:
- KMS for key protection
- Vault for secret lifecycle
- K8s Secrets for delivery, or Vault injection/CSI instead

---

## Interview-Ready Summary

> Secret management is the discipline of securely storing, distributing, rotating, and auditing sensitive credentials. Kubernetes Secrets provide native secret delivery to pods but require hardening via RBAC and encryption at rest (often using KMS). KMS manages encryption keys and enables envelope encryption as a root-of-trust for encrypting secrets and data. Vault provides a full secret lifecycle platform with strong authentication, auditing, and dynamic, short-lived secrets with leases and revocation. Production-grade designs favor short-lived credentials, least privilege access, and automated rotation and revocation.

---

## Final Takeaway

- **K8s Secrets**: convenient delivery mechanism, not automatically secure.
- **KMS**: protects keys and enables strong encryption patterns.
- **Vault**: the grown-up option for secret lifecycle, especially dynamic and short-lived secrets.

If secrets are scattered across repos, CI variables, and random dashboards, you don’t have secret management.
You have secret gambling.
