# GCP Networking

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
GCP's global VPC model (VPCs are global resources with regional subnets, unlike Azure's regional VNets), Shared VPC, Cloud NAT, Cloud DNS, Cloud Load Balancing, and Private Service Connect.

## Why Architects Use It
GCP's networking model is architecturally distinct enough from Azure/AWS (a single VPC can span all regions) that it changes some default design patterns — fewer VPCs are typically needed to cover a global footprint.

## Architecture Considerations
- **Global VPC**: one VPC can have subnets in every region, removing the need for cross-region peering that hub-spoke models require in Azure — simplifies global connectivity considerably.
- **Shared VPC**: a host project owns the VPC; service projects attach and deploy resources into it while IAM stays scoped per service project — this is GCP's primary hub-spoke-equivalent pattern.
- **Cloud NAT**: managed, regional egress NAT for instances without public IPs — analogous in purpose to Azure NAT Gateway/Firewall SNAT.
- **Private Service Connect (PSC)**: GCP's private connectivity mechanism to managed services and even to other VPCs/organizations — conceptually similar to Azure Private Link, with additional support for producer/consumer service publishing patterns across organizational boundaries.
- **Global Load Balancing**: GCP's HTTP(S) Load Balancer is a true global anycast service, differing from Azure's region-scoped Application Gateway (Azure's global equivalent is Front Door) — worth noting explicitly in interviews to avoid mismapping.

## Common Mistakes
- Assuming GCP networking needs the same regional-hub-per-region topology as Azure, when a single global VPC with regional subnets may suffice.
- Not using Shared VPC, leading to per-project VPCs that need complex peering to communicate.
- Leaving Cloud NAT unconfigured and giving instances public IPs by default.

## Principal-Level Questions
- How does GCP's global VPC model change your approach to multi-region connectivity compared to Azure's regional VNet model?
- When would you still choose multiple VPCs over a single global Shared VPC?

## Scenario Question
> Design connectivity for a GCP workload spanning 4 regions that needs to reach an on-prem datacenter and remain isolated from a separate business unit's workloads in the same organization.

## Strong Answer (Outline)
1. Single Shared VPC host project with regional subnets in all 4 regions — leverages GCP's global VPC model instead of a per-region hub design.
2. Cloud Interconnect (or VPN as a fallback) from the host project's VPC to on-prem, terminated once centrally rather than per region.
3. Business unit isolation via a **separate Shared VPC / separate host project** rather than subnet-level segmentation alone, since organizational isolation is cleaner at the project/IAM boundary than purely at the network layer.
4. Cloud DNS with a private zone shared via VPC peering/Shared VPC attachment, plus forwarding rules to on-prem DNS.

## Follow-Up Questions
- How would you handle a requirement for the two business units to occasionally share specific services without merging their VPCs?
- How do you enforce firewall policy consistently across many service projects attached to one Shared VPC?

## Trade-offs
- Single global VPC: operational simplicity, but a single blast-radius/quota domain for firewall rules and routes at scale.
- Multiple VPCs per business unit: stronger isolation, more operational overhead to manage consistent policy across them.
