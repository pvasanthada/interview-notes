# GCP IAM

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
GCP's access control system: predefined/custom roles bound to members (users, groups, service accounts) at organization/folder/project/resource scope, plus Workload Identity Federation for keyless service-to-service and external-workload authentication.

## Why Architects Use It
Same fundamental purpose as Azure RBAC — least-privilege access control — with GCP-specific tools (Workload Identity Federation, IAM Conditions) that are worth knowing precisely for interviews given they solve the "no long-lived credentials" goal differently than Azure Managed Identity.

## Architecture Considerations
- **Predefined roles over primitive roles**: avoid Owner/Editor/Viewer (primitive roles, very broad) in favor of predefined roles scoped to a specific service/action set — conceptually parallel to preferring Azure built-in roles over Owner/Contributor.
- **Workload Identity Federation**: allows external workloads (other clouds, CI/CD systems, on-prem) to authenticate to GCP using their native identity, without a downloaded service account key — this is GCP's strongest tool for eliminating long-lived credentials, directly comparable in intent to Azure Managed Identity but architected for federation across identity providers rather than being GCP-compute-bound only.
- **Service accounts**: prefer attaching a service account directly to the compute resource (GCE/GKE/Cloud Run) over distributing service account keys; disable key creation via Organization Policy where feasible.
- **IAM Conditions**: attribute-based conditions (time-bound, resource-tag-based) added on top of role bindings for finer-grained control, comparable in purpose to Azure ABAC conditions on role assignments.

## Common Mistakes
- Distributing downloadable service account keys instead of using Workload Identity Federation or attached service accounts.
- Using primitive roles (Editor) broadly because a predefined role wasn't obviously discoverable.
- No process to rotate or audit service account keys that do exist.

## Principal-Level Questions
- How would you eliminate all long-lived service account keys across a GCP organization?
- How does Workload Identity Federation compare architecturally to Azure Managed Identity, and where does each still require a fallback?

## Scenario Question
> A security review finds 300 active downloadable service account keys across your GCP organization, several over 2 years old. Remediate.

## Strong Answer (Outline)
1. Inventory and classify: keys used by GCP-hosted workloads (can move to attached service accounts) vs. external workloads (CI/CD, other clouds — can move to Workload Identity Federation) vs. genuinely unavoidable legacy cases.
2. Migrate GCP-hosted workloads to attached service accounts first — no application code change needed for identity, only removing key-based auth.
3. Migrate CI/CD and cross-cloud cases to Workload Identity Federation, mapping external identity providers (GitHub Actions OIDC, AWS IAM, Azure AD) to GCP service accounts.
4. Enforce an Organization Policy constraint disabling new service account key creation once migration is substantially complete, with a narrow, monitored exception process.
5. Rotate/disable remaining old keys and set up alerting on key usage going forward.

## Follow-Up Questions
- How would you handle a legacy on-prem system that can't support federated identity at all?
- How do you monitor for anomalous service account usage after this remediation?

## Trade-offs
- Workload Identity Federation adds initial setup complexity (trust configuration per external IdP) but removes an entire class of long-lived credential risk — worth the investment at any meaningful scale.
