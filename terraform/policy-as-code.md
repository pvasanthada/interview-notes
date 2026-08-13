# Terraform Policy as Code

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Automated policy enforcement evaluated against a Terraform plan (or applied state) before changes are allowed to proceed — implemented via tools like Open Policy Agent (OPA)/Conftest, Terraform's native Sentinel (Terraform Cloud/Enterprise), or cloud-native policy engines layered on top of the deployed resources (Azure Policy, GCP Organization Policy).

## Why Architects Use It
Turns architectural/security/compliance standards into automated, consistently-enforced gates instead of relying on manual code review to catch every violation — essential once an organization has more Terraform authors than any single reviewer can meaningfully audit.

## Architecture Considerations
- **Plan-time policy (OPA/Sentinel) vs. deployed-resource policy (Azure Policy/Org Policy)**: plan-time policy catches issues before anything is created (fastest feedback, prevents the resource from ever existing non-compliantly); deployed-resource policy is a necessary backstop for anything created outside Terraform or via a path plan-time policy didn't cover — mature platforms use both layers.
- **Policy as its own versioned, tested artifact**: treat policy rules with the same rigor as module code — versioned, tested, reviewed changes, not ad hoc scripts.
- **Soft-mandatory vs. hard-mandatory enforcement**: some policies should warn (advisory) before being escalated to blocking, giving teams time to adapt rather than breaking pipelines with zero notice.

## Common Mistakes
- Relying solely on deployed-resource policy (e.g., Azure Policy) with no plan-time check, so non-compliant resources are created and then flagged/remediated after the fact rather than prevented.
- Writing overly broad policies that block legitimate use cases, causing teams to seek workarounds outside Terraform entirely.
- No staged rollout (straight to hard-blocking) for new policies, breaking existing pipelines without warning.

## Principal-Level Questions
- How would you design a policy-as-code strategy spanning both Terraform plan-time checks and deployed-resource enforcement (Azure Policy/Org Policy)?
- How do you roll out a new mandatory policy across an organization without breaking existing pipelines?

## Scenario Question
> Leadership mandates that all new storage resources must have encryption and private network access enabled, enforced automatically, not by code review.

## Strong Answer (Outline)
1. Write an OPA/Conftest policy evaluated against the `terraform plan` JSON output in CI, checking storage resource blocks for the required encryption and network settings.
2. Roll out in advisory (warn, non-blocking) mode first, giving teams a window to remediate existing non-compliant configurations flagged by the same check.
3. Escalate to blocking mode after the remediation window, preventing any new non-compliant plan from being applied.
4. Layer a deployed-resource policy (Azure Policy/GCP Org Policy) as a backstop catching anything created outside this Terraform pipeline (e.g., portal/console changes).
5. Track compliance metrics over the rollout to demonstrate progress to leadership.

## Follow-Up Questions
- How would you handle a legitimate exception (e.g., a legacy system that can't yet meet the new policy)?
- How do you keep policy rules from drifting out of sync with actual compliance requirements over time?

## Trade-offs
- Plan-time policy: fastest feedback, doesn't catch changes made outside Terraform.
- Deployed-resource policy: catches everything regardless of origin, feedback comes after the fact (detect-and-remediate rather than prevent).
