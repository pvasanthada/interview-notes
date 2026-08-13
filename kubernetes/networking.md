# Kubernetes Networking

**Label:** `Hands-on / POC` / `Reference Architecture`

## What It Is
The Kubernetes networking model: flat pod-to-pod networking (every pod gets a routable IP), Services (stable virtual IPs for a set of pods), Ingress/Gateway API (L7 external routing), and the CNI plugin that implements the actual networking data plane.

## Why Architects Use It
Correct networking design determines both application connectivity and security segmentation — most production Kubernetes incidents involving "can't reach service X" trace back to a misunderstanding at this layer.

## Architecture Considerations
- **Service types**: ClusterIP (internal only, default), NodePort (rarely the right production choice), LoadBalancer (provisions a cloud LB per service — can get expensive at scale), and Headless Services (direct pod DNS resolution, needed for StatefulSets).
- **Ingress vs. Gateway API**: Ingress is simpler but limited in expressiveness; Gateway API is the newer, more expressive standard (traffic splitting, multiple protocols, clearer role separation between infra and app teams) — new platforms should default to Gateway API where the ecosystem support exists.
- **CNI choice**: affects network policy enforcement capability, performance, and IP address management (IPAM) — not all CNIs support NetworkPolicy equally; verify before assuming policies will be enforced.
- **DNS**: CoreDNS resolves in-cluster service names; DNS issues are among the most common root causes of intermittent connectivity failures (see [`troubleshooting.md`](troubleshooting.md)).
- **Service mesh** (Istio/Linkerd): adds mTLS, fine-grained traffic management, and richer observability at the cost of real operational and latency overhead — justify by an actual requirement (zero-trust mTLS mandate, canary/traffic-shaping needs), not by default.

## Common Mistakes
- Provisioning a cloud LoadBalancer Service per microservice, causing unnecessary cost and IP sprawl — should typically route through a single Ingress/Gateway.
- Assuming NetworkPolicy is enforced without verifying the CNI supports it.
- Adopting a service mesh for its own sake, adding complexity without a concrete driving requirement.
- Not understanding that Services load-balance at L4 (or L7 via kube-proxy IPVS/iptables specifics) — leads to confusion debugging uneven traffic distribution.

## Principal-Level Questions
- How would you design external traffic routing for 50 microservices across multiple teams?
- When would you introduce a service mesh, and what would you use as the trigger/requirement rather than "everyone else has one"?
- How does NetworkPolicy actually get enforced, and what happens if the CNI doesn't support it?

## Scenario Question
> Multiple teams are independently provisioning cloud LoadBalancer Services for their microservices, and cloud networking costs have grown substantially. Redesign.

## Strong Answer (Outline)
1. Consolidate to a single ingress controller (or Gateway API implementation) fronted by one or a small number of cloud load balancers, with per-service routing rules instead of per-service cloud LBs.
2. Establish this as the platform's golden path — teams request a route/hostname, not their own LoadBalancer Service, enforced via policy (e.g., admission control blocking `type: LoadBalancer` outside an approved namespace).
3. Migrate existing services incrementally, validating traffic cutover per service with canary routing before decommissioning old LBs.
4. Track resulting cost reduction and codify the pattern in platform documentation/templates.

## Follow-Up Questions
- How would you handle a team with a genuine requirement for a dedicated LoadBalancer (e.g., non-HTTP protocol)?
- How do you extend this pattern to handle TLS certificate management at scale?

## Trade-offs
- Shared ingress: much lower cost and operational surface, introduces a shared dependency/blast-radius point that needs its own resilience design (multiple ingress controller replicas, careful rollout process).
