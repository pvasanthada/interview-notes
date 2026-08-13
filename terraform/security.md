# Terraform Security

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
The practices protecting Terraform's execution path itself: credential management for provider authentication, secrets handling within configuration/state, and least-privilege execution identity.

## Why Architects Use It
Terraform's execution identity is frequently among the most powerful credentials in an organization (it can create/modify/delete infrastructure broadly) — securing this path is as important as securing the infrastructure it manages.

## Architecture Considerations
- **Federated/short-lived credentials over static secrets**: use Azure Workload Identity Federation / GCP Workload Identity Federation from CI/CD to Terraform's provider auth, avoiding long-lived service principal secrets or downloaded service account keys.
- **Least-privilege execution roles per stack**: the identity running Terraform for a given stack should have only the RBAC/IAM permissions that stack's resources require, not org-wide Owner/Editor.
- **Sensitive variables**: mark sensitive inputs/outputs (`sensitive = true`) to prevent accidental logging, but don't treat this as sufficient alone — sensitive values still land in state and must be protected there too (see [`state-management.md`](state-management.md)).
- **Avoid secrets in configuration**: generate secrets at apply time (e.g., via a `random_password` resource) or reference an external secret store (Key Vault/Secret Manager) rather than hardcoding values in `.tfvars`.

## Common Mistakes
- Long-lived static credentials for Terraform's cloud provider auth stored as CI/CD secrets, never rotated.
- One shared, broadly-privileged execution identity for all stacks regardless of what each stack actually needs.
- Secrets checked into `.tfvars` files in version control.
- Assuming `sensitive = true` fully protects a value, forgetting it's still present in plaintext state.

## Principal-Level Questions
- How would you design least-privilege execution identities for a platform with 40 independently-owned Terraform stacks?
- How do you prevent secrets from ending up in Terraform state or logs?

## Scenario Question
> A security review finds your CI/CD system holds a single, long-lived Terraform service principal with Owner access across the entire Azure tenant.

## Strong Answer (Outline)
1. Migrate to workload identity federation (OIDC-based) between the CI/CD platform and Azure Entra ID, eliminating the long-lived secret entirely.
2. Decompose the single broad identity into per-stack (or per-environment) service principals/app registrations, each scoped via RBAC to only the resource groups/subscriptions that stack manages.
3. Apply PIM-style time-bound elevation for any genuinely rare cross-cutting operations that can't be pre-scoped.
4. Audit historical usage of the old credential to confirm no unauthorized use occurred, then decommission it.

## Follow-Up Questions
- How would you handle a stack that genuinely needs cross-subscription permissions (e.g., a landing-zone-provisioning stack)?
- How do you validate the new scoped identities aren't accidentally too restrictive, breaking legitimate applies?

## Trade-offs
- Fine-grained per-stack identities: much stronger security posture, more identities to manage/rotate/audit — worth automating identity provisioning itself as part of the stack's bootstrap.
