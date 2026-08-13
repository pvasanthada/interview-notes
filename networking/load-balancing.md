# Load Balancing & Reverse Proxies

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
Distribution of traffic across multiple backend instances, at Layer 4 (TCP/UDP, connection-level) or Layer 7 (HTTP-aware, request-level routing) — implemented via cloud-native services (Azure Load Balancer/Application Gateway/Front Door, GCP Cloud Load Balancing) or self-managed reverse proxies (NGINX, Envoy).

## Why Architects Use It
Load balancing is the foundational mechanism for both horizontal scalability and availability (routing around unhealthy instances) — nearly every non-trivial architecture depends on getting this layer right.

## Architecture Considerations
- **L4 vs L7**: L4 is simpler, protocol-agnostic, lower latency; L7 enables content-based routing (path/header-based routing, TLS termination, WAF integration) at the cost of understanding/terminating the application protocol.
- **Global vs regional**: global load balancing (Azure Front Door, GCP global HTTP(S) LB) directs users to the nearest/healthiest region; regional load balancers (Azure Application Gateway, regional GCP LB) distribute within a region — combine both for true multi-region resilience.
- **Health probes**: the quality of failover depends entirely on health probe accuracy — a probe checking only "is the process listening" is much weaker than one checking actual application health (e.g., a `/healthz` endpoint verifying downstream dependencies).
- **Session affinity / sticky sessions**: needed for stateful application patterns, but works against even load distribution and complicates scaling — prefer stateless application design where possible instead.

## Common Mistakes
- Shallow health probes that don't reflect real application health, causing traffic to keep routing to a degraded instance.
- Using L4 load balancing where L7 features (path-based routing, WAF) were actually needed, forcing a workaround.
- Sticky sessions used as a substitute for proper session state externalization (e.g., a shared cache), creating scaling and failover fragility.

## Principal-Level Questions
- How would you design health probes to avoid routing traffic to an instance that's "up" but functionally degraded?
- When would you use both a global and a regional load balancer together, and why?

## Scenario Question
> During a partial outage, your load balancer kept routing ~30% of traffic to instances that were technically running but failing all downstream database calls.

## Strong Answer (Outline)
1. Root cause: the health probe checked only process/port liveness, not actual application health — the instance was "up" from the LB's perspective but functionally broken.
2. Redesign the health check endpoint to verify critical downstream dependencies (database connectivity, cache reachability) with a fast timeout, so it fails closed quickly when a real dependency is down.
3. Balance probe depth carefully — an overly deep probe can itself become a bottleneck or cause flapping if it checks too many dependencies too strictly; probe for dependencies that genuinely make the instance unable to serve requests, not every possible downstream.
4. Add circuit-breaker behavior in the application itself so a single degraded dependency doesn't cascade into a full outage even before the LB reacts.

## Follow-Up Questions
- How would you avoid a "thundering herd" if the health check itself becomes a load source during widespread degradation?
- How does this design change for a global multi-region deployment?

## Trade-offs
- Deep health checks: more accurate failover, risk of false-positive removal if the check itself is flaky or a dependency has transient latency.
