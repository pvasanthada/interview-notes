# Azure Compute

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
The spectrum of Azure compute options: IaaS (Virtual Machines, VM Scale Sets), container platforms (AKS, Container Apps), and PaaS (App Service, Azure Functions).

## Why Architects Use It
Choosing the right compute abstraction is one of the highest-leverage architecture decisions — it determines operational burden, scaling model, and cost structure for a workload's entire lifecycle.

## Architecture Considerations
- **Decision axes**: how much operational control is genuinely needed vs. how much can be delegated to the platform; team's container/Kubernetes maturity; workload shape (long-running services vs. event-driven bursts vs. batch).
- **VMs**: full control, highest operational burden — justified for legacy/lift-and-shift, specialized OS/driver requirements, or licensing constraints.
- **AKS**: best for teams with genuine multi-service, container-native workloads and the operational maturity to run Kubernetes (or willingness to use a managed add-on set).
- **App Service**: strong default for standard web apps/APIs needing minimal operational overhead.
- **Azure Functions**: best for event-driven, bursty, or scheduled workloads where per-execution billing and near-zero idle cost matter.
- **Container Apps**: middle ground — container-native without full Kubernetes operational surface, good for microservices that don't need AKS's full extensibility.

## Common Mistakes
- Defaulting to AKS for every workload regardless of actual need ("resume-driven architecture").
- Running stateful, latency-sensitive workloads on Functions without understanding cold-start implications.
- Over-provisioning VM SKUs "for headroom" instead of right-sizing with autoscale.

## Principal-Level Questions
- How do you decide between AKS, Container Apps, and App Service for a new microservices platform?
- When is a VM still the right answer in 2026?

## Scenario Question
> A team wants to migrate 30 microservices currently on VMs to a more modern compute platform. They have moderate Kubernetes experience. Recommend an approach.

## Strong Answer (Outline)
1. Segment the 30 services by actual requirements (network/sidecar needs, scaling pattern, statefulness) rather than migrating all identically.
2. Given "moderate" Kubernetes experience, propose Container Apps as the default target for standard stateless services (lower operational surface than AKS while still container-native), reserving AKS for services genuinely needing its extensibility (custom operators, complex networking, specific ecosystem tools).
3. Migrate in waves, starting with lower-risk services to build team confidence and platform tooling (CI/CD, observability) before tackling critical-path services.
4. Invest in a golden path (see [`platform-engineering/`](../platform-engineering/)) so future services default to the right platform without re-litigating the decision each time.

## Follow-Up Questions
- How would you handle a service with strict low-latency, high-throughput requirements that doesn't fit cleanly into either option?
- What's your migration rollback plan if a service performs worse after migration?

## Trade-offs
- AKS: maximum flexibility, maximum operational burden.
- Container Apps: less flexible, far less operational burden — right default for most teams.
- Functions: cheapest at low/bursty volume, but cold starts and execution-time limits constrain some workloads.
