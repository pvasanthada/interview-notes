# System Design Fundamentals

**Label:** `Reference Architecture` / `Interview Preparation`

## What It Is
The discipline of translating business/functional requirements into a technical architecture that meets specified non-functional requirements (scale, availability, latency, consistency) within real constraints (cost, team capability, timeline).

## Why Architects Use It
This is the core skill principal/staff interviews are built around — not memorizing a specific design, but demonstrating a repeatable, structured reasoning process (see [`docs/answering-frameworks.md`](../docs/answering-frameworks.md)).

## Key Concepts
- **Capacity planning**: back-of-envelope estimation (requests/sec, data volume, growth rate) grounds design decisions in realistic numbers rather than abstract "make it scalable" hand-waving — a system designed for 100 req/s looks very different from one designed for 100,000 req/s.
- **Scalability**: horizontal (add more instances) generally preferred over vertical (bigger instances) for resilience and elasticity, but requires the workload to actually be parallelizable/statelessly scalable — not every workload is.
- **Availability vs. reliability vs. resilience**: availability (is it up right now), reliability (does it consistently perform correctly over time), resilience (does it recover gracefully from failure) — distinct properties that sometimes trade off against each other (e.g., a system that fails fast and cleanly may have lower measured "availability" during a partial outage but higher genuine reliability of correct behavior).
- **Fault domains**: identify what can fail together (a single AZ, a single region, a single dependency) and design so a single fault domain failure doesn't take down the whole system.
- **Caching**: reduces load and latency but introduces consistency trade-offs (stale data) — choose cache invalidation strategy deliberately based on how stale is acceptable for the specific data.
- **Queues/event-driven design**: decouples producers from consumers, smooths bursty load, and improves resilience to downstream slowness — at the cost of added complexity (eventual consistency, harder end-to-end tracing).
- **API design (REST/GraphQL/gRPC)**: choose based on actual access patterns — REST for broad simplicity/cacheability, GraphQL for flexible client-driven queries over complex data graphs, gRPC for high-performance internal service-to-service communication.
- **Microservices vs. monolith**: microservices offer independent scaling/deployment/team ownership at the cost of distributed systems complexity (network calls, eventual consistency, operational overhead) — the right choice depends on team structure and genuine scaling needs, not default assumption that microservices are more "modern."

## Common Mistakes
- Designing for a scale far beyond what the requirements actually call for ("resume-driven architecture").
- Treating microservices as a default best practice rather than a trade-off to justify against team size and actual independent-scaling needs.
- Ignoring capacity/estimation numbers entirely, leading to vague, unfalsifiable design claims.

## Principal-Level Questions
- Walk through how you'd estimate the required infrastructure capacity for a new service given only a rough monthly active user count.
- When would you choose a monolith over microservices in 2026, and defend it?

## Scenario Question
> Design a URL-shortening service expected to handle 100M new URLs per month and 1B redirects per month.

## Strong Answer (Outline)
1. Capacity estimate: ~40 writes/sec average (100M/month), redirects ~400 reads/sec average — but design for peak, likely several multiples of average; this ratio (read-heavy, ~10:1) shapes the design significantly toward optimizing read path.
2. Data model: a key-value mapping (short code → long URL) — a simple, horizontally-scalable key-value store or a relational database with appropriate indexing both work at this scale; a dedicated key-value store simplifies horizontal scaling further if growth projections are much larger.
3. Read path: aggressive caching (the read:write ratio strongly favors this) — a cache layer in front of the datastore absorbs the vast majority of redirect traffic.
4. Short code generation: a counter-based or hash-based approach, with collision handling; a distributed system needs a strategy to generate unique codes without a single bottlenecked counter (e.g., pre-allocated ranges per node).
5. Availability: multi-AZ deployment at minimum; redirects are latency- and availability-critical (a broken short link is a poor user experience), so this deserves genuine resilience investment even in an otherwise simple system.
6. State the trade-offs explicitly: eventual consistency is acceptable here (a newly created URL doesn't need instant global read consistency), which simplifies the design considerably versus a strongly-consistent requirement.

## Follow-Up Questions
- How would you handle a custom/vanity URL feature layered on top of this design?
- How would your design change if this needed to support link analytics (click tracking) at the same scale?

## Trade-offs
- Simplicity of design vs. handling edge cases (custom URLs, expiration, analytics) — a principal-level answer should note these as deliberately deferred, not overlooked.
