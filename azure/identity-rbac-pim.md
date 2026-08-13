# Azure Identity, RBAC & PIM

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is

The identity control plane for Azure: Entra ID (tenant, users, groups, service principals, managed identities), Azure RBAC (role assignments scoped to management group/subscription/resource group/resource), and Privileged Identity Management (PIM) for just-in-time, time-bound privileged access.

## Why Architects Use It

Identity is the primary security boundary in cloud — network perimeters are porous by design (PaaS, SaaS, remote work), so access decisions increasingly rely on "who/what is this, and what do they need right now" rather than "where are they connecting from."

## Architecture Considerations

- **Least privilege by default**: assign the narrowest built-in role that satisfies the need; avoid Owner/Contributor at broad scope as a default.
- **Managed identity over service principals with secrets**: system- or user-assigned managed identities eliminate credential management for Azure-to-Azure auth entirely — prefer this wherever the workload runs on Azure compute.
- **PIM for all standing privileged roles**: no one should have persistent Owner/User Access Administrator; eligible assignments with approval and time-bound activation dramatically reduce the standing attack surface.
- **RBAC scope discipline**: assign at the narrowest scope that makes operational sense (resource group over subscription, subscription over management group) to limit blast radius.
- **Custom roles sparingly**: prefer built-in roles; custom roles create long-term maintenance burden and often drift from intent.
- **Break-glass accounts**: at least two emergency-access accounts excluded from Conditional Access/MFA enforcement, monitored heavily, and used only in genuine emergencies.

## Common Mistakes

- Granting Owner at subscription scope "to get things working" and never revisiting it.
- Long-lived service principal secrets instead of managed identity or certificate-based auth.
- No PIM — privileged roles standing permanently, discoverable and abusable by any compromised session.
- Group-based RBAC without lifecycle management, so group membership silently grows privilege over time.
- Ignoring Conditional Access as part of the identity architecture (MFA, device compliance, risk-based sign-in policies).

## Principal-Level Questions

- How would you design identity for a multi-cloud enterprise spanning Azure and GCP?
- How do you prevent privilege escalation via role assignment chains (e.g., a role that can assign other roles)?
- What's your strategy for service-to-service authentication across 200+ microservices?

## Scenario Question

> Your security team flags that 15 engineers have standing Owner access at the subscription level "because deployments kept failing otherwise." Redesign the access model without breaking their ability to deploy.

## Strong Answer (Outline)

1. **Diagnose why Owner was used** — usually a proxy for "I need to create resources and manage RBAC," which is broader than what deployments actually require.
2. **Define the actual permission set needed** by the deployment pipeline (e.g., Contributor + a narrow custom role for the specific RBAC/policy operations it needs) — grant that to a **managed identity used by the pipeline**, not to individual humans.
3. **Move humans to PIM-eligible Contributor** (not Owner) for break-fix scenarios, activated on demand with justification and time-bound.
4. **Automate the common case** so engineers rarely need elevated access at all — self-service infrastructure through a golden path (Terraform module + pipeline) handles 90% of what they were using standing access for.
5. **Audit and alert** on PIM activations and any remaining standing privileged assignments.

## Follow-Up Questions

- How would you handle an emergency where PIM approval isn't available (approver unreachable) during an active incident?
- How do you extend this model to third-party contractors or a managed service provider?
- How would you detect if someone is chaining lower-privilege roles to achieve privilege escalation?

## Trade-offs

- **PIM friction vs. security**: JIT activation adds a few seconds/minutes of friction; the security benefit (removing standing privilege) is almost always worth it, but break-glass paths must exist for genuine emergencies.
- **Managed identity simplicity vs. cross-cloud/on-prem limitations**: managed identity only works for Azure-hosted workloads authenticating to Azure resources — hybrid/multi-cloud still needs a federated identity or secrets-based fallback for some paths.
- **Custom roles (precise) vs. built-in roles (simpler to audit and reason about)**.
