# GCP Compute

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
Compute Engine (IaaS VMs), Cloud Run (managed, serverless container platform, request- or event-driven), Cloud Functions, and GKE (covered separately in [gke.md](gke.md)).

## Why Architects Use It
Same decision framework as Azure compute selection — match operational abstraction to actual workload needs — with Cloud Run notable as GCP's strongest serverless-container offering, often a better default than Azure Container Apps' equivalent maturity level for pure request-driven services.

## Architecture Considerations
- **Cloud Run**: scales to zero, per-request billing, minimal operational surface — excellent default for stateless HTTP services and event-driven workloads (via Pub/Sub or Eventarc triggers) that don't need full Kubernetes.
- **Compute Engine**: full control, justified for legacy lift-and-shift, specialized workloads, or licensing constraints — same rationale as Azure VMs.
- **GKE vs Cloud Run**: choose GKE when workloads need complex networking, custom controllers/operators, stateful sets, or an ecosystem of Kubernetes-native tooling; choose Cloud Run when the workload is fundamentally a stateless service/API/event handler.

## Common Mistakes
- Defaulting to GKE for workloads that would be simpler and cheaper on Cloud Run.
- Not leveraging Cloud Run's scale-to-zero for genuinely bursty/low-traffic services, leaving idle Compute Engine VMs running instead.
- Ignoring Cloud Run's request timeout and concurrency model when migrating from a long-running-process architecture.

## Principal-Level Questions
- When would you choose Cloud Run over GKE for a new microservices platform?
- How do you handle stateful workloads that don't fit Cloud Run's request-driven model?

## Scenario Question
> A team is running 20 lightweight internal APIs on dedicated Compute Engine VMs, each underutilized. Propose a more efficient platform.

## Strong Answer (Outline)
1. Migrate to Cloud Run: each API becomes an independently deployed, independently scaled service, billed per request/CPU-time rather than a constantly running VM.
2. Consolidate networking via a Shared VPC connector for services needing private connectivity to internal resources.
3. Standardize CI/CD (container build → Artifact Registry → Cloud Run deploy) as a golden path so future internal APIs default to this pattern.
4. Reserve GKE only if a subset of these later needs capabilities Cloud Run doesn't support (e.g., long-lived WebSocket connections beyond Cloud Run's limits, custom scheduling).

## Follow-Up Questions
- How would you handle a service needing WebSocket support or execution beyond Cloud Run's request timeout limits?
- What's your approach to secrets/config management across many independently deployed Cloud Run services?

## Trade-offs
- Cloud Run: minimal operational burden, less control over underlying infrastructure/networking specifics than GKE.
- GKE: maximum flexibility, meaningfully higher operational investment.
