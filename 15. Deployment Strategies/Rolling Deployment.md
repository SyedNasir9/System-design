# Rolling Deployment

---

## What This Means

A **Rolling Deployment** updates application instances **gradually**, replacing old versions with new ones in small batches instead of all at once.

Instead of:
- shutting everything down
- deploying new version
- praying

You:
> replace instances step by step while the system stays live.

---

## Why This Exists

Without rolling deployments:
- deployments cause downtime
- one bad release breaks everything instantly
- rollback becomes stressful
- traffic shifts too abruptly

Rolling deployment exists to:
- achieve zero (or near-zero) downtime
- reduce risk during releases
- allow gradual rollout
- make rollback manageable

---

# 1) How Rolling Deployment Works

---

## Basic Flow

Assume 10 instances running v1.

1. Take 1–2 instances out of rotation
2. Deploy v2 to them
3. Wait for health checks to pass
4. Add them back to traffic
5. Repeat until all instances run v2

At no point is the system fully down.

---

## Key Requirements

- multiple instances (horizontal scaling)
- load balancer
- health checks
- readiness checks
- stateless or compatible state handling

If you only have one instance, “rolling” means “downtime”.

---

# 2) Critical Concepts

---

## Health Checks

Two types:
- **Liveness**: is the app alive?
- **Readiness**: is the app ready to receive traffic?

During rollout:
- new instance must pass readiness before receiving traffic
- failing instance should be removed quickly

Without health checks, rolling deployments become chaos.

---

## Max Unavailable & Max Surge (Kubernetes Concept)

- **maxUnavailable**: how many instances can be down during rollout
- **maxSurge**: how many extra instances can be created temporarily

Example:
- 10 replicas
- maxUnavailable = 1
- maxSurge = 2

System:
- keeps at least 9 available
- may temporarily run up to 12

This controls safety vs speed.

---

## Graceful Shutdown

Before terminating old instance:
- stop accepting new requests
- finish in-flight requests
- close connections cleanly

Otherwise:
- user requests fail mid-flight
- you get random 5xx errors

---

# 3) Advantages of Rolling Deployment

---

- minimal downtime
- no traffic switch complexity
- simple mental model
- no extra environments required
- resource-efficient (no full duplicate environment)

This is why rolling is the default strategy in many systems.

---

# 4) Limitations and Risks

---

## Gradual Spread of Bugs

If v2 has a bug:
- it spreads gradually
- but still reaches 100% unless stopped

Rolling doesn’t prevent bad releases.
It just slows the damage.

---

## Backward Compatibility Issues

If:
- new version changes DB schema
- new version changes API contract

Old and new versions must coexist temporarily.

Otherwise:
- mixed traffic breaks things

---

## Stateful Services

Rolling updates for:
- databases
- tightly coupled systems

can cause:
- replication issues
- inconsistent state
- downtime if not carefully managed

---

## Slow Rollouts

If:
- startup time is long
- readiness takes time

deployment duration increases.

---

# 5) Rolling Deployment vs Other Strategies

---

## Rolling vs Blue-Green

Rolling:
- gradual replacement
- same environment
- cheaper
- more subtle failures

Blue-Green:
- full new environment
- instant traffic switch
- easier rollback
- higher resource cost

---

## Rolling vs Canary

Rolling:
- same traffic proportion per instance
- eventually reaches 100%

Canary:
- intentionally route small % of traffic
- monitor before full rollout

Rolling is simpler.
Canary is more controlled.

---

# 6) Rollback Strategy

---

## Fast Rollback

If v2 fails:
- redeploy v1
- reverse the rolling process
- or scale down v2 and scale up v1

Rollback is faster when:
- images are cached
- infra is stable
- no irreversible migrations occurred

---

## Dangerous Scenario

If:
- v2 changes DB schema in incompatible way
- v1 can’t operate anymore

Rollback becomes complicated.

Rule:
> Always deploy backward-compatible schema changes first.

---

# 7) Observability During Rolling Deployments

---

## What to Monitor

- error rate (5xx)
- latency (p95/p99)
- CPU/memory
- request success rate
- logs for new version only
- health check failures

You want to detect:
- regressions early
- performance degradation
- resource spikes

If you don’t monitor during rollout, you’re just guessing.

---

# 8) Common Mistakes

---

- deploying with only one replica
- no readiness probe
- aggressive scale-in (dropping too many old instances)
- not handling long-running requests
- schema changes breaking old version
- not testing rollback path
- ignoring startup time in rollout strategy

Worst mistake:
> “It worked in staging.”

Production traffic is creative in ways staging never is.

---

## Interview-Ready Summary

> Rolling deployment is a strategy that gradually replaces old application instances with new ones while maintaining service availability. Instances are updated in batches, passing health and readiness checks before receiving traffic. It enables near-zero downtime and resource-efficient releases but requires backward compatibility between versions, proper health checks, and observability to detect regressions. Compared to blue-green and canary deployments, rolling is simpler and cost-effective but offers less controlled traffic isolation during rollout.

---

## Final Takeaway

Rolling deployment is the practical default:
- safe enough
- simple enough
- resource-efficient

But it doesn’t protect you from bad code.
It just gives you time to notice before everything burns.
