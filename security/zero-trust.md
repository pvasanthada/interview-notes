# Zero Trust

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
A security model that assumes no implicit trust based on network location — every access request is explicitly verified based on identity, device health, and context, regardless of whether it originates "inside" or "outside" a traditional network perimeter.

## Why Architects Use It
Perimeter-based trust (VPN in = trusted) breaks down under modern realities: SaaS, remote work, PaaS services with no clear network perimeter, and the assumption that a breach will eventually occur somewhere. Zero Trust limits blast radius by verifying continuously rather than once at the edge.

## Architecture Considerations
- **Identity as the primary control plane**: strong authentication (MFA), Conditional Access (device compliance, risk-based sign-in), and least-privilege authorization (RBAC/PIM) as the foundation — see [`iam.md`](iam.md).
- **Device trust**: verify device compliance/health as part of the access decision, not just user identity.
- **Micro-segmentation**: assume lateral movement risk even for authenticated traffic — segment networks so a compromised identity/workload can't freely reach everything (see [`networking/firewalls.md`](../networking/firewalls.md)).
- **Explicit verification, least privilege, assume breach**: the three core Zero Trust principles — every architecture decision should be checked against these.
- **Workload identity**: extend Zero Trust to service-to-service communication, not just human access — mutual TLS, workload identity federation, not implicit trust because traffic originates "inside the VNet."

## Common Mistakes
- Treating Zero Trust as a single product purchase rather than an architectural principle applied across identity, network, device, and application layers.
- Implementing strong perimeter identity controls (MFA at login) while leaving internal service-to-service traffic fully trusted by network location alone.
- No device compliance signal factored into access decisions, so a compromised but "authenticated" device retains full access.

## Principal-Level Questions
- How would you implement Zero Trust across Azure and GCP consistently, given they have different native tooling?
- What does "assume breach" change about how you'd design a specific system, concretely?

## Scenario Question
> Leadership wants to adopt Zero Trust but has a limited budget and can't rearchitect everything at once. Prioritize.

## Strong Answer (Outline)
1. Start with identity: enforce MFA and Conditional Access broadly first — highest security return for relatively contained effort, and foundational to everything else.
2. Move to least-privilege access: audit and remediate standing privileged access (PIM rollout) — addresses the most common real-world breach amplifier (over-privileged compromised accounts).
3. Layer in workload identity for service-to-service auth, eliminating long-lived credentials incrementally, starting with the highest-risk/highest-exposure services.
4. Add micro-segmentation for the most sensitive workloads first (e.g., anything handling regulated data), rather than attempting full network segmentation everywhere simultaneously.
5. Treat this as a multi-year, prioritized roadmap communicated clearly to leadership — not a single project with a defined "done" state.

## Follow-Up Questions
- How would you measure progress/maturity on a Zero Trust journey to report to leadership?
- How do you handle legacy systems that fundamentally can't support modern identity/device signals?

## Trade-offs
- Zero Trust: significantly reduces blast radius and lateral movement risk, adds friction/complexity and requires sustained investment — not a one-time project.
