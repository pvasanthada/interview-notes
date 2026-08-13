# Helm & GitOps

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Helm (Kubernetes package manager — templated manifests bundled as versioned charts) and GitOps (declarative desired state stored in Git, continuously reconciled into the cluster by a controller such as Argo CD or Flux, rather than pushed by a CI pipeline).

## Why Architects Use It
Helm solves the "how do I template and version Kubernetes manifests" problem; GitOps solves the "how do I know the cluster matches what's in Git, and how do I roll back reliably" problem — together they form the standard deployment model for mature Kubernetes platforms.

## Architecture Considerations
- **Push-based CI/CD vs. GitOps (pull-based)**: in GitOps, a controller running inside the cluster pulls and reconciles desired state from Git, rather than an external CI pipeline pushing changes via `kubectl`/Helm directly — this improves security (no external system needs cluster-admin credentials) and gives a continuously-enforced source of truth (drift is auto-corrected, not just detected at deploy time).
- **Argo CD / Flux**: both implement the GitOps controller pattern; Argo CD adds a strong UI and app-of-apps pattern for managing many applications' desired state as itself a Git-managed hierarchy.
- **Helm chart structure**: values files per environment, a shared base chart — avoid excessive templating complexity that makes charts hard to reason about; sometimes plain Kustomize overlays are simpler for environment-specific variation.
- **Progressive delivery**: combine GitOps with tools like Argo Rollouts for canary/blue-green deployments driven by automated metric analysis, not just a straight rollout.

## Common Mistakes
- CI pipelines retaining direct cluster-admin credentials for `kubectl apply`, a significant credential-sprawl and security risk GitOps is specifically designed to avoid.
- Helm charts with excessive conditional templating logic that becomes unmaintainable — often a sign plain manifests + Kustomize would have been simpler.
- No drift detection/auto-remediation, so manual `kubectl edit` changes silently diverge from Git and are never reconciled back.
- Treating GitOps repo structure as an afterthought, leading to unclear ownership of what's deployed where.

## Principal-Level Questions
- How does GitOps improve security posture compared to a traditional CI-push deployment model?
- How would you design a GitOps repository structure for 50 applications across 5 environments?

## Scenario Question
> Design a GitOps-based deployment platform for 50 microservices across dev/staging/prod, with self-service for application teams but centralized guardrails.

## Strong Answer (Outline)
1. Repository structure: an "app-of-apps" pattern — a central platform repo declares which applications exist per environment/cluster, while each application's own repo/chart holds its actual manifests, giving app teams self-service ownership of their own deployment config within a centrally reviewed structure.
2. Argo CD (or Flux) running in each cluster, configured to only sync from approved repos/paths.
3. Environment promotion via Git — a PR/merge moving a version reference from staging to prod path, giving a full audit trail and approval gate without a separate deployment tool.
4. Guardrails enforced via admission control (e.g., OPA/Gatekeeper) independent of GitOps itself, so even a misconfigured GitOps-managed manifest can't violate baseline policy.
5. Progressive delivery (canary) for production promotions on critical services, automated via metric-based analysis.

## Follow-Up Questions
- How would you handle secrets in a GitOps model, given Git shouldn't store plaintext secrets?
- How do you roll back quickly if a GitOps-deployed change causes an incident?

## Trade-offs
- GitOps: strong auditability and security posture, adds the operational responsibility of running and securing the GitOps controllers themselves.
- Helm's templating power vs. Kustomize's simplicity: choose based on actual variation complexity needed, not habit.
