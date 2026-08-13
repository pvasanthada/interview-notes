# Hybrid Connectivity

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
Dedicated, private connectivity between on-premises networks and cloud environments — Azure ExpressRoute, Azure VPN Gateway, GCP Cloud VPN, and GCP Cloud Interconnect — as alternatives to routing hybrid traffic over the public internet.

## Why Architects Use It
Provides predictable latency, higher bandwidth, and (for dedicated circuits) a private path that doesn't traverse the public internet — important for latency-sensitive, high-volume, or compliance-sensitive hybrid workloads.

## Architecture Considerations
- **ExpressRoute/Interconnect (dedicated circuit) vs. VPN (encrypted tunnel over the internet)**: dedicated circuits offer more predictable performance and higher bandwidth ceilings at meaningfully higher cost and longer provisioning lead time; VPN is faster to provision, cheaper, and a reasonable choice for lower-bandwidth or non-latency-critical needs, or as a backup path.
- **Resilience design**: a single ExpressRoute circuit is a single point of failure — production-grade hybrid connectivity typically pairs a primary dedicated circuit with a VPN failover path, or uses dual circuits from different providers/locations.
- **BGP route exchange**: both ExpressRoute/Interconnect and VPN can use BGP for dynamic route propagation, avoiding static route maintenance as the network evolves.
- **Bandwidth planning**: dedicated circuits are provisioned at fixed bandwidth tiers — undersizing causes throttling under peak load; oversizing wastes recurring cost.

## Common Mistakes
- Treating a single ExpressRoute/Interconnect circuit as fully resilient with no failover path.
- Choosing VPN for a workload with genuine high-bandwidth, low-latency requirements where a dedicated circuit was actually justified.
- Not planning for bandwidth growth, requiring a disruptive circuit resize later.

## Principal-Level Questions
- How would you design resilient hybrid connectivity for a business-critical workload requiring both on-prem and cloud presence?
- When is VPN sufficient, and when does the business case justify a dedicated circuit?

## Scenario Question
> Your only ExpressRoute circuit experienced a provider-side outage, causing a full hybrid connectivity loss for 4 hours.

## Strong Answer (Outline)
1. Immediate: this exposes a single-point-of-failure design — no failover path existed.
2. Redesign: add a VPN Gateway failover path (BGP-based, lower priority route) so a circuit failure triggers automatic failover rather than a total outage — acceptable given VPN's lower cost as a backup-only path, not the primary.
3. For workloads where even VPN-tier bandwidth/latency during failover is unacceptable, consider a second ExpressRoute circuit via a different peering location/provider for true dual-dedicated-circuit resilience — justify the added cost against the business impact of the outage that just occurred.
4. Add monitoring/alerting on circuit health and BGP session state so failover events (and non-failover circuit degradation) are visible immediately, not discovered via user reports.

## Follow-Up Questions
- How would you test this failover path without waiting for a real outage?
- How does cost scale between a VPN-failover design and a true dual-dedicated-circuit design, and how would you justify the choice to leadership?

## Trade-offs
- Dedicated circuit + VPN failover: cost-effective resilience, failover path has lower bandwidth/higher latency than primary — acceptable for most, not for the most latency-critical workloads.
- Dual dedicated circuits: strongest resilience, meaningfully higher recurring cost.
