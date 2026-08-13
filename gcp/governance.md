# GCP Governance — Organization Policy

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
Organization Policy Service: constraints applied at organization/folder/project scope that restrict what configurations are allowed (e.g., disable external IPs, restrict resource locations) — GCP's conceptual counterpart to Azure Policy, though mechanically simpler (boolean/list constraints rather than Azure's more expressive JSON policy language with custom effects).

## Why Architects Use It
Enforces guardrails structurally across the organization hierarchy, same governance goal as Azure Policy — critical once an org has more than a handful of projects.

## Architecture Considerations
- **Managed constraints vs. custom constraints**: prefer Google's predefined managed constraints (broad library covering common needs) before reaching for custom constraints (more flexible, more maintenance).
- **Inheritance**: constraints set at folder level apply to all projects beneath, same inheritance model as Azure management groups — design the folder hierarchy with this in mind.
- **Dry-run before enforce**: similar discipline to Azure's audit-before-deny — test constraint impact before organization-wide enforcement.
- **Common high-value constraints**: restrict resource locations (data residency), disable default network creation, disable service account key creation, restrict public IP assignment.

## Common Mistakes
- Applying broad constraints at the organization root without considering legitimate exceptions in sandbox/dev folders.
- Not using resource location constraints, leading to accidental data residency violations.
- Treating Organization Policy as a substitute for IAM (it constrains configuration, not who can access what).

## Principal-Level Questions
- How would you enforce data residency across a multi-region GCP deployment using Organization Policy?
- How do you balance sandbox/experimentation freedom against organization-wide guardrails?

## Scenario Question
> A regulator requires all customer data to remain within EU regions. Enforce this technically, not just by process.

## Strong Answer (Outline)
1. Apply the `gcp.resourceLocations` constraint at the folder level containing EU-customer-data workloads, restricting allowed resource locations to EU regions.
2. Separate folder structure so EU-data workloads are structurally isolated from global/other-region folders, making the constraint boundary unambiguous.
3. Pair with Cloud Asset Inventory scans to detect any pre-existing non-compliant resources and VPC Service Controls if data exfiltration outside the perimeter is also a concern.
4. Document as an auditable control mapped directly to the regulatory requirement for compliance evidence.

## Follow-Up Questions
- How would you handle a legitimate need for a global service (e.g., a CDN) that technically touches non-EU infrastructure?
- How do you continuously verify the constraint hasn't been exempted or bypassed?

## Trade-offs
- Strict location constraints: strong compliance guarantee, can complicate genuinely global services and disaster recovery design.
