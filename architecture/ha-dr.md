# HA/DR — Cross-Cloud Principles

**Label:** `Reference Architecture`

## What It Is
The cross-cutting principles for high availability (surviving component/zone-level failure with minimal disruption) and disaster recovery (recovering from a larger-scale failure — region, major outage — within defined RTO/RPO), applicable across Azure ([`azure/disaster-recovery.md`](../azure/disaster-recovery.md)) and GCP ([`gcp/disaster-recovery.md`](../gcp/disaster-recovery.md)).

## Why Architects Use It
HA/DR design is a direct translation of business risk tolerance and regulatory requirements into technical architecture — one of the clearest examples of the principal architect mindset (setting the dial deliberately, not maximizing by default).

## Architecture Considerations
- **RTO/RPO first, technology second**: every HA/DR conversation should start with "what does the business actually need," not "what pattern should we use" — see [`docs/principal-architect-mindset.md`](../docs/principal-architect-mindset.md).
- **HA (zone-level) vs. DR (region-level) are different problems**: HA is usually solved by redundancy within a region (multiple availability zones) and is comparatively cheap; DR (surviving a full regional outage) is a bigger, more expensive undertaking — don't conflate "we're multi-AZ" with "we have DR."
- **Backup protects against different failure modes than replication**: replication protects against infrastructure failure; backup protects against logical/data corruption and ransomware — a mature DR strategy needs both, since replication faithfully propagates corrupted data too.
- **Testing is part of the architecture, not an afterthought**: an untested DR plan should be assumed non-functional until proven otherwise — regular failover drills are a required component, not optional polish.
- **Active-active vs. active-passive vs. backup-only**: increasing cost/complexity in that order, decreasing RTO/RPO in that order — choose based on actual business requirement (see the worked example in [`azure/disaster-recovery.md`](../azure/disaster-recovery.md)).

## Common Mistakes
- Assuming multi-AZ deployment constitutes DR.
- Never testing failover, discovering gaps only during a real incident.
- Designing to the most conservative RTO/RPO by default rather than the one the business actually requires, incurring unnecessary cost/complexity.
- Relying on replication alone, with no separate backup, leaving no protection against data corruption/ransomware.

## Principal-Level Questions
- What's the difference between HA and DR, and why does conflating them lead to under-protected systems?
- How would you convince a business stakeholder to invest in DR testing, which has a real cost and no immediate visible benefit until an incident occurs?

## Scenario Question
> A business stakeholder insists the application "already has DR" because it runs across multiple availability zones. Assess this claim and respond.

## Strong Answer (Outline)
1. Clarify the distinction: multi-AZ protects against a zone-level failure (a more common, lower-impact event) but does not protect against a full regional outage, which is what "DR" typically refers to — the stakeholder's claim conflates HA with DR, a very common misunderstanding worth surfacing clearly and non-condescendingly.
2. Establish the actual business requirement: ask what RTO/RPO and what failure scope (zone vs. region vs. data corruption) the business genuinely needs protection against, rather than assuming the answer.
3. If regional DR is genuinely required, design accordingly (per [`azure/disaster-recovery.md`](../azure/disaster-recovery.md) / [`gcp/disaster-recovery.md`](../gcp/disaster-recovery.md)) and present the cost/complexity trade-off transparently.
4. If the actual risk tolerance is genuinely satisfied by multi-AZ HA alone (a legitimate possible outcome for some workloads), document that decision explicitly as a conscious choice, not an accidental gap discovered later.

## Follow-Up Questions
- How would you prioritize DR investment across many applications with limited budget?
- How do you validate that a documented DR plan would actually work, without disrupting production to test it?

## Trade-offs
- Regional DR: protects against a broader failure class, meaningfully higher cost/complexity than HA alone — the decision should be explicit and business-driven, not accidental or default-maximal.
