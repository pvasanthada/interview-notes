# GCP Security

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
Security Command Center (CSPM + threat detection, GCP's rough equivalent of Defender for Cloud), default encryption at rest, CMEK (customer-managed encryption keys via Cloud KMS), and VPC Service Controls for data exfiltration protection.

## Why Architects Use It
Provides organization-wide visibility into misconfigurations and threats, plus a distinctive GCP capability — VPC Service Controls — for constraining data movement across API-based service boundaries, which has no exact Azure equivalent.

## Architecture Considerations
- **Security Command Center**: Standard tier gives basic CSPM; Premium adds threat detection, container/web security scanning — decide tier based on actual compliance/threat-detection requirements, not by default.
- **CMEK via Cloud KMS**: use for compliance-driven key control requirements, understanding the same operational trade-off as Azure CMK (availability dependency, rotation responsibility).
- **VPC Service Controls**: creates a service perimeter around GCP APIs/resources (e.g., Cloud Storage, BigQuery) to prevent data exfiltration even by an identity with valid IAM permissions but from outside the perimeter — a strong defense-in-depth layer distinct from IAM alone.
- **Binary Authorization**: for GKE/Cloud Run, enforce that only signed/attested container images can deploy — supply-chain integrity control.

## Common Mistakes
- Relying on IAM alone for data protection and not considering VPC Service Controls for genuinely sensitive data stores.
- Enabling Security Command Center but never triaging or acting on findings.
- Treating CMEK as a default "more secure" choice without weighing the operational cost.

## Principal-Level Questions
- How would you prevent a compromised, IAM-valid identity from exfiltrating data out of your GCP organization?
- How would you implement Zero Trust across Azure and GCP consistently?

## Scenario Question
> Design a defense-in-depth strategy protecting a BigQuery dataset containing sensitive customer data, assuming an attacker may obtain valid but limited IAM credentials.

## Strong Answer (Outline)
1. IAM least privilege on the dataset (baseline, but assumed already potentially compromised in this scenario).
2. VPC Service Controls perimeter around the project/dataset so API access is restricted to defined networks/identities even with valid IAM — this is the layer that specifically addresses "credentials are valid but should still be blocked."
3. CMEK on the dataset for an additional key-control layer if compliance requires it.
4. Data access logging (Cloud Audit Logs, Data Access logs specifically) with alerting on anomalous query volume or unusual export patterns.
5. Column/row-level security within BigQuery for further least-privilege at the data layer itself.

## Follow-Up Questions
- How would you extend this pattern consistently to an Azure counterpart, given Azure lacks a direct VPC Service Controls equivalent?
- What's the operational cost of VPC Service Controls in terms of legitimate cross-project workflows breaking?

## Trade-offs
- VPC Service Controls: strong exfiltration protection, real risk of breaking legitimate cross-perimeter access if not carefully scoped — requires a deliberate rollout with dry-run mode first.
