# Kubernetes Security & RBAC

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
Kubernetes RBAC (Roles/ClusterRoles bound to subjects via RoleBindings/ClusterRoleBindings), NetworkPolicies (pod-level network segmentation), Secrets, and Pod Security Standards/admission control.

## Why Architects Use It
Kubernetes clusters are frequently multi-tenant (multiple teams/workloads sharing a cluster); RBAC and NetworkPolicy are the primary mechanisms for enforcing least privilege and segmentation within that shared environment.

## Architecture Considerations
- **Least privilege RBAC**: namespace-scoped Roles over cluster-wide ClusterRoles wherever possible; avoid binding `cluster-admin` to anything but genuine platform-admin identities.
- **Default-deny NetworkPolicy**: without an explicit default-deny policy, all pods can reach all other pods by default — establish default-deny per namespace, then explicitly allow required traffic.
- **Secrets**: base64-encoded, not encrypted, by default in etcd unless encryption-at-rest is explicitly enabled — treat Secrets as sensitive but not sufficient alone; prefer external secrets management (synced via an operator) or workload identity federation to cloud secret stores for genuinely sensitive values.
- **Pod Security Standards**: enforce `restricted` or `baseline` profiles via admission control to prevent privileged containers, host network/PID access, etc., in workload namespaces.
- **Workload identity**: bind pods to cloud identities (Azure Workload Identity, GKE Workload Identity) instead of embedding cloud credentials as Kubernetes Secrets.

## Common Mistakes
- No default-deny NetworkPolicy, leaving full east-west traffic open across all namespaces.
- Overly broad RBAC (`cluster-admin` for CI/CD service accounts "to avoid permission errors").
- Storing cloud credentials as raw Kubernetes Secrets instead of using workload identity federation.
- Allowing privileged pods/hostPath mounts in general-purpose namespaces without justification.

## Principal-Level Questions
- How would you design multi-tenant isolation for a shared Kubernetes cluster hosting 10 different teams' workloads?
- How would you implement Zero Trust networking within a cluster?

## Scenario Question
> A shared Kubernetes cluster hosts workloads for 8 teams. A security review finds any pod can reach any other pod, and several CI/CD service accounts have cluster-admin. Redesign.

## Strong Answer (Outline)
1. Namespace-per-team (or per-workload-domain) as the tenancy boundary, with ResourceQuotas to prevent noisy-neighbor resource contention.
2. Default-deny NetworkPolicy per namespace, with explicit allow rules only for genuinely required cross-namespace traffic (e.g., a shared internal API), documented per exception.
3. Replace cluster-admin CI/CD bindings with namespace-scoped Roles granting only the verbs/resources the pipeline actually needs (create/update Deployments, ConfigMaps, Secrets within its own namespace).
4. Enforce Pod Security Standards (`restricted` baseline) via admission control across workload namespaces, with a documented, reviewed exception process for genuine edge cases (e.g., a monitoring agent needing host access).
5. Migrate cloud credential access to workload identity federation, removing static cloud secrets from the cluster entirely where possible.

## Follow-Up Questions
- How would you handle a legitimate cross-team dependency that needs a NetworkPolicy exception?
- How do you audit RBAC drift over time as new roles/bindings accumulate?

## Trade-offs
- Default-deny NetworkPolicy: strong security posture, requires disciplined documentation of allow-rules or teams will file constant "why can't I reach X" tickets.
- Namespace-per-team multi-tenancy: simpler RBAC/NetworkPolicy boundaries than a shared namespace model, still shares the underlying node/control-plane blast radius — genuinely hard isolation requirements may need separate clusters instead.
