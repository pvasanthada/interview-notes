# Terraform — Scenario Questions

**Label:** `Interview Preparation`

## Foundation
1. Explain the Terraform workflow (`init`, `plan`, `apply`) and what each step actually does.
2. Why should Terraform state never be stored locally in a team setting?

## Intermediate
3. A `terraform apply` fails halfway through, leaving some resources created and others not. How do you recover safely?
4. Design a module structure for a reusable "compliant storage account" pattern.

## Senior
5. Terraform state has become corrupted for a critical production stack. Walk through your recovery process.
6. When should you use Terraform workspaces versus separate state files/configurations? Justify with a concrete scenario.
7. A module used by 50 stacks needs a breaking change. Plan the rollout.

## Principal
8. Design a large-enterprise Terraform architecture spanning 100+ stacks, multiple clouds, and varying team ownership models.
9. Design a secure CI/CD pipeline for Terraform that's safe for junior engineers to use without direct production access.
10. Propose a policy-as-code strategy that prevents non-compliant infrastructure from being created in the first place, not just detected afterward.

## Architecture Scenario
11. Design the full lifecycle for a new application team onboarding onto your Terraform-based self-service platform, from initial access to production deployment.
12. Design a Terraform strategy for managing infrastructure consistently across Azure and GCP for a genuinely multi-cloud workload.
