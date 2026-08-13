# Networking — Scenario Questions

**Label:** `Interview Preparation`

## Foundation
1. Explain the difference between L4 and L7 load balancing with an example of when each is appropriate.
2. Walk through what happens, step by step, when a browser requests a website (DNS through TCP handshake through HTTP response).

## Intermediate
3. A service works fine internally but fails intermittently when accessed from a partner network over a site-to-site VPN. Diagnose.
4. Design DNS resolution for a hybrid environment with on-prem, Azure, and GCP components.

## Senior
5. Design network segmentation for a workload subject to PCI-DSS, assuming a shared hub-spoke platform.
6. Outbound connections from a busy service intermittently fail under load with connection resets. Diagnose and fix.
7. A single ExpressRoute circuit outage caused a 4-hour full hybrid connectivity loss. Redesign for resilience.

## Principal
8. Design a global, multi-region load balancing and failover strategy for a latency-sensitive customer-facing application.
9. Design private-only connectivity for 200 PaaS services across a large enterprise, including DNS and policy enforcement.
10. Propose a strategy to move a WAF from detection-only to blocking mode in a live, high-traffic production environment.

## Architecture Scenario
11. Design multi-cloud network connectivity between Azure and GCP for a workload requiring low-latency, private communication between services in both clouds.
12. Design the full network architecture (connectivity, segmentation, egress inspection, DNS) for a new enterprise landing zone from scratch.
