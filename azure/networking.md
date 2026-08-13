# Azure Networking

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is

The set of connectivity patterns that connect Azure workloads to each other, to on-premises, and to the internet: VNets, hub-spoke topology, Azure Virtual WAN, private endpoints/Private Link, DNS, and edge services (Firewall, Application Gateway, Load Balancer).

## Why Architects Use It

Correct network design determines blast radius, latency, cost, and the feasibility of Zero Trust segmentation. It's usually the first thing that constrains everything built on top of it.

## Architecture Considerations

- **Hub-spoke vs. Virtual WAN**: hub-spoke (manually peered VNets through a central hub) suits moderate scale and gives full control; Virtual WAN is a managed, Microsoft-operated backbone that scales better for many regions/spokes but trades away some low-level control.
- **Private endpoints vs. service endpoints**: private endpoints bring the PaaS service's IP into your VNet (best isolation, works across peering/on-prem); service endpoints keep traffic on the Azure backbone but don't fully remove public exposure. Prefer private endpoints for anything handling sensitive data.
- **DNS design**: centralized Private DNS Zones linked to the hub, with conditional forwarding for on-prem, is the standard pattern — DNS is frequently the actual root cause of "connectivity" issues.
- **Egress control**: centralize internet egress through Azure Firewall or a NVA in the hub for inspection and consistent policy, rather than allowing each spoke to have its own NAT gateway/public IP.
- **Segmentation**: use NSGs at the subnet level and Azure Firewall/NVA for cross-spoke and egress policy; don't rely on a single layer.

## Common Mistakes

- Overlapping address space between VNets that later need to peer or connect to on-prem.
- Using service endpoints where private endpoints were needed for compliance (data still traverses via a "service" boundary, not fully private).
- No centralized DNS strategy, causing resolution failures across peered/hybrid networks.
- Flat spoke VNets with no subnet segmentation or NSGs — one compromised VM has full lateral access.
- Ignoring transitive routing limitations (spoke-to-spoke doesn't automatically work through VNet peering without a hub NVA/route table).

## Principal-Level Questions

- When would you choose Virtual WAN over a manually managed hub-spoke topology?
- How do you design DNS resolution for a hybrid environment spanning on-prem, Azure, and another cloud?
- How would you design network segmentation for a workload subject to PCI-DSS?

## Scenario Question

> Design network connectivity for an enterprise with 40 Azure subscriptions across 3 regions, an on-prem datacenter, and a requirement that PCI-scoped workloads be fully network-isolated from all other traffic.

## Strong Answer (Outline)

1. **Topology**: Virtual WAN hub per region (given the scale — 40 subscriptions/3 regions favors a managed backbone over manual hub-spoke peering mesh).
2. **Hybrid connectivity**: ExpressRoute from on-prem into the Virtual WAN hub, with a backup VPN path.
3. **PCI isolation**: dedicated hub/VNet (or a separate Virtual WAN hub in some designs) with its own firewall policy, no default routing to/from the general environment, and explicit, reviewed peering only where a documented data flow requires it.
4. **DNS**: centralized Private DNS Zones linked across hubs, conditional forwarders to on-prem DNS.
5. **Egress/inspection**: Azure Firewall Premium (TLS inspection, IDPS) centralized per hub, with policy inherited via Firewall Manager.
6. **Segmentation within spokes**: NSGs per subnet tier (web/app/data), Application Security Groups to avoid IP-based rule sprawl.

## Follow-Up Questions

- How would you handle a workload that needs bidirectional connectivity between the PCI environment and a general-purpose analytics workload?
- What's your approach to IP address planning at this scale to avoid future overlap?
- How would you monitor for policy drift or accidental cross-boundary connectivity?

## Trade-offs

- **Virtual WAN (managed, less granular control) vs. manual hub-spoke (full control, more operational burden)** — choose based on scale and team's networking depth.
- **Full isolation (PCI hub) vs. shared services convenience** — isolation adds duplicate shared services (DNS, firewall) but is usually mandatory for compliance scope reduction.
- **Centralized egress inspection (single choke point, potential bottleneck) vs. distributed NAT (more resilient, harder to govern uniformly)**.
