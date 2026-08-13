# Routing, NAT & BGP

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
The mechanisms that determine the path packets take (routing tables, route propagation), how private addresses are translated for internet-bound traffic (NAT), and the protocol underlying dynamic route exchange between networks (BGP), most visible in cloud architecture through ExpressRoute/Interconnect and VPN route propagation.

## Why Architects Use It
Understanding routing is essential to reasoning about hybrid connectivity, multi-region traffic paths, and diagnosing "unreachable" scenarios that trace back to a missing or overlapping route rather than a firewall block.

## Architecture Considerations
- **User-Defined Routes (Azure) / custom routes (GCP)**: override default system routing, commonly used to force traffic through a central firewall/NVA (hub-spoke egress inspection pattern) — a frequent source of asymmetric routing bugs if not applied consistently to both directions of a flow.
- **NAT**: translates private addresses to a shared public IP for outbound internet access — centralizing NAT (Azure NAT Gateway/Firewall SNAT, GCP Cloud NAT) at the hub avoids per-spoke public IP sprawl and gives a consistent, allowlist-able egress IP.
- **BGP over ExpressRoute/Interconnect**: dynamically exchanges routes between on-prem and cloud, avoiding the operational burden of manually maintaining static routes as networks change — but requires understanding route-preference/failover behavior for the design to actually achieve intended resilience.
- **Route propagation vs. explicit routes**: cloud providers often auto-propagate routes learned via BGP into route tables — explicit/static routes may be needed to override this for specific traffic engineering needs (e.g., forcing traffic through an NVA despite a more direct BGP-advertised path).

## Common Mistakes
- Asymmetric routing: forcing outbound traffic through a central firewall via UDR but not accounting for the return path, breaking stateful connections.
- SNAT port exhaustion from too many outbound connections sharing too few NAT IPs/ports, causing intermittent connection failures under load.
- Overlapping IP address ranges across networks that later need to be connected, forcing painful re-addressing.

## Principal-Level Questions
- How would you design egress traffic inspection for a hub-spoke network without introducing asymmetric routing?
- What causes SNAT port exhaustion, and how would you diagnose and fix it?

## Scenario Question
> Outbound connections from a busy application tier intermittently fail, and logs show connection resets under high load specifically.

## Strong Answer (Outline)
1. Hypothesis: SNAT port exhaustion — a shared, limited pool of NAT ports being consumed faster than they're released (especially with short-lived, high-volume outbound connections).
2. Diagnose via NAT gateway/firewall metrics showing port utilization/exhaustion events correlating with the failure timing.
3. Fix: increase available SNAT ports (additional public IPs on the NAT resource, or scale out NAT capacity), reduce outbound connection churn (connection reuse/pooling in the application), or move latency-sensitive outbound traffic to private connectivity paths (private endpoints) that don't consume SNAT ports at all.
4. Add proactive alerting on SNAT port utilization before it reaches exhaustion.

## Follow-Up Questions
- How would connection pooling at the application layer reduce this pressure specifically?
- How does this diagnosis change in a Kubernetes environment with many pods sharing node-level SNAT?

## Trade-offs
- Centralized NAT/egress: simpler governance and IP allowlisting, a shared capacity limit that must be actively monitored and sized for peak load.
