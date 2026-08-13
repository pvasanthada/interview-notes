# Azure Governance & Policy

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
Azure Policy (deny/audit/append/modify effects), initiatives (policy sets), and management-group-level assignment used to enforce and continuously assess compliance across the estate — the technical mechanism behind cloud governance.

## Why Architects Use It
Turns governance from a manual review process into an enforced, auditable, and continuously assessed control — essential once an organization exceeds a handful of subscriptions.

## Architecture Considerations
- **Audit-then-enforce rollout**: never go straight to `deny` in an existing environment; start with `audit`, review the blast radius, then enforce.
- **Initiatives over individual policies**: group related policies (e.g., "PCI baseline") into an initiative assigned once, rather than dozens of individual assignments.
- **Exemptions with expiry**: legitimate exceptions should be time-bound and tracked, not permanent silent overrides.
- **Policy-as-code**: manage policy definitions/assignments through IaC (Terraform/Bicep) with review, not manually through the portal.

## Common Mistakes
- Enforcing deny-mode policy without first auditing impact, breaking existing production deployments.
- Policy sprawl with no ownership model — nobody knows why a policy exists or whether it's still needed.
- Using policy for things better solved by RBAC (policy is for resource configuration state, not access control).

## Principal-Level Questions
- How would you roll out a mandatory tagging policy across an organization with 300 existing untagged resources?
- How do you balance policy enforcement speed against the risk of breaking existing workloads?

## Scenario Question
> Leadership wants all storage accounts to deny public network access org-wide within 30 days. Some legacy workloads currently depend on public access. Plan the rollout.

## Strong Answer (Outline)
1. Deploy the policy in `audit` mode immediately to get a full inventory of non-compliant resources.
2. Triage: workloads that can move to private endpoints quickly vs. ones with genuine short-term dependency.
3. Grant time-bound policy exemptions for the latter with a remediation deadline and named owner.
4. Migrate compliant-capable workloads first, remediate remaining ones, then flip to `deny`.
5. Add a pre-deployment check (CI/CD policy validation) to prevent regression.

## Follow-Up Questions
- How would you handle a business-critical legacy system that genuinely cannot move off public access in the timeframe?
- How do you keep the policy set itself from becoming unmanageably large?

## Trade-offs
- Fast enforcement reduces risk exposure time but raises the chance of breaking production; phased rollout is safer but slower.
- Exemptions add governance flexibility but require active tracking or they become permanent unaudited holes.
