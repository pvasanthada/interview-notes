# GCP Landing Zones — Organization, Folders, Projects

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
GCP's resource hierarchy: Organization (root) → Folders (grouping, mirrors business units/environments) → Projects (the unit of billing, IAM, and API enablement, roughly analogous to an Azure subscription) → Resources.

## Why Architects Use It
Same purpose as Azure Landing Zones: provide a governed, policy-inherited structure so IAM, Organization Policy, and billing map cleanly to organizational structure instead of being applied ad hoc per project.

## Architecture Considerations
- **Projects are cheap and numerous by design** — GCP's model leans toward many small, single-purpose projects (unlike Azure's historical tendency toward fewer, larger subscriptions), which improves blast-radius isolation but requires strong automation for project provisioning at scale.
- **Folder structure** typically mirrors environment (prod/non-prod) and/or business unit — design so Organization Policy and IAM inheritance align with folder boundaries.
- **Shared VPC** centralizes networking (host project) while allowing service projects to attach without owning their own VPC — the GCP equivalent of Azure's hub-spoke, but implemented at the IAM/project level rather than pure network peering.
- **Resource Manager APIs** should back an automated project-vending process (similar to Azure subscription vending), not manual console creation.

## Common Mistakes
- Treating a GCP project like an Azure subscription 1:1 and under-provisioning projects, losing the isolation benefit the model is designed for.
- No folder structure — flat organization with hundreds of ungrouped projects, making policy inheritance unmanageable.
- Manual project creation without a standard template (default network left enabled, no baseline IAM/logging), producing inconsistent security posture per project.

## Principal-Level Questions
- How would you design a GCP folder/project structure for an enterprise with 5 business units and 3 environments each?
- How do you decide when a workload gets its own project vs. shares one, given GCP's "many small projects" philosophy?

## Scenario Question
> Design a landing zone for a company adopting GCP as a secondary cloud, needing to integrate with an existing centralized security/logging function that currently lives in Azure.

## Strong Answer (Outline)
1. Organization → Folders per environment (Production, Non-Production, Sandbox) → sub-folders per business unit if scale warrants it.
2. Automated project-vending (Terraform module + pipeline) producing a standard baseline project: default network disabled, baseline IAM, logging sink wired to the centralized log destination, budget alert configured.
3. Shared VPC host project per environment for workloads needing centralized connectivity; standalone VPC for genuinely isolated workloads.
4. Cross-cloud logging: configure log sinks/exports from Cloud Logging into the existing centralized platform (e.g., via Pub/Sub → export pipeline) so security has one pane of glass across clouds.

## Follow-Up Questions
- How would you enforce consistent labeling/tagging across a "many small projects" model without it becoming unmanageable?
- What's your approach to cost allocation given project sprawl?

## Trade-offs
- Many small projects: excellent isolation, more automation investment required upfront.
- Few large projects: less isolation, easier initial management — tends to accumulate governance debt.
