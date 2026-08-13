# Scenario: Major Production Outage

**Label:** `Interview Preparation`

## Prompt
> A customer-facing application experiences a full outage during peak business hours. As the architect, you're pulled into the incident bridge. Walk through your approach.

## Context
Live, customer-impacting incident, high visibility, time pressure, and initially unclear root cause.

## Problem
Restore service as quickly as possible while ensuring the response is coordinated, not chaotic, and while preserving the information needed for an accurate post-incident review.

## Analysis
In an active incident, an architect's role shifts from designer to incident coordinator/technical advisor — the immediate priority is mitigation, not root-cause certainty; root-cause analysis follows once the immediate impact is contained.

## Options
The "options" in an active incident are typically mitigation paths, evaluated for speed and risk:
1. Roll back the most recent deployment/change (fastest if a recent change is the likely trigger).
2. Fail over to a secondary region/replica (if the issue is infrastructure-localized).
3. Scale out (if the issue is capacity-related).
4. Apply a targeted fix (only if root cause is already clear and the fix is low-risk).

## Decision (Structured Incident Response Approach)
1. **Establish clear incident command**: one person coordinating, avoiding the common failure mode of many people independently investigating and stepping on each other — as the architect, I'd support the incident commander with technical guidance rather than trying to run the incident myself unless I'm explicitly in that role.
2. **Check recent changes first**: the majority of production incidents trace to a recent deployment/config change (per [`kubernetes/troubleshooting.md`](../kubernetes/troubleshooting.md) principles) — if a clear recent change correlates with the incident start time, roll it back immediately rather than continuing to investigate root cause live.
3. **Mitigate before diagnosing fully**: prefer the fastest safe mitigation (rollback, failover, scale-out) over a fully confident root-cause fix — restoring service takes priority over understanding exactly why it broke, as long as the mitigation itself is low-risk.
4. **Communicate status regularly**: to stakeholders/customers at a defined cadence, even if the update is "still investigating," rather than going silent during the incident.
5. **Preserve evidence**: ensure logs/metrics/traces from the incident window are retained (not rotated out) for the post-incident review, since a fast mitigation can sometimes remove the very evidence needed to fully understand root cause later.

## Trade-offs
- Prioritizing fast mitigation over full root-cause certainty is the right trade-off during active customer impact, but requires discipline to still follow up with a genuine post-incident investigation rather than declaring victory once service is restored.

## Implementation
Post-restoration: blameless post-incident review (see [`docs/behavioral-framework.md`](../docs/behavioral-framework.md) for the leadership angle on owning mistakes) identifying true root cause and concrete follow-up actions with owners and deadlines — not just a narrative writeup.

## Outcome (Illustrative)
Service restored via rollback within 20 minutes of confirmed root cause; a formal review identifies a gap in pre-deployment load testing as the underlying cause, leading to a new mandatory load-test gate for changes to that service.

## Lessons Learned
- The single highest-leverage early action in most production incidents is checking recent changes, not deep technical investigation.
- A well-run incident (clear command, fast mitigation, regular communication) is itself a leadership skill separate from technical diagnosis skill — both matter and interviewers often probe for both explicitly.
