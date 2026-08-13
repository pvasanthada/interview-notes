# Private Connectivity — Private Endpoints, Private Link, PSC

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
Mechanisms that let a consumer network privately reach a PaaS/managed service (or another VNet/VPC's services) via a private IP within the consumer's own network, without traffic ever traversing the public internet — Azure Private Link/Private Endpoint, GCP Private Service Connect (PSC).

## Why Architects Use It
Removes public network exposure for sensitive services entirely while retaining full PaaS convenience — a strong default for compliance-sensitive workloads (see [`azure/networking.md`](../azure/networking.md), [`gcp/networking.md`](../gcp/networking.md)).

## Architecture Considerations
- **Private endpoint = a NIC in your VNet**: Azure Private Endpoint effectively projects the service's private IP directly into your VNet's address space — requires corresponding private DNS zone configuration to actually resolve to that IP (see [`dns.md`](dns.md)).
- **PSC's producer/consumer model**: GCP PSC additionally supports publishing your *own* service for private consumption by other projects/organizations (not just consuming Google-managed services) — a distinctive capability for building private, cross-organization service integrations without any public exposure or VPC peering.
- **Combine with public access disablement**: a private endpoint alone doesn't guarantee isolation unless the service's public network access is also explicitly disabled — otherwise both paths remain valid.
- **Scale considerations**: private endpoints/PSC add per-service networking objects to manage at scale — automate provisioning via IaC modules (see [`terraform/modules.md`](../terraform/modules.md)) rather than manual per-service setup.

## Common Mistakes
- Enabling a private endpoint but leaving the service's public network access enabled, defeating the isolation intent.
- Forgetting private DNS zone linkage, leaving clients still resolving to the public endpoint.
- Not automating private endpoint provisioning, causing inconsistent adoption across an estate.

## Principal-Level Questions
- What has to be true, end-to-end, for a private endpoint to actually eliminate public exposure of a service?
- How would you enforce private-connectivity-only access consistently across hundreds of PaaS services?

## Scenario Question
> A compliance audit finds several PaaS services with private endpoints configured but still reachable via their public endpoint.

## Strong Answer (Outline)
1. Root cause: private endpoint creation and public-access disablement are separate settings — teams enabled the former without the latter.
2. Immediate remediation: disable public network access on the flagged services, validating no legitimate traffic still depends on the public path first (check access logs).
3. Systemic fix: enforce via policy-as-code (Azure Policy/Terraform module default, see [`azure/governance-policy.md`](../azure/governance-policy.md)) that public network access is denied by default for any service with a private endpoint, rather than relying on manual configuration discipline.
4. Add a continuous compliance check (policy or scheduled scan) to catch regression going forward.

## Follow-Up Questions
- How would you handle a legitimate need for both public (e.g., a public API) and private (internal callers) access to the same service?
- How do you scale private DNS zone management across many VNets/subscriptions?

## Trade-offs
- Private-only access: strongest isolation, requires all legitimate callers to have private network connectivity (VPN/ExpressRoute for on-prem/remote callers) — a real operational dependency to plan for.
