# KMS + SSM (Key Management Service and Parameter Store)

---

## What This Means

**KMS + SSM** is a common cloud-native pattern for **secure configuration and secret management** without running extra infrastructure.

- **KMS (Key Management Service)**: manages encryption keys and performs cryptographic operations
- **SSM Parameter Store**: stores configuration values and secrets, optionally encrypted using KMS

Core idea:
> KMS protects the keys. SSM stores the values. IAM decides who gets to see anything.

---

## Why This Exists

Teams need a way to:
- store secrets securely
- avoid hardcoding credentials
- encrypt sensitive values at rest
- control access with IAM
- audit secret access
- avoid running heavy tools like Vault (when needs are simpler)

KMS + SSM exists to provide:
- **managed encryption**
- **tight IAM integration**
- **low operational overhead**
- **good-enough security for many workloads**

---

# 1) KMS Basics (The Root of Trust)

---

## What KMS Actually Does

KMS:
- creates and manages **encryption keys**
- encrypts and decrypts data
- controls key usage via IAM policies
- logs every key usage (audit trail)

KMS does NOT:
- store application secrets directly (by default)
- know what your data means

Rule:
> KMS protects keys, not business logic.

---

## Envelope Encryption (How KMS Is Used)

Typical pattern:
1. KMS key (CMK) exists
2. data is encrypted using a data key
3. data key is encrypted by KMS key
4. encrypted data stored anywhere (SSM, DB, S3)

This limits exposure of the master key and scales well.

---

# 2) SSM Parameter Store Basics

---

## What Parameter Store Is

SSM Parameter Store stores values as:
- **String**
- **StringList**
- **SecureString** (encrypted with KMS)

Parameters are versioned and can be:
- retrieved via API
- injected into instances or containers
- accessed by apps at runtime

---

## SecureString (The Important Part)

When using `SecureString`:
- value is encrypted at rest using KMS
- access requires **both**:
  - SSM permission
  - KMS decrypt permission

This creates layered security.

Rule:
> No KMS permission = unreadable secret.

---

# 3) Common KMS + SSM Workflow

---

## Runtime Secret Access (Typical)

Flow:
1. app runs with IAM role
2. app requests parameter from SSM
3. SSM checks IAM permission
4. SSM calls KMS to decrypt
5. decrypted value returned to app

Secrets are:
- encrypted at rest
- decrypted only at access time
- never hardcoded

---

## CI/CD Secret Access

Flow:
1. pipeline assumes IAM role
2. role has read-only access to required parameters
3. secrets fetched during deploy
4. secrets never stored in repo

Avoid:
- exporting secrets to logs
- storing decrypted secrets long-term

---

# 4) Access Control (Where Security Actually Lives)

---

## IAM Policy Model

To read a secure parameter, you need:
- `ssm:GetParameter`
- `kms:Decrypt` on the specific key

This enables:
- fine-grained access control
- environment isolation (dev/prod)
- least privilege enforcement

Example (conceptual):
- app-role can read `/prod/db/password`
- cannot read `/prod/admin/*`

---

## Key Policies vs IAM Policies

- **IAM policy**: who can call KMS
- **KMS key policy**: who the key trusts

Both must allow access.

Rule:
> If either policy blocks you, decryption fails.

---

# 5) Rotation and Versioning

---

## Rotation Options

- manual rotation (update parameter value)
- automated rotation via scripts or Lambda
- KMS key rotation (separate from secret rotation)

Important:
> Rotating the KMS key does NOT rotate the secret value.

---

## Versioning

SSM keeps versions:
- rollback possible
- audit-friendly
- safer updates

Apps can:
- fetch latest version
- or pin to a specific version (rare, but possible)

---

# 6) Performance and Scaling Considerations

---

## Latency

- SSM + KMS calls are network operations
- repeated calls per request are expensive

Best practice:
- fetch secrets at startup
- cache in memory
- refresh on schedule if needed

Rule:
> Don’t call SSM on every request unless you enjoy latency.

---

## Limits

- API rate limits exist
- KMS decrypt has quotas
- high-throughput apps need caching

---

# 7) When KMS + SSM Is a Good Fit

---

## Good Fit When
- secrets are relatively static
- rotation frequency is low/moderate
- you want minimal ops overhead
- IAM-based access control is sufficient
- workloads already run on cloud IAM roles

---

## Not Ideal When
- you need dynamic secrets (DB creds per app)
- you need short-lived credentials
- you need secret leasing and revocation
- you need complex workflows

That’s when Vault starts making sense.

---

# 8) Comparison with Vault (Conceptual)

---

| Feature | KMS + SSM | Vault |
|------|---------|------|
| Managed service | Yes | No |
| Dynamic secrets | No | Yes |
| IAM integration | Native | Via auth methods |
| Rotation automation | Basic | Strong |
| Operational overhead | Low | High |
| Audit logging | Yes | Yes |

Rule:
> KMS + SSM is simple and boring. Vault is powerful and demanding.

---

# 9) Common Mistakes

---

- storing secrets as plain `String` instead of `SecureString`
- giving wide `kms:Decrypt` permissions
- reusing one KMS key for everything
- calling SSM on every request
- confusing KMS key rotation with secret rotation
- storing secrets in user data or env vars permanently

Worst mistake:
> “We use KMS, so secrets are safe everywhere.”  
No. IAM still decides who gets them.

---

## Interview-Ready Summary

> KMS + SSM is a cloud-native secret management pattern where sensitive parameters are stored in SSM Parameter Store as SecureStrings encrypted by KMS keys. Access requires both SSM permissions and KMS decrypt permissions, enabling strong IAM-based least privilege control and auditability. Secrets are encrypted at rest and decrypted only at access time, with versioning and optional rotation. This approach is simple and low-ops but best suited for relatively static secrets, unlike Vault which supports dynamic secrets, leasing, and fine-grained lifecycle control.

---

## Final Takeaway

- **KMS** is the cryptographic root of trust
- **SSM Parameter Store** is the secure storage layer
- **IAM** is the gatekeeper

KMS + SSM is not fancy.
It’s reliable, boring, and good enough for many systems.

And boring is exactly what you want for secrets.
