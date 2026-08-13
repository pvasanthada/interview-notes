# GKE — Google Kubernetes Engine

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
Google's managed Kubernetes offering, available in Standard mode (full node/cluster control) and Autopilot mode (Google manages node infrastructure, scaling, and much of the operational surface, billing per pod resource request).

## Why Architects Use It
GKE Autopilot in particular is a distinctive GCP offering worth knowing precisely for interviews — it shifts a meaningful chunk of the "who patches/scales nodes" operational burden to Google compared to AKS's more traditional managed-control-plane-only model.

## Architecture Considerations
- **Autopilot vs. Standard**: Autopilot removes node management entirely (no node pools to size/patch) at the cost of some flexibility (restricted to supported workload types, less low-level tuning) — strong default for teams wanting Kubernetes' API without the operational load of node management; Standard mode needed for workloads requiring privileged pods, custom node configurations, or DaemonSets that don't fit Autopilot's model.
- **Workload Identity** (GKE's implementation of Workload Identity Federation for pods): lets Kubernetes service accounts impersonate GCP service accounts without key files — the GKE-specific application of the IAM pattern in [`gcp/iam.md`](iam.md).
- **Multi-cluster/multi-region**: use Multi-Cluster Ingress/Fleet management for workloads needing regional failover, similar in intent to AKS multi-region patterns but with GCP's fleet-centric tooling.

## Common Mistakes
- Choosing Standard mode by default without evaluating whether Autopilot would eliminate real operational burden for the workload.
- Not adopting GKE Workload Identity, instead distributing service account keys into pods.
- Under-provisioning cluster autoscaling limits, causing pending pods during traffic spikes.

## Principal-Level Questions
- When would you choose GKE Autopilot over Standard mode, and what would push you back to Standard?
- How does GKE Workload Identity compare to AKS workload identity federation?

## Scenario Question
> Your platform team is spending significant time patching and right-sizing GKE Standard node pools across 15 clusters. Propose a way to reduce this operational burden.

## Strong Answer (Outline)
1. Evaluate each cluster's workloads against Autopilot's supported feature set (most standard microservice workloads qualify; privileged/DaemonSet-heavy workloads may not).
2. Migrate qualifying clusters to Autopilot, eliminating node patching/sizing entirely for those workloads.
3. For clusters that must remain Standard, adopt node auto-provisioning and automated node upgrades to reduce (not eliminate) manual burden.
4. Consolidate cluster count where feasible using namespaces/multi-tenancy patterns instead of one cluster per team, reducing the total operational surface.

## Follow-Up Questions
- How would you handle a workload needing a specific node-level configuration (e.g., a custom kernel parameter) that Autopilot doesn't support?
- What's your approach to cost visibility in Autopilot's per-pod-resource billing model versus Standard's per-node billing?

## Trade-offs
- Autopilot: far less operational burden, less low-level control and some workload restrictions.
- Standard: full control, meaningfully more ongoing operational investment.
