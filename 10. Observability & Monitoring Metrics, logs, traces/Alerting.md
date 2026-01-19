# Alerting (CloudWatch, Prometheus Alertmanager)

---

## What Is Alerting?

**Alerting** is the process of detecting abnormal conditions and notifying humans or automation to take action.

Key idea:
> Alerts exist to trigger response, not to describe reality.

If an alert fires and nobody can (or should) act on it, it’s not an alert.
It’s noise.

---

## Why Alerting Matters

Modern systems fail in partial, non-obvious ways:
- latency spikes without errors
- dependencies degrade slowly
- capacity saturates silently
- deployments introduce subtle regressions

Alerting aims to:
- reduce time-to-detect (TTD)
- reduce time-to-mitigate (TTM)
- protect SLOs and user experience

Bad alerting increases:
- burnout
- missed incidents
- blind rollbacks
- “ignore the pager” culture

---

## Monitoring vs Alerting vs Observability

| Concept | Purpose |
|--------|---------|
| Monitoring | Collect signals and track health |
| Alerting | Notify when action is needed |
| Observability | Investigate unknown failures |

Alerting is a subset of monitoring.
And it must be backed by observability, or incident response becomes guessing.

---

## What to Alert On: Symptoms vs Causes

### Symptom-based alerting (Preferred)
Alert on user impact:
- error rate is high
- latency is high
- availability is dropping
- SLO burn rate is too high

### Cause-based alerting (Careful)
Alert on internal conditions:
- CPU high
- memory high
- disk high
- pod restarts

Cause alerts can be useful but often create noise unless tied to impact.

Good alerting prioritizes:
> “Users are affected” over “a metric looks weird”.

---

## Alert Quality Principles (Non-Negotiable)

A good alert is:
- actionable (clear next steps)
- urgent (needs attention now)
- accurate (low false positives)
- scoped (what service/region/version)
- deduplicated (one incident, not 200 pages)

If your alerts are not actionable, you’re building a pager-based anxiety generator.

---

## Core Alert Types

### 1) Availability Alerts
- service is down
- error rate above threshold

### 2) Latency Alerts
- p95/p99 latency spike
- timeouts rising

### 3) Saturation Alerts
- CPU throttling
- memory pressure / OOM risk
- queue lag growing
- DB connections exhausted

### 4) Deployment/Change Alerts
- errors rising after deploy
- canary health failing

### 5) Security/Integrity Alerts (Selective)
- critical vulnerabilities detected in runtime artifacts
- unauthorized access patterns (carefully tuned)

---

## CloudWatch Alerting

### :contentReference[oaicite:0]{index=0}

CloudWatch alerting revolves around:
- Metrics
- Alarms
- Notifications via SNS
- Composite alarms (combine conditions)

Best for:
- AWS managed services (EC2, RDS, ALB, Lambda, DynamoDB)
- account/region-wide monitoring
- operational alerts integrated with AWS events

Strengths:
- native AWS integration
- managed and reliable
- easy to alert on AWS service metrics

Weaknesses:
- can get expensive at scale (metrics, logs, dashboards)
- limited flexibility compared to PromQL-style querying
- cross-service correlation requires careful design

CloudWatch is excellent when your system is AWS-heavy and you want native visibility.

---

## Prometheus Alerting (Alertmanager)

### :contentReference[oaicite:1]{index=1}

Prometheus handles:
- scraping metrics (Prometheus)
- evaluating alert rules (Prometheus)
- routing/dedup/grouping notifications (Alertmanager)

Best for:
- Kubernetes workloads
- application-level SLO alerting
- flexible, label-based alert routing

Strengths:
- powerful query language (PromQL)
- label-driven routing (team/service/env)
- deduplication and grouping built-in
- strong ecosystem for K8s

Weaknesses:
- requires operational maturity (rule quality, cardinality control)
- misconfigured alerts create storms quickly
- multi-cluster/multi-region setups need federation or remote write

Alertmanager is not the alert brain. Your alert rules are.

---

## CloudWatch vs Alertmanager (When to Use Which)

| Use Case | CloudWatch | Prometheus + Alertmanager |
|---------|------------|---------------------------|
| AWS service health | Excellent | Possible but indirect |
| Kubernetes app metrics | Possible (with work) | Excellent |
| Custom app SLOs | Okay | Excellent |
| Routing by labels/team | Limited | Strong |
| Managed simplicity | Strong | Medium (ops required) |

Most real systems use both:
- CloudWatch for AWS infrastructure/platform signals
- Prometheus/Alertmanager for Kubernetes + application signals

---

## Alert Routing and Ownership

Alerts must map clearly to ownership:
- service/team labels
- environment labels (prod vs non-prod)
- severity labels

Routing channels:
- on-call pager
- Slack/Teams notifications
- ticket creation (only for non-urgent alerts)

Rule of sanity:
- Pager is for urgent, actionable, prod-impact alerts
- Everything else should not wake humans up

---

## Severity Levels (Practical)

Typical severity scheme:
- **SEV1**: user-visible outage or rapid SLO burn (page immediately)
- **SEV2**: partial impact / degradation (page or urgent notify)
- **SEV3**: warning / trend (notify, not page)
- **INFO**: logging-only / dashboards

If everything is SEV1, nothing is.

---

## Alert Noise: The Main Enemy

Common sources of alert noise:
- static thresholds for dynamic systems
- alerting on causes not symptoms
- no grouping/deduplication
- no suppression during maintenance
- flapping metrics and short windows

Noise leads to:
- alert fatigue
- missed real incidents
- distrust in monitoring

A noisy alert system is functionally broken.

---

## SLO-Based Alerting (Best Practice)

Rather than alerting on raw CPU or random thresholds, alert on:
- error budget burn rate
- availability SLI dropping
- latency SLI violation

This keeps alerts aligned with:
- user experience
- business impact

It also prevents paging on harmless spikes.

---

## Runbooks and Playbooks

Every serious alert should link to:
- what it means
- how to confirm impact
- immediate mitigations
- escalation path
- rollback guidance

An alert without a runbook is just a panic button.

---

## Common Failure Modes

- paging on CPU usage (too noisy)
- no alerts on user impact (false sense of safety)
- missing labels (can’t route alerts)
- no maintenance silencing (pages during upgrades)
- thresholds not tuned to baseline
- “alert everything” dashboards-as-alerts

Alerting is a product. It needs maintenance.

---

## Mental Model (Remember This)

- Alerts should be symptom-based and actionable
- CloudWatch excels for AWS-native signals
- Alertmanager excels for label-driven K8s/app signals
- SLOs keep alerts aligned with impact
- Noise destroys trust

Alerting is not about more alerts.
It’s about better ones.

---

## Interview-Ready Summary

> Alerting detects actionable abnormal conditions and notifies responders, typically using CloudWatch Alarms for AWS-native metrics and Prometheus Alertmanager for flexible, label-based application and Kubernetes alert routing; high-quality alerting focuses on symptom/SLO-based signals, deduplication, and runbook-backed response to avoid alert fatigue.

If someone says “we alert on CPU > 80%,” they’re describing a beginner setup that will page you into hatred.

---

## Final Takeaway

Alerting is the line between:
- catching incidents early
and
- finding out from angry users

CloudWatch and Alertmanager both work, but success depends on:
- choosing the right signals (symptoms)
- tuning and deduping intelligently
- routing to the right owners
- backing alerts with runbooks

Your pager should be trustworthy.
If it isn’t, the system is already failing.
