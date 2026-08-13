# Cloud Security Fundamentals

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
The foundational model underpinning cloud security architecture: the shared responsibility model (what the cloud provider secures vs. what the customer secures, which shifts by service type — IaaS vs PaaS vs SaaS), and encryption practices (at rest, in transit, and increasingly in use).

## Why Architects Use It
Misunderstanding the shared responsibility boundary is a leading cause of real-world cloud breaches (e.g., assuming the provider secures data access configuration on a PaaS service, when that remains the customer's responsibility) — getting this model right shapes the entire security architecture.

## Architecture Considerations
- **Shared responsibility shifts with abstraction level**: IaaS (VMs) — customer responsible for OS patching, network config, data; PaaS — provider manages the underlying platform, customer still responsible for access configuration, data, and application-layer security; SaaS — provider manages nearly everything except data and access configuration.
- **Encryption at rest**: platform-managed keys as the default (lower operational burden); customer-managed keys (CMK) when compliance mandates explicit key control, understanding the added availability/rotation responsibility this creates.
- **Encryption in transit**: TLS enforced everywhere by default, including internal service-to-service traffic in Zero Trust designs — not just at the public-facing edge.
- **Encryption in use (confidential computing)**: an emerging capability (e.g., Azure confidential VMs, GCP Confidential Computing) protecting data even while being processed in memory — relevant for the highest-sensitivity workloads, not yet a default expectation.

## Common Mistakes
- Assuming the cloud provider secures data access configuration on a PaaS service by default (a very common real-world source of public data exposure incidents — e.g., publicly accessible storage buckets/containers).
- Treating encryption at rest as sufficient security by itself, without also addressing access control (encrypted data with broad, poorly-controlled access provides limited real protection).
- Adopting CMK broadly "for security" without understanding the operational cost and availability coupling it introduces.

## Principal-Level Questions
- Explain exactly where the shared responsibility line falls for a specific PaaS service, and what commonly gets missed.
- When would you actually need confidential computing, versus standard encryption at rest/in transit being sufficient?

## Scenario Question
> A public-facing storage container was found to be misconfigured for public read access, exposing customer data for an unknown period.

## Strong Answer (Outline)
1. Immediate: disable public access, rotate any credentials/data that may have been exposed, and begin an incident investigation (access logs to determine actual exposure/exfiltration, not just theoretical exposure window).
2. Root cause: this class of incident is a classic shared-responsibility gap — the platform secured the storage service's infrastructure, but access configuration (a customer responsibility) was misconfigured.
3. Systemic fix: enforce via policy-as-code that public access is denied by default org-wide (see [`azure/governance-policy.md`](../azure/governance-policy.md), [`gcp/governance.md`](../gcp/governance.md)), rather than relying on manual configuration discipline per resource.
4. Add continuous CSPM scanning (see [`cspm.md`](cspm.md)) to catch any future drift toward public exposure immediately, not at the next scheduled audit.
5. Regulatory/customer notification process as required by the nature and scope of the data exposed.

## Follow-Up Questions
- How would you determine actual data exposure/exfiltration versus theoretical exposure?
- How do you balance "deny public access by default" against legitimate use cases (e.g., a public asset CDN origin)?

## Trade-offs
- Deny-by-default public access: prevents the most common real-world misconfiguration class, requires an explicit, reviewed exception process for genuine public-access use cases.
