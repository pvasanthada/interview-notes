# Firewalls, WAF & Segmentation

**Label:** `Learning / Overview` / `Reference Architecture`

## What It Is
Network-layer firewalls (stateful packet filtering, e.g., Azure Firewall/NSGs, GCP firewall rules), Web Application Firewalls (L7, inspecting HTTP traffic for known attack patterns), and the broader practice of network segmentation.

## Why Architects Use It
Firewalls and segmentation implement defense in depth — even with strong identity controls, network-layer restriction limits lateral movement and blast radius if another layer is compromised.

## Architecture Considerations
- **Layered enforcement**: NSGs (subnet/NIC level) + centralized firewall (hub egress/ingress inspection) + WAF (L7 application protection) — each layer catches different threat classes; none alone is sufficient.
- **WAF managed rule sets**: use provider-managed OWASP-based rule sets as a baseline, tuned with custom exclusions for legitimate application traffic patterns that trigger false positives, rather than either accepting excessive false positives or disabling rules wholesale.
- **Micro-segmentation**: restrict east-west traffic within a network (not just north-south at the perimeter) — assume perimeter breach is possible and design so a compromised workload can't freely reach everything else.
- **Default-deny posture**: default-deny with explicit allow rules is the standard baseline; default-allow environments are a common finding in security reviews of less mature estates.

## Common Mistakes
- Perimeter-only firewalling with a flat, unsegmented internal network — a single compromised host has unrestricted lateral access.
- WAF deployed in detection-only mode indefinitely, never actually blocking, due to fear of false positives — provides monitoring value but not real protection.
- Overly broad NSG rules (e.g., allowing entire subnet ranges) instead of specific, justified rules.

## Principal-Level Questions
- How would you design network segmentation for a workload subject to PCI-DSS?
- What's your approach to safely moving a WAF from detection to blocking mode in a live environment?

## Scenario Question
> Your security team wants to enable WAF blocking mode, but the last attempt caused false positives that broke legitimate customer traffic.

## Strong Answer (Outline)
1. Run in detection/log-only mode with full logging first (if not already), analyzing triggered rules against genuine traffic over a representative period (including peak/edge-case traffic, not just a quiet week).
2. Identify and tune false-positive-prone rules with targeted exclusions (specific to the request pattern causing the false positive, not disabling the rule category wholesale).
3. Roll out blocking mode incrementally — a percentage of traffic, or lowest-risk rule categories first — rather than all-at-once, with fast rollback capability.
4. Establish an ongoing process (not a one-time tuning exercise) since application changes will continue to introduce new false-positive patterns over time.

## Follow-Up Questions
- How would you balance WAF strictness against a legitimate business need for an unusual traffic pattern (e.g., a bulk API client)?
- How do you extend this segmentation discipline into a Kubernetes cluster's east-west traffic (see [`kubernetes/networking.md`](../kubernetes/networking.md))?

## Trade-offs
- Strict WAF/segmentation: stronger security posture, real risk of breaking legitimate traffic if not carefully tuned and tested.
