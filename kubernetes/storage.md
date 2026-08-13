# Kubernetes Storage

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
The Container Storage Interface (CSI) abstraction connecting Kubernetes to cloud/on-prem storage backends, expressed through PersistentVolumes (PV), PersistentVolumeClaims (PVC), and StorageClasses (dynamic provisioning templates).

## Why Architects Use It
Kubernetes was originally stateless-workload-focused; running stateful workloads correctly requires understanding these abstractions precisely, especially around failure/rescheduling behavior.

## Architecture Considerations
- **StorageClass parameters**: define provisioner, reclaim policy (`Delete` vs `Retain` — `Retain` is safer for data you can't afford to lose to an accidental PVC deletion), and volume binding mode (`Immediate` vs `WaitForFirstConsumer` — the latter avoids scheduling conflicts in multi-zone clusters by delaying provisioning until a pod is actually scheduled).
- **Access modes**: ReadWriteOnce (single node), ReadOnlyMany, ReadWriteMany (needs a storage backend that supports it — not all CSI drivers do) — mismatching access mode to actual multi-pod access needs is a common design error.
- **StatefulSets**: provide stable network identity and stable per-replica storage (via volumeClaimTemplates), essential for workloads like databases run in-cluster.
- **Backup**: Kubernetes-native volume snapshots (via CSI snapshot support) plus an actual backup tool (e.g., Velero) for full disaster recovery — a volume snapshot alone is not a complete backup strategy.

## Common Mistakes
- Using `Delete` reclaim policy for critical data PVCs, risking accidental data loss on PVC deletion.
- Assuming ReadWriteMany works with any storage backend without checking CSI driver support.
- Running production databases in-cluster without a real backup/DR strategy beyond the underlying disk's replication.
- Not using `WaitForFirstConsumer` in multi-zone clusters, causing pods to be unschedulable because their volume was provisioned in the wrong zone.

## Principal-Level Questions
- How would you design storage for a StatefulSet-based database running across 3 availability zones?
- What's your DR strategy for stateful workloads running in Kubernetes?

## Scenario Question
> A production pod using a PVC was accidentally deleted along with its namespace, and the underlying data was lost. Redesign to prevent recurrence.

## Strong Answer (Outline)
1. Root cause: reclaim policy was `Delete`, and no backup existed independent of the PVC lifecycle.
2. Immediate fix: set `Retain` reclaim policy on StorageClasses backing critical/stateful workloads, and add an admission policy blocking accidental namespace deletion for production namespaces (or require a two-step confirmation process).
3. Add a real backup solution (e.g., Velero with scheduled backups to object storage) independent of the PVC/PV lifecycle entirely.
4. Document and test the actual restore procedure — an untested backup is not a working DR plan (same principle as [`architecture/ha-dr.md`](../architecture/ha-dr.md)).

## Follow-Up Questions
- How would you validate that backups are actually restorable on a regular basis?
- How does this design change for a multi-zone or multi-region cluster?

## Trade-offs
- `Retain` policy: safer against accidental deletion, requires manual PV cleanup afterward — an acceptable trade for critical data.
