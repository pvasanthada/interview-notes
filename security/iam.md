# Cross-Cloud Identity Architecture

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
The design of identity, authentication, and authorization consistently across a multi-cloud (Azure + GCP, potentially more) environment — building on the platform-specific mechanisms in [`azure/identity-rbac-pim.md`](../azure/identity-rbac-pim.md) and [`gcp/iam.md`](../gcp/iam.md).

## Why Architects Use It
Multi-cloud environments risk inconsistent access models (different least-privilege discipline, different credential lifecycle practices per cloud) unless identity architecture is deliberately unified rather than left to grow independently per platform.

## Architecture Considerations
- **Single source of identity truth**: a central IdP (e.g., Entra ID, or another federated IdP) as the authoritative source, federated into each cloud's native identity system, rather than separate user directories per cloud.
- **Federation over duplication**: Workload Identity Federation (both Azure's and GCP's implementations) lets workloads in one cloud (or CI/CD systems) authenticate to another cloud without maintaining separate long-lived credentials per cloud.
- **Consistent least-privilege discipline**: apply the same policy (no standing privileged access, JIT elevation) across both clouds' native mechanisms (PIM in Azure, time-bound IAM Conditions in GCP), even though the implementation details differ.
- **Unified audit visibility**: aggregate identity/access audit logs (Entra ID sign-in logs, GCP Cloud Audit Logs) into a central SIEM for cross-cloud correlation.

## Common Mistakes
- Separate, drifting identity practices per cloud — strong PIM discipline in Azure, standing broad access in GCP (or vice versa) because ownership of the two clouds sits with different teams.
- Duplicating user/service identities per cloud instead of federating from one source, doubling the offboarding/lifecycle burden.
- No unified visibility, so a security team investigating an incident has to separately query two entirely different audit log systems.

## Principal-Level Questions
- How would you design identity for a multi-cloud enterprise, ensuring consistent least-privilege enforcement across Azure and GCP?
- How would you prevent privilege escalation across cloud boundaries (e.g., a compromised Azure identity being used to pivot into GCP)?

## Scenario Question
> How would you design identity for a multi-cloud enterprise spanning Azure and GCP with 5,000 employees and hundreds of workloads?

## Strong Answer (Outline)
1. Entra ID as the central IdP for human identity, federated into GCP via Workload Identity Federation / SSO integration — no duplicate GCP-native user accounts for employees.
2. Least-privilege baseline enforced consistently: PIM for Azure privileged roles, time-bound IAM Conditions for equivalent GCP roles, both governed by the same access-review cadence and policy (even though tooling differs).
3. Workload-to-workload authentication across clouds via Workload Identity Federation on both sides, eliminating static cross-cloud credentials entirely.
4. Centralized SIEM ingesting sign-in and audit logs from both Entra ID and GCP Cloud Audit Logs, with correlation rules for cross-cloud anomalous access patterns.
5. Regular joint access reviews spanning both clouds, owned by a single identity governance function rather than siloed per-cloud teams.

## Follow-Up Questions
- How would you handle a scenario where the two clouds are owned by genuinely separate teams with different risk tolerances?
- How do you prevent identity federation misconfiguration from becoming its own attack vector?

## Trade-offs
- Centralized identity governance: much stronger consistency and visibility, requires cross-team coordination and shared tooling investment that siloed per-cloud teams may resist.
