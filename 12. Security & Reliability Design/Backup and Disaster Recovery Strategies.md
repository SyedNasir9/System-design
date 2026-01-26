# Backup and Disaster Recovery Strategies

---

## What This Means

- **Backup**: a copy of data you can restore later.
- **Disaster Recovery (DR)**: the plan (and systems) to restore service after a major failure like region outage, ransomware, accidental deletion, or “someone ran a script in prod”.

Core idea:
> Backups are data. DR is the ability to run the business again.

Lots of teams have backups.
Fewer teams have *restores that actually work*.

---

## Why This Exists

Things that break in real life:
- accidental deletes / bad migrations
- data corruption
- ransomware
- cloud region outages
- misconfigurations / IaC mistakes
- credential compromise
- hardware failures (yes, still)

Backup + DR exist to:
- reduce downtime
- reduce data loss
- meet compliance requirements
- make failures survivable instead of catastrophic

---

# 1) Key DR Concepts (You Must Know)

---

## RPO (Recovery Point Objective)
“How much data can we lose?”

Examples:
- RPO = 5 minutes → you can lose up to 5 minutes of data
- RPO = 24 hours → daily backups are fine

---

## RTO (Recovery Time Objective)
“How fast must we recover service?”

Examples:
- RTO = 15 minutes → automated failover and ready capacity
- RTO = 8 hours → manual restore might be okay

---

## DR Tiering (How Serious You Are)

- **Backup only**: restore data, rebuild app later (slow)
- **Pilot light**: minimal infra running, scale up during disaster
- **Warm standby**: smaller version always running, scale up fast
- **Hot active-active**: full capacity in multiple regions (expensive, complex)

Rule:
> Lower RTO/RPO costs more. Money is the most reliable technology on earth.

---

# 2) Backup Strategies (Data Protection)

---

## What to Back Up

- databases (SQL/NoSQL)
- object storage (critical buckets)
- secrets (carefully, with encryption)
- configs and IaC state (Terraform state, cluster manifests)
- application state (if any)
- audit logs (often overlooked)

Also: backups of backups metadata, because humans break tooling too.

---

## Backup Types

### Full Backup
- complete copy
- easier restore
- slower, more storage

### Incremental Backup
- only changes since last backup
- efficient
- restore requires chain (more complex)

### Differential Backup
- changes since last full backup
- faster restore than incremental chain
- more storage than incremental

Most production setups are:
> periodic full + frequent incremental.

---

## Snapshots vs Logical Dumps

### Snapshots (Volume/DB Snapshots)
Pros:
- fast
- good for large datasets
Cons:
- can capture corruption if taken after corruption
- sometimes harder portability

### Logical Dumps (SQL dump / export)
Pros:
- portable
- can restore to different system/version (sometimes)
Cons:
- slower for huge DBs
- can impact performance if not done carefully

Best practice:
> Use both if the system is critical. Snapshots for speed, logical dumps for insurance.

---

## Backup Frequency and Retention

Define:
- hourly/daily/weekly cadence
- retention tiers (e.g., 7 days + 30 days + 1 year)

Common retention model:
- short-term frequent backups (fast recovery)
- long-term archives (compliance)

---

## Encryption and Access Control

Backups are extremely sensitive:
- encrypt at rest
- encrypt in transit
- limit who can read/restore
- audit access
- store keys securely (KMS/Vault)

Backup access is basically “keys to the kingdom”.

---

## Immutability and Ransomware Resistance

To survive ransomware:
- use **immutable backups** (object lock / WORM)
- separate backup credentials from production
- write backups to a separate account/project
- limit delete permissions (MFA delete / dual control)

Rule:
> If attackers can delete backups, you don’t have backups. You have false confidence.

---

# 3) Disaster Recovery Strategies (Service Recovery)

---

## Strategy A: Restore from Backups (Backup-Only DR)
Workflow:
1. incident happens
2. provision infra (manual or IaC)
3. restore DB from backup
4. redeploy apps
5. validate + go live

Pros:
- cheapest
Cons:
- slowest RTO
- more human steps (more failure chances)

---

## Strategy B: Pilot Light
Minimal services running in DR region:
- network, IAM, logging, maybe small DB replica

Workflow:
1. scale up infra
2. promote/restore data
3. switch traffic

Pros:
- faster than backup-only
Cons:
- still some manual/automation needed

---

## Strategy C: Warm Standby
Reduced-capacity full stack is always running:
- app tier + DB replication in DR region

Workflow:
- failover traffic and scale up

Pros:
- good RTO
Cons:
- higher cost, needs regular testing

---

## Strategy D: Active-Active (Multi-Region)
Both regions serve traffic.

Pros:
- very low RTO
- resilient to region failures
Cons:
- hardest to build
- data consistency challenges
- complex operations
- expensive

Active-active is a lifestyle, not a checkbox.

---

# 4) Database DR Patterns (Common)

---

## Backups + Point-in-Time Recovery (PITR)
- continuous logs + snapshots
- restore to “just before the disaster”

Great for:
- accidental deletes
- corruption
- ransomware

---

## Replication to Another Region
- async replication is common
- watch replication lag (affects RPO)

Failover flow:
- promote replica to primary
- redirect app traffic
- re-establish replication after recovery

---

# 5) DR Runbooks (How Humans Survive Incidents)

---

## A Good Runbook Includes

- exact steps to restore and failover
- who is responsible for each step
- access requirements and break-glass process
- validation checklist (data + app health)
- communication plan (internal + customer)
- rollback and re-entry steps (return to primary region)

If DR requires tribal knowledge, it will fail at the worst moment.

---

## Testing (The Part Everyone Skips, Then Regrets)

Types of DR tests:
- **restore tests** (can we restore backups?)
- **tabletop exercises** (walkthrough)
- **game days** (simulate outages)
- **partial failovers** (one service at a time)
- **full region failover drills** (rare but valuable)

Best practice:
> Test restores regularly. Untested backups are just expensive files.

---

# 6) Validation After Restore

---

What to verify:
- application boots and can serve traffic
- data integrity checks (row counts, checksums if possible)
- auth and secrets are correct
- background jobs are not double-processing
- DNS/routing is correct
- monitoring/alerts are active
- performance is acceptable under real load

A restore that “completed successfully” can still produce wrong data. Computers love lying politely.

---

# 7) Common Mistakes

---

- no clear RPO/RTO targets (so DR is vague and slow)
- backups in same region/account as prod (single blast radius)
- no immutability (ransomware deletes backups)
- no restore testing
- relying on manual steps during crisis
- forgetting to back up IaC state/configs/secrets
- restoring DB but breaking app compatibility (schema/version mismatch)
- not planning for DNS/traffic switching

---

## Interview-Ready Summary

> Backup strategies protect data via scheduled full/incremental backups, snapshots and logical exports, encryption, retention policies, and ransomware-resistant immutability. Disaster recovery strategies focus on restoring service within RPO/RTO targets using approaches like backup-only restores, pilot light, warm standby, or active-active multi-region. Effective DR requires automated runbooks, cross-region or cross-account backup storage, least-privilege access to backups, regular restore and failover testing, and validation checks to ensure recovered systems are correct and usable.

---

## Final Takeaway

Backups are easy to *create*.
Backups are hard to *restore under pressure*.

So the real strategy is:
- define RPO/RTO
- store backups safely (encrypted + immutable + separate blast radius)
- automate restore steps
- test restores like you actually want to keep your job
