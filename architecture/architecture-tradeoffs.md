# Architecture Trade-off Discussions

**Label:** `Interview Preparation`

A principal-level interview will frequently probe your ability to reason about a specific trade-off pair rather than just asking you to define terms. Use this as a rehearsal reference — for each pair, be ready to state the decision axis, not just list pros/cons.

## Monolith vs. Microservices
**Decision axis**: team structure and independent-scaling need, not "which is more modern."
- Monolith: simpler operations, easier transactional consistency, harder to scale specific components independently, harder for many teams to work on concurrently without stepping on each other.
- Microservices: independent scaling/deployment/ownership, distributed systems complexity (network calls, eventual consistency, harder debugging).
- **Strong answer framing**: "I'd default to a well-modularized monolith until there's a genuine, demonstrated need for independent scaling or team autonomy that the monolith is actually blocking — premature microservices decomposition is one of the most common over-engineering patterns I see."

## VM vs. Container
**Decision axis**: isolation/OS-level control need vs. density/deployment speed.
- VM: full OS isolation, heavier resource footprint, slower startup.
- Container: lighter, faster startup, shares host kernel (weaker isolation boundary — relevant for genuinely multi-tenant untrusted workloads).

## Kubernetes vs. Managed PaaS
**Decision axis**: see [`azure/compute.md`](../azure/compute.md) — team's Kubernetes maturity and actual need for Kubernetes-specific extensibility versus operational simplicity.

## SQL vs. NoSQL
**Decision axis**: data structure, consistency requirements, and query pattern, not scale alone (modern SQL databases scale further than commonly assumed).
- SQL: strong consistency, relational integrity, mature tooling, less flexible schema evolution.
- NoSQL: flexible schema, often better horizontal write scaling for specific access patterns, weaker consistency guarantees (varies by product) and typically weaker cross-entity transactional support.
- **Strong answer framing**: "I'd default to a relational database unless the access pattern or scale genuinely doesn't fit it — most applications benefit from the consistency and query flexibility relational databases provide, and 'NoSQL by default' is a common overcorrection."

## Synchronous vs. Asynchronous Communication
**Decision axis**: whether the caller needs an immediate result to proceed, and how tolerant the system is of eventual consistency.
- Synchronous: simpler to reason about, tighter coupling, failure/latency propagates directly to the caller.
- Asynchronous: better decoupling and resilience to downstream slowness, added complexity (tracking eventual completion, harder end-to-end tracing).

## Centralized vs. Decentralized Platform
**Decision axis**: see [`platform-engineering/platform-architecture.md`](../platform-engineering/platform-architecture.md) — golden paths with self-service (a hybrid, not a pure binary) is usually the mature answer.

## Single-Cloud vs. Multi-Cloud
**Decision axis**: see [`multi-cloud.md`](multi-cloud.md) — genuine strategic driver required to justify the complexity; default to single-cloud depth unless a specific requirement says otherwise.

## Active-Active vs. Active-Passive
**Decision axis**: RTO/RPO requirement versus cost/complexity tolerance — see [`ha-dr.md`](ha-dr.md).

## How to Use This in an Interview
Don't recite a memorized pros/cons list. State the **decision axis** first (what should actually determine the choice for this specific scenario), then apply it to the given context, then name what you're trading away. This demonstrates judgment, not memorization.
