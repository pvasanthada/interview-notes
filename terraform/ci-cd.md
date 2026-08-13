# Terraform CI/CD

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Automated pipelines that run `terraform plan` on proposed changes (typically on a pull request) and `terraform apply` on approved/merged changes, replacing manual local execution.

## Why Architects Use It
Manual local `apply` execution is a major source of both security risk (broad standing credentials on developer machines) and inconsistency (works on my machine, wrong provider version) — CI/CD makes infrastructure changes auditable, reviewable, and consistently executed.

## Architecture Considerations
- **Plan on PR, apply on merge**: standard pattern — `plan` output posted as a PR comment for human review before any `apply` runs, giving reviewers visibility into the actual blast radius of a change.
- **Least-privilege execution identity**: the CI/CD pipeline's execution identity (e.g., Azure Service Principal/Managed Identity, GCP Workload Identity Federation) should have only the permissions the specific stack needs, not broad org-wide access — mirrors the RBAC/IAM discipline in [`azure/identity-rbac-pim.md`](../azure/identity-rbac-pim.md) and [`gcp/iam.md`](../gcp/iam.md).
- **Manual approval gates for production**: production applies should require explicit human approval even after a clean plan, especially for platform-layer stacks.
- **Drift detection job**: a scheduled `plan` (no apply) against production catches manual out-of-band changes before they cause a surprising apply later (see [`drift-detection.md`](drift-detection.md)).

## Common Mistakes
- Long-lived, broadly-scoped credentials stored as CI/CD secrets instead of federated/short-lived identity.
- No mandatory plan review before apply, treating Terraform changes like any other code merge without infrastructure-specific scrutiny.
- Auto-apply on merge for production stacks with no manual gate.
- No drift detection, so manual changes silently accumulate until an unrelated apply "corrects" them unexpectedly.

## Principal-Level Questions
- How would you design a CI/CD pipeline for Terraform that's safe for junior engineers to use without risking production?
- How do you handle secrets needed during `plan`/`apply` (e.g., a database password to set as a resource attribute)?

## Scenario Question
> Design a Terraform CI/CD pipeline for a platform team supporting 30 application teams, balancing safety for production with velocity for lower environments.

## Strong Answer (Outline)
1. Dev/staging: plan-on-PR, auto-apply-on-merge — optimized for velocity given lower blast radius.
2. Production: plan-on-PR (posted for review), merge triggers a pipeline run that stops at a manual approval gate before `apply` — a named approver (platform team lead or the stack owner) must explicitly approve.
3. Execution identity scoped per environment/stack via federated workload identity (no long-lived secrets in the CI system).
4. Scheduled nightly drift-detection plan against production posted to a monitoring channel, independent of the deploy pipeline.
5. Policy-as-code check (OPA) run as part of the `plan` stage, blocking merges that would violate baseline guardrails regardless of human review outcome.

## Follow-Up Questions
- How would you handle an emergency production fix that can't wait for the normal review cycle?
- How do you prevent plan output itself from leaking sensitive values in PR comments/logs?

## Trade-offs
- Manual approval gates: safer, adds latency — justified for production/platform-layer stacks, often unnecessary friction for low-risk dev environments.
