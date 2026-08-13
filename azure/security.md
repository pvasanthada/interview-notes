# Azure Security

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
Platform-level security capabilities: Key Vault (secrets/keys/certs), Defender for Cloud (CSPM + workload protection), encryption at rest/in transit, and network security controls.

## Why Architects Use It
Provides the technical controls that implement Zero Trust and compliance requirements consistently across a large estate, rather than per-application bespoke security.

## Architecture Considerations
- **Key Vault**: use managed identity for access (never embed Key Vault credentials); separate vaults per environment/sensitivity tier; enable soft-delete and purge protection.
- **Defender for Cloud**: enables Cloud Security Posture Management (secure score, misconfiguration detection) plus optional workload protection plans (servers, containers, SQL, storage) — treat the secure score as a leading indicator, not a vanity metric.
- **Encryption**: platform-managed keys by default; customer-managed keys (CMK) only when compliance requires it, understanding the added operational burden (key rotation, availability dependency).
- **CSPM as continuous compliance**: map Defender for Cloud regulatory compliance dashboards to actual frameworks required (PCI, ISO 27001, etc.) rather than treating it as generic hygiene.

## Common Mistakes
- Storing secrets in app config/environment variables instead of Key Vault.
- Ignoring Defender for Cloud recommendations for months, letting secure score decay silently.
- Enabling customer-managed keys everywhere "for security" without understanding the operational cost and availability risk.
- Treating security tooling as a checkbox rather than wiring alerts into an actual response process.

## Principal-Level Questions
- How would you design secrets management for a platform with hundreds of microservices across multiple environments?
- How do you decide when customer-managed keys are actually justified vs. added risk?
- How would you operationalize Defender for Cloud recommendations across 40 subscriptions without alert fatigue?

## Scenario Question
> A security audit finds 200+ secrets hardcoded across application configs. Design a remediation and prevention strategy.

## Strong Answer (Outline)
1. Triage by sensitivity/blast radius — rotate and remove the highest-risk secrets first.
2. Migrate application access to managed identity wherever the workload runs on Azure compute, eliminating entire classes of secrets.
3. For remaining genuine secrets (third-party API keys), move to Key Vault with app access via managed identity + Key Vault reference, not direct retrieval-and-store.
4. Add a pre-commit/CI secret-scanning gate to prevent recurrence.
5. Establish rotation policy and ownership per secret.

## Follow-Up Questions
- How do you handle secrets needed by workloads outside Azure (on-prem, other clouds)?
- What's your rollback plan if a mass credential rotation breaks something in production?

## Trade-offs
- CMK gives more control but adds availability dependency and operational overhead — justify per compliance need, not by default.
- Aggressive secret-scanning gates reduce risk but can slow developer velocity if false-positive rates aren't tuned.
