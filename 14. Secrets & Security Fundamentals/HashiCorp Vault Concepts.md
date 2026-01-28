# HashiCorp Vault Concepts

---

## What This Means

**HashiCorp Vault** is a centralized system for **securely storing, generating, accessing, rotating, and auditing secrets**.

It’s not just “a place to keep passwords”.
It’s a **secret lifecycle manager**.

Core idea:
> Don’t hardcode secrets. Don’t keep them forever. Don’t trust humans with them.

---

## Why Vault Exists

Traditional secret handling fails because:
- secrets get committed to Git
- long-lived credentials never rotate
- multiple systems copy the same secret
- no audit trail of who accessed what
- revocation is slow or impossible

Vault exists to:
- issue **short-lived secrets**
- centralize access control
- automate rotation and revocation
- provide strong auditing
- reduce blast radius when secrets leak

---

# 1) Vault Core Components

---

## Vault Server

The Vault server:
- stores encrypted secrets
- enforces access policies
- authenticates clients
- issues tokens
- logs access (audit logs)

Vault does **not** trust the network.
Every request must authenticate.

---

## Storage Backend

Vault needs storage for encrypted data:
- Consul
- integrated storage (Raft)
- cloud storage backends (depending on setup)

Important:
> Vault does not trust its storage backend. All data is encrypted before storage.

---

## Seal and Unseal

### Sealed State
- Vault starts **sealed**
- encryption keys are not in memory
- no secrets can be accessed

### Unsealed State
- master key reconstructed
- Vault can decrypt data
- services can authenticate and fetch secrets

Unsealing can be:
- manual (unseal keys)
- automated (cloud KMS, HSM)

Rule:
> A sealed Vault is safe but useless. An unsealed Vault must be protected.

---

# 2) Authentication (Auth Methods)

---

## What Auth Methods Do

Auth methods verify **who or what** is requesting secrets and issue a **Vault token**.

Common auth methods:
- Kubernetes auth (pods authenticate via service accounts)
- AppRole (machine-to-machine)
- OIDC / SSO (humans)
- AWS/GCP/Azure IAM auth
- TLS certificates

Auth ≠ access.
Auth proves identity. Policies decide access.

---

## Tokens

After authentication, Vault issues a **token**:
- short-lived (TTL)
- renewable or non-renewable
- tied to policies

Tokens can be:
- periodic (renew automatically)
- orphan (not tied to parent token)
- limited-scope

Rule:
> Tokens should expire faster than human memory.

---

# 3) Authorization (Policies)

---

## Policies

Policies define:
- what paths can be accessed
- what operations are allowed (read/write/list/delete)
- optional conditions

Example (conceptual):
- allow read on `secret/data/app/*`
- deny everything else

Important:
- default is deny
- explicit deny always wins

Vault uses **path-based access control**.

---

## Least Privilege

Best practice:
- one app = one role
- one role = minimal policy
- separate policies per environment (dev/prod)

If an app can read all secrets, you’ve already lost.

---

# 4) Secrets Engines (Where the Magic Happens)

---

## What a Secrets Engine Is

A secrets engine is a plugin that:
- stores secrets
- or generates secrets dynamically

Mounted at a path:
- `secret/`
- `kv/`
- `database/`
- `pki/`

Different engines, different behavior.

---

## Static Secrets (KV Engine)

- key-value storage
- encrypted at rest
- versioned (KV v2)

Use for:
- API keys
- config secrets
- bootstrap credentials

Downside:
> Static secrets must be rotated manually or via automation.

---

## Dynamic Secrets (The Killer Feature)

Vault can **generate secrets on demand**.

Examples:
- database credentials
- cloud IAM credentials
- message queue users

How it works:
1. app requests secret
2. Vault creates a real credential
3. credential has TTL
4. Vault revokes it automatically

Benefits:
- no shared credentials
- automatic rotation
- short blast radius

This is why people tolerate Vault’s complexity.

---

# 5) Leases, TTLs, and Revocation

---

## Leases

Most Vault-issued secrets have:
- a **lease**
- a **TTL**

When TTL expires:
- secret becomes invalid
- Vault revokes it (if dynamic)

---

## Renewal

Some tokens/secrets can be:
- renewed before expiry
- extended if still needed

Apps must handle:
- renewal
- re-authentication
- secret refresh

Design rule:
> Apps must survive secret rotation without restart.

---

## Revocation

Vault can:
- revoke a single secret
- revoke all secrets issued by a role
- revoke all secrets tied to a token

This is how you respond to compromise **fast**.

---

# 6) Encryption as a Service (Transit Engine)

---

## Transit Engine

Vault can encrypt/decrypt data **without storing it**.

Use cases:
- encrypt sensitive fields before DB write
- tokenization
- application-level encryption

Vault never sees plaintext at rest.

Transit turns Vault into a cryptographic service, not just a secret store.

---

# 7) PKI and Certificates

---

## PKI Engine

Vault can:
- act as a certificate authority
- issue TLS certificates
- rotate certs automatically
- revoke compromised certs

Great for:
- mTLS between services
- internal service identity
- short-lived certs

This avoids:
- long-lived certs
- manual certificate hell

---

# 8) Vault + Kubernetes (Conceptual Flow)

---

## Typical Runtime Flow

1. pod starts with Kubernetes ServiceAccount
2. Vault verifies pod identity (K8s auth)
3. Vault issues token
4. secrets are injected (sidecar, CSI, API)
5. app reads secrets from file or memory
6. secrets rotate automatically

Patterns:
- Vault Agent sidecar
- CSI Secrets Store
- app-native Vault client

---

# 9) Auditing and Observability

---

## Audit Logs

Vault can log:
- every auth attempt
- every secret read/write
- token creation and revocation

Audit logs are:
- append-only
- critical for incident response
- should be shipped to SIEM

If audit logs are off, Vault is blind.

---

# 10) Operational Concepts (Reality Check)

---

## High Availability
- multiple Vault nodes
- leader election
- shared storage backend

## Backup
- backup storage backend
- protect unseal keys
- test restore

## Upgrades
- careful rolling upgrades
- version compatibility matters

Vault is infrastructure. Treat it like one.

---

# 11) Common Mistakes

---

- using Vault like a dumb key-value store
- long-lived root tokens
- over-broad policies
- storing secrets but not rotating them
- no audit logs
- manual unseal with keys pasted in chat
- running Vault without HA

Worst mistake:
> Adding Vault and continuing to hardcode secrets anyway.

---

## Interview-Ready Summary

> HashiCorp Vault is a centralized secret management system that secures, distributes, rotates, and audits access to sensitive data. It authenticates clients using multiple auth methods, authorizes access through path-based policies, and supports both static secrets via KV storage and dynamic secrets with leases and automatic revocation. Core concepts include sealing/unsealing, tokens with TTLs, secret engines, and audit logging. Vault is especially valuable for issuing short-lived credentials, enabling rapid revocation, and reducing blast radius in modern cloud-native and Kubernetes environments.

---

## Final Takeaway

Vault is powerful because it assumes:
- secrets will leak
- systems will fail
- humans will make mistakes

So it designs around:
- short-lived access
- revocation
- auditability
- automation

Vault doesn’t remove risk.
It makes risk **manageable and reversible**.
