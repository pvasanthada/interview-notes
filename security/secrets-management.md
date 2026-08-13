# Secrets & Key Management

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The practices and tooling (Azure Key Vault, GCP Secret Manager/Cloud KMS, HashiCorp Vault) for storing, rotating, and controlling access to secrets (API keys, passwords, certificates) and cryptographic keys.

## Why Architects Use It
Secrets sprawl (hardcoded, unrotated, over-shared credentials) is one of the most common real-world breach vectors — a deliberate secrets management architecture reduces this attack surface systematically.

## Architecture Considerations
- **Eliminate secrets where possible first**: workload identity (managed identity, Workload Identity Federation) removes entire categories of secrets by replacing static credentials with platform-verified identity — always the preferred first move over "store the secret better."
- **Centralized secrets store for what remains**: third-party API keys, certain database credentials not eligible for identity-based auth — stored in a managed secrets service with access via managed identity, not embedded in application config.
- **Rotation**: automated rotation wherever the downstream system supports it; for systems that don't, at minimum a defined manual rotation cadence and ownership.
- **Access auditing**: every secret access should be logged and attributable to a specific identity — critical for incident investigation.
- **Separate vaults/scopes per sensitivity tier**: don't put a low-sensitivity API key and a production database master credential in the same access-controlled scope.

## Common Mistakes
- Reaching for "put it in the secrets manager" as the default fix, without first asking whether the secret could be eliminated entirely via workload identity.
- No rotation policy — secrets set once at provisioning and never rotated for years.
- Overly broad access policies on a shared vault, granting many identities access to secrets they don't actually need.

## Principal-Level Questions
- How would you design secrets management for a platform with hundreds of microservices?
- What's your strategy for migrating an estate off long-lived static credentials?

## Scenario Question
> A security audit finds 200+ hardcoded secrets across application configs, several unrotated for over 2 years.

## Strong Answer (Outline)
1. Triage by sensitivity/blast radius; rotate and remove the highest-risk secrets immediately.
2. Classify remaining secrets: those backing Azure/GCP-to-Azure/GCP auth can be eliminated via managed identity/workload identity; genuine external secrets (third-party APIs) move to the centralized secrets store.
3. Application access to the secrets store itself goes through managed identity — never a static credential to access secrets, or you've just moved the problem.
4. Add automated rotation for everything the downstream system supports, and a tracked manual rotation cadence + ownership for what doesn't.
5. Add secret-scanning in CI/CD (pre-commit and pipeline stage) to prevent recurrence.

## Follow-Up Questions
- How do you handle secrets needed by workloads outside your cloud (on-prem, another cloud)?
- What's your incident response process if a secret is confirmed leaked?

## Trade-offs
- Aggressive elimination of secrets via workload identity: strongest security posture, requires the downstream system to support federated/managed identity auth — not universally available for legacy or third-party systems.
