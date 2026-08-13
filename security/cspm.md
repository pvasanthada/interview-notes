# Cloud Security Posture Management (CSPM)

**Label:** `Reference Architecture` / `Hands-on / POC`

## What It Is
Continuous, automated scanning of cloud configuration against security best practices and compliance frameworks — Microsoft Defender for Cloud, GCP Security Command Center, or third-party CSPM platforms — surfacing misconfigurations before (ideally) or after they're exploited.

## Why Architects Use It
Manual periodic security reviews can't keep pace with the rate of change in a modern cloud estate; CSPM provides continuous, automated visibility that scales with the environment.

## Architecture Considerations
- **Secure score as a leading indicator, not a vanity metric**: track trend over time and tie remediation to actual risk reduction, not just score optimization for its own sake.
- **Prioritize by exploitability and blast radius**: a CSPM tool will surface hundreds of findings — triage by what's actually internet-facing, holds sensitive data, or has excessive privilege, not by raw finding count.
- **Wire findings into an actual response process**: CSPM findings that nobody triages are equivalent to not having the scan at all — integrate into ticketing/ownership workflows with SLAs by severity.
- **Compliance framework mapping**: map findings to the specific regulatory frameworks the organization must satisfy (PCI, ISO 27001, SOC 2) for audit-ready reporting, not just generic "security score."

## Common Mistakes
- Enabling CSPM tooling but never establishing an ownership/triage process, letting findings accumulate unaddressed.
- Treating every finding with equal urgency, causing alert fatigue and genuine critical findings getting lost in noise.
- No feedback loop from CSPM findings back into golden-path templates/modules, so the same misconfiguration class keeps recurring in new resources.

## Principal-Level Questions
- How would you operationalize CSPM findings across 40 subscriptions/projects without alert fatigue?
- How do you prevent the same class of misconfiguration from recurring after remediation?

## Scenario Question
> Your CSPM tool reports 3,000 open findings across the estate, and the security team of 3 people can't realistically triage all of them.

## Strong Answer (Outline)
1. Triage by risk: filter to internet-facing resources, resources holding sensitive data classifications, and excessive-privilege findings first — this typically reduces the actionable set dramatically.
2. Automate remediation for well-understood, low-risk-of-side-effect finding classes (e.g., auto-remediate public storage access via policy) rather than manual ticket-by-ticket handling.
3. Push the highest-recurrence finding classes back into golden-path IaC modules (see [`terraform/modules.md`](../terraform/modules.md)) so new resources don't reintroduce the same issue.
4. Establish a risk-based SLA (critical findings: days; low findings: best-effort backlog) and report trend, not raw count, to leadership.

## Follow-Up Questions
- How would you distinguish a finding that's genuinely urgent from one that's low-risk despite a high severity label?
- How do you handle findings on legacy systems that can't be easily remediated?

## Trade-offs
- Full auto-remediation: fastest risk reduction, real risk of breaking legitimate configuration if not carefully scoped to well-understood, low-side-effect changes.
