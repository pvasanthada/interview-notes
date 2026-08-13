# Terraform State Management

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Terraform state (the JSON record mapping configuration to real resource IDs), remote state backends (e.g., Azure Storage, GCS, Terraform Cloud), state locking (preventing concurrent conflicting applies), and workspaces (a mechanism for managing multiple state instances from one configuration).

## Why Architects Use It
State is the single most operationally critical artifact in a Terraform setup — losing or corrupting it effectively loses Terraform's ability to safely manage the infrastructure it describes, even though the infrastructure itself still exists.

## Architecture Considerations
- **Remote state, always**: local state files are unacceptable beyond individual experimentation — no locking, no team access, high loss risk.
- **State locking**: prevents two concurrent `apply` operations from corrupting state — essential in any team/CI context (Azure Storage native lease-based locking, GCS with a lock mechanism, or Terraform Cloud's built-in locking).
- **State encryption**: state can contain sensitive values (e.g., generated passwords) in plaintext — ensure the backend encrypts at rest, and treat state access itself as a privileged operation.
- **Separate state per stack/environment**: state should be scoped to match the blast-radius scoping discussed in [`architecture.md`](architecture.md) — one state file per environment/layer, not one giant shared state.

## Terraform Workspaces vs. Separate State Files/Configurations

> **Key interview question**: When should you use Terraform workspaces versus separate state files/configurations?

**Strong architect-level answer:**

Terraform workspaces are best suited for **near-identical environments that differ only in a small number of variable values** and are managed by the same team with the same lifecycle — for example, quick ephemeral feature-branch environments built from the same configuration. They're lightweight because they share the same configuration and backend, differing only in a separate state file per workspace.

**Separate state files/configurations (distinct root modules, potentially in separate repos or directories) are the better choice when:**
- Environments have genuinely different configurations, not just different variable values (e.g., prod has additional DR resources that dev doesn't).
- Environments are owned/approved by different teams or need different access control — workspaces share the same backend permissions, making it hard to grant prod-only access separately from dev.
- Blast radius matters — a mistake in one workspace's `apply` is easy to run against the wrong workspace accidentally (a well-documented workspace footgun); fully separate configurations with separate state make this much harder to do by accident.
- Environments need independent apply cadences/pipelines (e.g., prod requiring manual approval, dev auto-applying).

**In practice**, most mature platform teams use **separate state per environment via distinct configuration roots** (often templated by a shared module) rather than workspaces, specifically to get the stronger access-control and blast-radius isolation — reserving workspaces for genuinely ephemeral, low-stakes variants.

## Common Mistakes
- Using workspaces to separate dev/staging/prod and then accidentally applying against prod because the current workspace wasn't checked.
- Local state files committed to version control (or not backed up at all).
- No state locking, leading to concurrent applies corrupting state during a CI race condition.
- Sensitive values left unencrypted in a state backend with overly broad access.

## Principal-Level Questions
- Walk through how you'd recover from a corrupted or lost Terraform state file for a critical production stack.
- How do you enforce that only authorized pipelines/identities can run `apply` against production state?

## Scenario Question
> An engineer accidentally ran `terraform apply` against production while believing they were in a dev workspace, causing unintended changes.

## Strong Answer (Outline)
1. Immediate: assess actual changes made (via `terraform plan`/state diff and cloud provider audit logs) and remediate any unintended resource changes.
2. Root cause: workspace-based environment separation makes this exact mistake easy — migrate toward separate configuration roots/state per environment with distinct backend access controls, so "prod" isn't just a workspace name away from "dev."
3. Add a pipeline-enforced control: `apply` against production state only runs through CI/CD with a manual approval gate, never from a local developer machine.
4. Add a state-backend-level safeguard (e.g., separate storage account/bucket per environment with separate IAM/RBAC) so accessing prod state at all requires distinct, audited credentials.

## Follow-Up Questions
- How would you design least-privilege access to state backends across many environments?
- What's your approach to state file backup/versioning in case of corruption?

## Trade-offs
- Workspaces: fast to set up, weaker isolation — appropriate for low-stakes, ephemeral use.
- Separate configurations/state: stronger isolation and access control, more initial setup and some duplication to manage (mitigated via shared modules).
