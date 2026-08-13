# Scenario: Terraform State/Drift Incident

**Label:** `Interview Preparation`

## Prompt
> A routine `terraform apply` for an unrelated change unexpectedly destroyed and recreated a production database, causing a brief outage. Diagnose and prevent recurrence.

## Context
A change intended to be scoped/low-risk had an unexpectedly large blast radius — see [`terraform/architecture.md`](../terraform/architecture.md), [`terraform/drift-detection.md`](../terraform/drift-detection.md).

## Problem
Understand why an unrelated change caused destroy/recreate of a critical resource, and redesign to prevent recurrence.

## Analysis
Terraform destroy/recreate typically happens when a resource attribute that requires replacement (not just in-place update) changes — this can happen intentionally (an actual required config change) or, commonly, due to drift: the resource's real-world state had diverged from Terraform's last-known state (e.g., a manual out-of-band change), and the `apply` "corrected" it in a way that forced replacement.

## Options
Diagnostic hypotheses:
1. The apply itself included a change to a replacement-forcing attribute that wasn't reviewed carefully in the plan output.
2. Drift existed (an out-of-band manual change) that the plan silently "corrected" via replacement.
3. A module version bump changed a resource's underlying configuration in a way that forced replacement.

## Decision (Diagnostic Approach)
1. Review the actual `terraform plan` output from the incident (should be preserved in CI/CD pipeline logs, per [`terraform/ci-cd.md`](../terraform/ci-cd.md)) to see exactly what attribute triggered the destroy/recreate.
2. Cross-reference with cloud provider audit logs around the same timeframe for any manual changes to that resource that might explain drift.
3. If it was drift-driven, this reveals a gap in drift detection (per [`terraform/drift-detection.md`](../terraform/drift-detection.md)) — the drift should have been caught and investigated before an unrelated apply "corrected" it destructively.
4. If it was a module version change, this reveals a gap in module upgrade review — the plan diff should have been scrutinized more carefully before merging, especially for any resource marked for replacement, not just addition/modification.

## Remediation
- Add `prevent_destroy` lifecycle protection on genuinely critical resources (like production databases) as a hard safeguard against unintended replacement, requiring a deliberate override if replacement is ever truly intended.
- Add a CI/CD policy check that flags any plan containing a destroy/recreate of a tagged "critical" resource for mandatory extra review/approval, regardless of the change's stated intent.
- Strengthen drift detection (scheduled `plan`-only runs) so drift is caught and investigated proactively, not discovered destructively during an unrelated apply.

## Trade-offs
- `prevent_destroy` adds friction for genuinely intended replacements (requires deliberate lifecycle override), an acceptable trade for critical resources given the cost of an accidental one.

## Outcome (Illustrative)
Root cause: an out-of-band manual change (an emergency fix made weeks earlier, never reflected back into Terraform configuration) created drift; the unrelated apply's plan included the database's replacement to "correct" that drift, and the destructive plan line was missed during review due to its length and unrelated context.

## Lessons Learned
- `prevent_destroy` on critical resources and a policy-as-code gate flagging destroy/recreate operations for critical resources are both cheap, high-value safeguards that should be standard practice, not added only after an incident.
- This incident also reinforces the drift-detection lesson in [`terraform/drift-detection.md`](../terraform/drift-detection.md): manual emergency fixes must be reflected back into configuration promptly, not left as silent drift.
