# Kubernetes Troubleshooting

**Label:** `Hands-on / POC` / `Interview Preparation`

## What It Is
A systematic approach to diagnosing common Kubernetes failure modes, structured by symptom, so interview troubleshooting questions can be answered methodically rather than as a list of memorized commands.

## General Diagnostic Method
1. **Identify the layer**: is the problem at the pod level, node level, networking level, or control-plane level?
2. **Check events first**: `kubectl describe` and `kubectl get events` surface most scheduling/image/config errors immediately.
3. **Check logs**: application logs, then kubelet/system logs if the issue looks infrastructure-level.
4. **Check recent changes**: most production incidents trace to a recent deploy, config change, or scaling event — check this before assuming an exotic root cause.
5. **Reproduce narrowly**: isolate whether the issue affects one pod, one node, one namespace, or the whole cluster — this dramatically narrows the likely cause.

## Common Scenarios

### Pod CrashLoopBackOff
- Check `kubectl logs <pod> --previous` for the crash reason.
- Common causes: application error on startup, missing config/secret, failing readiness/liveness probe misconfigured too aggressively, resource limits too low (OOMKilled).

### Pending Pods
- Check `kubectl describe pod` events for the scheduler's reason (insufficient CPU/memory, no matching node due to taints/affinity, PVC not bound).
- Common causes: cluster at capacity (Cluster Autoscaler not configured or hitting a max-node limit), resource requests too large for any node, zone-mismatched volume.

### DNS Failure
- Check CoreDNS pod health and logs first.
- Common causes: CoreDNS pods unhealthy/undersized for cluster scale, NetworkPolicy blocking DNS traffic (port 53) unintentionally, upstream DNS resolver misconfiguration for external names.

### Network Connectivity Failure
- Check NetworkPolicy first (default-deny + missing allow rule is extremely common), then CNI health, then Service/Endpoint object correctness (`kubectl get endpoints`).

### Node Failure
- Check `kubectl describe node` for conditions (MemoryPressure, DiskPressure, NotReady).
- Common causes: kubelet crash/disconnect, underlying VM/host issue, disk full from log/image accumulation.

### Ingress Failure
- Check ingress controller logs/events, verify the Ingress object's backend Service/Endpoints are healthy, check TLS certificate validity/expiry.

### Storage Failure
- Check PVC/PV binding status, CSI driver pod health, underlying cloud disk/volume health and quota limits.

### API Server Unavailable
- On managed clusters, check the cloud provider's status page and control-plane metrics if exposed; check for a client generating excessive API load (see [`architecture.md`](architecture.md)).

### etcd Issue (self-managed)
- Check etcd cluster quorum/member health, disk latency, and database size against configured quota.

### Resource Exhaustion
- Check `kubectl top nodes/pods`, ResourceQuotas, and whether Cluster Autoscaler is configured and has room to scale (node group max limits).

## Principal-Level Framing

In an interview, don't just recite commands — narrate the **reasoning**: which layer you're isolating to, why you check events before logs, and how you'd confirm root cause versus just treating a symptom. This is what distinguishes a principal-level troubleshooting answer from a rote command list.

## Follow-Up Questions Interviewers Often Ask
- How would you have caught this before it caused a customer-facing incident?
- What would you change about the platform's defaults to prevent this class of issue recurring?
- How does your approach change on a managed control plane where you lack etcd/API server access?
