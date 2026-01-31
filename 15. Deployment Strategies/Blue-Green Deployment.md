# Blue/Green Deployment

---

## What This Means

**Blue/Green deployment** runs two identical production environments:

- **Blue** → current live version
- **Green** → new version

You deploy the new version to Green, test it, then switch traffic from Blue to Green instantly.

Core idea:
> Don’t replace the engine mid-flight. Build a second plane and switch runways.

---

## Why This Exists

Rolling deployments:
- gradually spread new code
- can mix old and new versions

Blue/Green avoids that by:
- keeping environments fully separate
- switching traffic in one clean move

It exists to:
- eliminate downtime
- simplify rollback
- reduce mixed-version complexity
- isolate releases from active traffic

---

# 1) How Blue/Green Works

---

## Step-by-Step Flow

1. Blue environment is serving traffic.
2. Green environment is deployed with new version.
3. Smoke tests and validation happen on Green.
4. Traffic is switched from Blue → Green (via LB/DNS).
5. Blue becomes standby.
6. If needed, switch back to Blue.

Switch can happen via:
- load balancer target group swap
- DNS update
- reverse proxy config change

At no time are both versions serving live traffic (unless intentionally testing).

---

# 2) Key Requirements

---

- duplicate infrastructure (compute + possibly DB layer compatibility)
- load balancer or traffic control layer
- health checks
- deployment automation
- monitoring before and after switch

Blue/Green costs more because you’re running two environments.

---

# 3) Traffic Switching Strategies

---

## Load Balancer Target Switch (Most Common)

- Blue target group active
- Green target group prepared
- flip traffic to Green

Fast and controlled.
Preferred for most systems.

---

## DNS Switch

- change DNS record to Green
- relies on TTL expiration

Slower due to caching.
Less precise.

---

## Feature Flag Activation

Sometimes Blue and Green exist inside the same infra, but:
- version is enabled via flag

This blends with feature-flag-based releases.

---

# 4) Database Considerations (Critical)

---

## Backward Compatibility

Both Blue and Green must:
- work with the same database
- tolerate temporary coexistence

Best practice:
1. deploy schema changes (backward compatible)
2. deploy new code
3. remove old schema only after safe window

---

## Dangerous Scenario

If Green:
- migrates schema incompatibly
- changes data format

Rollback becomes hard or impossible.

Rule:
> Code can change instantly. Data must change slowly.

---

# 5) Advantages of Blue/Green

---

- zero downtime
- instant rollback (flip traffic back)
- no version mixing
- clear separation of old vs new
- safer major upgrades

This makes it ideal for:
- high-traffic systems
- critical production services
- risky deployments

---

# 6) Limitations and Costs

---

## Infrastructure Cost

- two full environments running
- more compute usage
- more resource consumption

Not cheap at scale.

---

## Operational Complexity

- must keep both environments in sync
- config drift risk
- monitoring complexity

---

## State and Session Handling

If:
- sessions stored in memory
- sticky sessions enabled

Switching traffic can:
- invalidate sessions
- cause login issues

Use:
- shared session store
- stateless apps

---

# 7) Blue/Green vs Rolling vs Canary

---

## Rolling
- gradual replacement
- cheaper
- slower rollback
- mixed versions during rollout

## Blue/Green
- full environment duplication
- instant switch
- easy rollback
- higher cost

## Canary
- small percentage traffic to new version
- gradual traffic increase
- more controlled testing

Blue/Green is about isolation.
Canary is about progressive exposure.

---

# 8) Observability During Switch

---

## Monitor Before Switch

- startup success
- health checks
- internal tests
- logs for errors
- DB connectivity
- memory usage

---

## Monitor After Switch

- error rate
- latency (p95/p99)
- CPU/memory
- user-facing metrics
- business KPIs (signups, transactions)

Rollback should be based on metrics, not panic.

---

# 9) Rollback Strategy

---

Rollback is simple in concept:
- switch traffic back to Blue

Rollback is complex if:
- data migrations are incompatible
- background jobs changed state
- version-specific side effects occurred

Fast rollback only works if:
> You planned for rollback before deployment.

---

# 10) Common Mistakes

---

- not testing Green before switch
- forgetting environment parity
- incompatible DB migrations
- ignoring session state
- leaving Blue environment unpatched
- manual switch with no monitoring

Worst mistake:
> Assuming rollback will be easy without verifying it.

---

## Interview-Ready Summary

> Blue/Green deployment uses two identical production environments to isolate new releases. The new version is deployed to the Green environment while Blue continues serving traffic. After validation, traffic is switched to Green via load balancer or DNS. This strategy enables zero downtime and instant rollback but requires duplicate infrastructure and careful database compatibility. Compared to rolling deployments, Blue/Green offers stronger isolation and faster rollback at higher cost and operational complexity.

---

## Final Takeaway

Blue/Green is for when:
- downtime is unacceptable
- rollback must be fast
- risk tolerance is low

It costs more.
It’s cleaner.
And when production is critical, clean is usually cheaper than chaos.
