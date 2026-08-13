# Security Governance & SIEM

**Label:** `Reference Architecture` / `Learning-Overview`

## What It Is
The organizational and technical layer that turns individual security controls into a governed program: security policy definition, SIEM (Security Information and Event Management) for centralized detection and correlation, and vulnerability management.

## Why Architects Use It
Individual controls (IAM, CSPM, WAF) each reduce specific risks, but without a governance layer tying them together — policy ownership, centralized detection, and a defined response process — an organization has security tools without a security program.

## Architecture Considerations
- **Centralized SIEM ingestion**: aggregate signal from identity (sign-in logs), network (firewall/flow logs), endpoint, and cloud-native security tools (Defender for Cloud, Security Command Center) into one correlation engine — fragmented visibility across tools slows detection and investigation.
- **Detection engineering, not just log collection**: SIEM value comes from tuned detection rules mapped to real threat scenarios (e.g., impossible travel, privilege escalation chains), not just having logs searchable after the fact.
- **Vulnerability management as a continuous process**: scanning (infrastructure, container images, dependencies) feeding a prioritized, owned remediation backlog — not a point-in-time audit exercise.
- **Security policy as a living, enforced artifact**: policies should map directly to technical controls (Azure Policy/Org Policy, CSPM rules) wherever feasible, not exist only as a document nobody references operationally.

## Common Mistakes
- SIEM deployed with default/generic detection rules never tuned to the organization's actual environment, producing high noise and missed real threats.
- Vulnerability scanning without an owned remediation process, so findings pile up indefinitely.
- Security policy documents disconnected from actual enforced technical controls.

## Principal-Level Questions
- How would you design a SIEM strategy for a multi-cloud environment with disparate logging formats?
- How do you keep vulnerability management from becoming a perpetual, unaddressed backlog?

## Scenario Question
> Your security team has a SIEM ingesting logs from Azure, GCP, and on-prem, but incident investigations still take days because analysts struggle to correlate events across sources.

## Strong Answer (Outline)
1. Standardize log schema/normalization at ingestion (e.g., mapping all sources to a common schema) so correlation queries don't need source-specific logic each time.
2. Build and tune detection rules for the organization's actual highest-risk scenarios (informed by threat modeling, not generic templates), reducing noise and improving signal quality.
3. Establish consistent identity correlation keys (a unified identity, per [`security/iam.md`](iam.md)) across sources, so a single investigation can trace one identity's activity across clouds.
4. Invest in a documented, rehearsed incident response runbook so analysts aren't reasoning from scratch during a live incident.

## Follow-Up Questions
- How would you measure whether your SIEM investment is actually improving detection/response outcomes?
- How do you balance detection engineering investment against the ongoing cost of log ingestion at scale?

## Trade-offs
- Deep log ingestion/retention: better investigative depth, real and growing cost — tune retention/ingestion scope to actual investigative and compliance value.
