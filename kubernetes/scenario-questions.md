# Kubernetes — Scenario Questions

**Label:** `Interview Preparation`

## Foundation
1. Walk through diagnosing a pod stuck in `Pending` state.
2. Explain the difference between a Deployment, ReplicaSet, and Pod, and why the layering exists.

## Intermediate
3. A service intermittently fails to resolve another service's DNS name, but only under load. Diagnose.
4. Design resource requests/limits for a new workload with unknown traffic patterns.

## Senior
5. A cluster upgrade caused a rolling restart that took down a stateful workload's availability for 10 minutes despite having 3 replicas. Diagnose and prevent recurrence.
6. Design multi-tenant isolation for 10 teams sharing one cluster, covering RBAC, networking, and resource fairness.
7. Your Cluster Autoscaler isn't scaling down at night despite low utilization. Diagnose likely causes.

## Principal
8. Design a Kubernetes platform strategy for an organization currently running 40 independent, inconsistently configured clusters.
9. Propose a GitOps-based deployment model for a 200-microservice platform with strict compliance requirements.
10. A production outage was caused by a misconfigured PodDisruptionBudget blocking node draining during a security patch window. Redesign the platform's upgrade process.

## Architecture Scenario
11. Design a Kubernetes-based internal developer platform giving 30 application teams self-service deployment with centralized security guardrails.
12. Design multi-region Kubernetes architecture for a workload requiring regional failover with a 15-minute RTO.
