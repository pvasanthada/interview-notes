# DNS

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
The hierarchical, distributed naming system translating hostnames to IP addresses (and other records), foundational to nearly every cloud connectivity scenario — and, per common operational wisdom, involved in a disproportionate share of "mysterious" outages.

## Why Architects Use It
Correct DNS design (especially private DNS zones for hybrid/cloud-internal resolution) is what makes private connectivity (private endpoints, Private Link/PSC) actually work end-to-end — a private endpoint with broken DNS resolution is functionally still unreachable.

## Architecture Considerations
- **Record types relevant to cloud architecture**: A/AAAA (host resolution), CNAME (aliasing, used heavily for PaaS service private endpoint resolution), TXT (domain verification, SPF), MX (mail routing), NS (delegation).
- **Split-horizon / private DNS zones**: resolve the same hostname differently depending on whether the query originates internally (private IP, e.g., via a private endpoint) or externally (public IP) — essential for private connectivity architectures.
- **Conditional forwarding**: routes queries for specific domains to specific resolvers — the standard mechanism for hybrid on-prem/cloud DNS resolution.
- **TTL considerations**: low TTLs enable faster failover/change propagation at the cost of more query volume; balance based on how often the record legitimately changes and how fast you need failover to propagate.

## Common Mistakes
- Deploying private endpoints without corresponding private DNS zone configuration, leaving clients still resolving to the public IP.
- No conditional forwarding for hybrid environments, causing on-prem clients to fail resolving cloud-internal names (or vice versa).
- TTLs set too high on records used for failover, delaying recovery during an actual incident.

## Principal-Level Questions
- Walk through what has to be true, DNS-wise, for a private endpoint to actually route traffic privately end-to-end.
- How would you design DNS resolution for a hybrid environment spanning on-premises, Azure, and GCP?

## Scenario Question
> An application team reports their private endpoint "isn't working" — traffic still appears to leave via the public internet path.

## Strong Answer (Outline)
1. Nearly always a DNS issue rather than a networking issue at the private endpoint itself: check whether the private DNS zone (matching the PaaS service's private-endpoint DNS zone name) is linked to the VNet the client resides in.
2. Verify the client is actually resolving to the private IP (`nslookup`/`dig`) rather than the public IP — if it resolves to public, the private endpoint's DNS record either doesn't exist or isn't being served to this client.
3. Check for a conflicting public DNS override (e.g., a custom DNS server not forwarding correctly to the private zone) causing the client to bypass Azure-provided DNS resolution entirely.
4. Fix: link the correct private DNS zone to the VNet, and correct any custom DNS forwarding configuration.

## Follow-Up Questions
- How would you design this so the same application code works whether accessed from on-prem or from within the VNet?
- What monitoring would catch this class of misconfiguration proactively rather than via a support ticket?

## Trade-offs
- Split-horizon DNS: essential for private connectivity, adds operational complexity (must keep public and private views intentionally consistent for coexisting access paths where both are legitimate).
