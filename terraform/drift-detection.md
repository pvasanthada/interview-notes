# Terraform Drift Detection

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
The practice of detecting when real infrastructure state diverges from what Terraform's configuration/state believes it to be — typically caused by manual out-of-band changes (console/portal edits, another automation tool, or an emergency fix that bypassed the pipeline).

## Why Architects Use It
Undetected drift causes two problems: it silently defeats the "configuration is the source of truth" guarantee IaC is meant to provide, and it creates dangerous surprises when a future legitimate `apply` "corrects" the drifted resource, potentially reverting an intentional emergency change or causing unexpected downtime.

## Architecture Considerations
- **Scheduled drift-detection plans**: run `terraform plan` (never apply) on a schedule against production stacks, alerting when a non-empty diff appears unexpectedly.
- **Root-cause the drift, don't just reconcile it blindly**: before running `apply` to "fix" drift, understand *why* it happened — it may represent a legitimate emergency change that needs to be reflected back into configuration, not overwritten.
- **Reduce drift at the source**: the best fix for chronic drift is usually removing the ability/need for manual changes (via strict RBAC, breaking console access for managed resources) rather than only detecting it after the fact.
- **Import workflow**: for resources that were created outside Terraform and need to be brought under management, use `terraform import` (or the newer `import` block) deliberately rather than deleting and recreating.

## Common Mistakes
- No scheduled drift detection at all — drift discovered only when an unrelated `apply` unexpectedly modifies a resource.
- Blindly running `apply` to "fix" drift without investigating whether the manual change was an intentional emergency fix.
- Broad console/portal access left open for engineers even after adopting Terraform, guaranteeing recurring drift.

## Principal-Level Questions
- How would you build a drift-detection strategy for a large multi-team Terraform estate?
- What's your process when drift is detected — how do you decide whether to reconcile toward configuration or update configuration to match reality?

## Scenario Question
> A nightly drift-detection job flags that a production database's firewall rules don't match Terraform configuration. Investigate and respond.

## Strong Answer (Outline)
1. Investigate first: check change history/audit logs (Azure Activity Log/GCP Audit Logs) to identify who/what made the change and when.
2. Determine intent: if it was an emergency fix (e.g., temporarily allowing an IP during an incident) that's no longer needed, reconcile by running `apply` to restore the Terraform-defined state. If it reflects a legitimate, still-needed change, update the Terraform configuration to match reality instead, then apply that as a reviewed change.
3. Root-cause why the out-of-band change was possible/necessary — if emergency console access was required because the standard pipeline was too slow for an active incident, that's a process gap worth fixing (e.g., a fast-tracked emergency-change pipeline) rather than just tightening access further.
4. Communicate the finding and resolution to the resource owner and, if it reveals a broader access-control gap, to the security team.

## Follow-Up Questions
- How would you prevent this specific class of drift from recurring?
- How do you scale drift detection across hundreds of stacks without excessive noise or cost?

## Trade-offs
- Aggressive automated drift reconciliation (auto-apply on detected drift): fast, risky if it silently reverts a legitimate emergency change.
- Alert-and-review drift detection: safer, requires human bandwidth to triage regularly.
