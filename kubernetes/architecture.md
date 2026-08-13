# Kubernetes Architecture

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
The control plane (API server, scheduler, controller manager, etcd) and data plane (kubelet, kube-proxy, container runtime on worker nodes) that together form a Kubernetes cluster.

## Why Architects Use It
Understanding the control loop model (desired state in etcd, controllers continuously reconciling actual state toward it) is foundational to reasoning about failure modes, scaling limits, and extension points (operators/CRDs) — this underpins almost every deeper Kubernetes question.

## Architecture Considerations
- **API server**: stateless, horizontally scalable front door to the cluster; all components talk to etcd only through it.
- **etcd**: the single source of truth for cluster state — a distributed key-value store using Raft consensus; its health (latency, disk I/O, quorum) is often the actual root cause of "cluster is slow/unresponsive" symptoms.
- **Scheduler**: assigns pods to nodes based on resource requests, affinity/anti-affinity, taints/tolerations, and topology spread constraints.
- **Controller manager**: runs the reconciliation loops (Deployment controller, ReplicaSet controller, etc.) that continuously drive actual state toward desired state.
- **kubelet**: the node agent that actually starts/stops containers per the container runtime interface (CRI) and reports node/pod status back to the API server.
- **Managed control planes (AKS/GKE/EKS)**: the cloud provider operates the control plane; architects should still understand its behavior to reason about node pool scaling, upgrade sequencing, and control-plane-related failure scenarios even without directly managing etcd.

## Common Mistakes
- Treating the cluster as a black box and not understanding reconciliation loops, leading to confusion when "the YAML I applied doesn't match what's running."
- Under-provisioning etcd (especially self-managed) storage/IOPS, causing cluster-wide slowness under load.
- Ignoring the difference between desired state (what you declared) and actual state (what's really running) when troubleshooting.

## Principal-Level Questions
- Walk through what happens end-to-end when you run `kubectl apply` for a new Deployment.
- How would you diagnose a cluster where the API server is slow to respond but nodes/pods otherwise appear healthy?
- What are the failure implications if etcd loses quorum?

## Scenario Question
> Your cluster's API server response times have degraded significantly over the past week, with no obvious increase in workload count. Diagnose.

## Strong Answer (Outline)
1. Check etcd health first — latency, disk I/O, and database size are the most common root causes of API server slowness that isn't tied to workload count. Excessive Kubernetes Events or large numbers of ConfigMaps/Secrets can bloat etcd without a corresponding rise in "workload count."
2. Check API server request rate and error rate by verb/resource (e.g., a controller or CI system polling excessively).
3. Check for a misbehaving controller/operator generating excessive watch/list traffic.
4. Review recent changes — a new CRD/operator installation is a very common trigger for this exact symptom.
5. Once root-caused, address at the source (fix the noisy client, tune etcd, or scale the control plane if self-managed) rather than just restarting components.

## Follow-Up Questions
- How would this diagnosis differ on a managed control plane (AKS/GKE) where you can't directly access etcd?
- What metrics would you want alerting on to catch this earlier next time?

## Trade-offs
- Self-managed control plane: full visibility/control over etcd and API server tuning, significant operational burden.
- Managed control plane: much less operational burden, less direct visibility — architects compensate with strong node-level and application-level observability instead.
