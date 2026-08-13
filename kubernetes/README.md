# Kubernetes — Interview Preparation

**Label:** `Hands-on / POC` / `Reference Architecture`

Senior/principal-level Kubernetes material: architecture, networking, storage, security, autoscaling, observability, GitOps, and troubleshooting.

## Contents

| File | Covers |
|---|---|
| [architecture.md](architecture.md) | Control plane, worker nodes, API server, etcd |
| [networking.md](networking.md) | Services, Ingress, Gateway API, CNI |
| [storage.md](storage.md) | CSI, PVs/PVCs, storage classes |
| [security-rbac.md](security-rbac.md) | RBAC, network policies, secrets |
| [autoscaling.md](autoscaling.md) | HPA, VPA, Cluster Autoscaler, PDBs |
| [observability.md](observability.md) | Prometheus, Grafana, OpenTelemetry |
| [helm-gitops.md](helm-gitops.md) | Helm, Argo CD, GitOps patterns |
| [troubleshooting.md](troubleshooting.md) | Systematic failure diagnosis |
| [scenario-questions.md](scenario-questions.md) | Kubernetes scenario prompts |

AKS and GKE specifics are cross-referenced from [`azure/compute.md`](../azure/compute.md) and [`gcp/gke.md`](../gcp/gke.md) respectively — this folder focuses on platform-agnostic Kubernetes knowledge.
