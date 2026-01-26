# Storage Tiers (S3 Standard vs IA vs Glacier)

---

## What This Means

S3 storage tiers are basically **different price/performance deals** for storing objects:
- **S3 Standard**: fast access, higher storage cost
- **S3 Standard-IA / One Zone-IA**: cheaper storage, but you pay more when you read it (meant for infrequent access)
- **S3 Glacier tiers**: very cheap storage, but retrieval is slower and sometimes costs more

Core idea:
> Don’t pay “hot storage” prices for data you never touch. Humans do this anyway, but computers shouldn’t.

---

## Why This Exists

Different data has different “temperature”:
- **hot**: used constantly (app assets, active data)
- **warm**: used sometimes (monthly reports)
- **cold**: rarely used (archives, compliance, old logs)

Storage tiers exist to:
- reduce storage cost at scale
- match retrieval speed to business needs
- support retention/compliance without wasting money

---

# 1) S3 Standard (Hot Data)

---

## What It Is
Default general-purpose storage:
- low latency, high throughput
- designed for frequently accessed objects

## Best For
- app assets (images, JS bundles)
- active datasets
- data lakes used regularly
- logs used often (recent logs)

## Tradeoffs
- highest storage cost among common tiers
- but cheapest in “operational surprise” because retrieval is normal

---

# 2) S3 Standard-IA (Infrequent Access)

---

## What It Is
Cheaper storage, designed for data accessed **less often**, but still needs **milliseconds access** when required.

Key traits (conceptually):
- same durability target as Standard
- lower storage cost
- **retrieval fee** when you read data
- typically has a **minimum storage duration** (you pay for a minimum number of days even if you delete early)

## Best For
- backups you might restore occasionally
- older logs you sometimes query
- DR artifacts, old reports

## Tradeoffs
- reading lots of data can become expensive
- bad fit for hot workloads (cost and fees stack up)

---

# 3) S3 One Zone-IA (Cheaper, Less Resilient)

---

## What It Is
Like Standard-IA but stored in **one AZ**, so it’s cheaper but less resilient to AZ failure.

## Best For
- re-creatable data
- secondary copies
- non-critical backups
- processed outputs you can regenerate

## Tradeoffs
- AZ outage can impact availability (or worse)
- don’t store your only copy of critical data here unless you enjoy preventable disasters

---

# 4) Glacier Storage Classes (Cold Archive)

---

## What It Is
For long-term retention and archival.
Cheapest storage, but retrieval is slower.

Common Glacier tiers (conceptual):
- **Glacier Instant Retrieval**: archive pricing, but fast retrieval (ms) for rarely accessed data
- **Glacier Flexible Retrieval**: minutes to hours depending on retrieval option
- **Glacier Deep Archive**: lowest cost, slowest retrieval (hours)

## Best For
- compliance archives (7 years retention)
- old backups
- historical logs
- legal hold datasets

## Tradeoffs
- retrieval time delay (not for “need it now”)
- retrieval fees (especially at scale)
- minimum storage duration is typically longer

Rule:
> Glacier is for “we might need it later”, not “our app reads this every minute”.

---

# 5) Decision Framework (How to Choose)

---

## Ask These Questions

1) **How often is the data accessed?**
- frequent → Standard
- occasional → Standard-IA / Glacier Instant Retrieval
- rare → Glacier Flexible / Deep Archive

2) **How quickly must it be retrieved?**
- ms → Standard / IA / Glacier Instant Retrieval
- minutes → Glacier Flexible (expedited/standard style)
- hours → Deep Archive

3) **Is the data critical and irreplaceable?**
- critical → avoid One Zone-IA as the only copy
- non-critical/derivable → One Zone-IA can be fine

4) **Do you delete objects quickly?**
If yes, watch out for **minimum storage duration charges** in IA/Glacier.

5) **Do you read large volumes?**
If yes, IA/Glacier retrieval fees can hurt.

---

# 6) Lifecycle Policies (The Practical DevOps Tool)

---

## What Lifecycle Rules Do

Automatically move objects between tiers based on age:
- Day 0–30: Standard
- Day 31–90: Standard-IA
- Day 91–365: Glacier
- After 1–7 years: Deep Archive
- Delete after retention period

This is how you scale storage without manually managing millions of objects.

Rule:
> The best storage tier decision is the one automated by lifecycle policies.

---

# 7) Real Use Cases (DevOps Friendly)

---

## Logs
- Standard for recent logs (fast troubleshooting)
- IA after 30–90 days
- Glacier/Deep Archive after 6–12 months for compliance

## Backups
- recent snapshots/backups: Standard or IA (depending on restore frequency)
- old backups: Glacier (archive)
- compliance backups: Deep Archive

## Media Content
- actively served content: Standard (often behind CDN)
- older content rarely requested: IA or Glacier Instant Retrieval

---

# 8) Common Mistakes

---

- putting hot data in IA (retrieval fees + pain)
- putting your only critical copy in One Zone-IA
- using Glacier then being shocked it’s not instantly available
- no lifecycle policies (paying Standard forever)
- deleting early and paying minimum duration charges anyway
- forgetting retrieval fees in cost forecasts

Worst mistake:
> Treating storage tiers as “set once and forget forever”. Data temperature changes.

---

## Interview-Ready Summary

> S3 storage tiers balance storage cost, access frequency, and retrieval latency. S3 Standard is for frequently accessed data with low-latency retrieval. Standard-IA reduces storage cost for infrequently accessed but still millisecond-retrieval data, at the expense of retrieval fees and minimum storage duration charges. One Zone-IA is cheaper but stores data in a single AZ, suitable only for re-creatable or secondary data. Glacier tiers provide archival storage with very low cost and slower retrieval, appropriate for backups, compliance, and long-term retention. Lifecycle policies automate tier transitions based on object age to optimize cost at scale.

---

## Final Takeaway

- **Standard**: hot and frequently used
- **IA**: cold-ish but still needs fast access
- **Glacier**: true archive, cheap but slow to pull back

Choose based on access frequency + restore urgency, then automate it with lifecycle policies so you don’t have to “remember” later (because you won’t).
