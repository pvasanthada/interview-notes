# Scenario: Security Incident

**Label:** `Interview Preparation`

## Prompt
> Your security monitoring detects that a service principal with broad permissions was used to access resources outside its normal pattern at 3am. Walk through your response.

## Context
Potential credential compromise, unclear scope of impact, off-hours detection (limited immediate human context available).

## Problem
Determine whether this is a genuine compromise, contain any actual damage, and remediate — without either overreacting to a benign anomaly or under-reacting to a real breach.

## Analysis
Anomalous off-hours access by a broadly-privileged identity is exactly the pattern Zero Trust / least-privilege design (see [`security/zero-trust.md`](../security/zero-trust.md)) is meant to limit the blast radius of — this incident is also a signal to examine why the identity had broad permissions in the first place, regardless of the immediate outcome.

## Options
1. Immediately revoke/disable the credential (fast containment, risk of disrupting a legitimate automated process if this turns out to be a false positive).
2. Investigate first, then act (lower disruption risk, slower containment if it's a genuine breach).
3. Contain in a targeted way (restrict the credential's permissions/network access without full revocation) while investigating.

## Decision
1. **Immediate containment**: given a broadly-privileged identity and clearly anomalous access pattern, err toward fast containment — disable/rotate the credential immediately, accepting the risk of disrupting a legitimate process, since the downside of a confirmed breach continuing is far worse than a contained false positive.
2. **Scope the investigation**: use cloud audit logs (Azure Activity Log / GCP Cloud Audit Logs, per [`security/security-governance.md`](../security/security-governance.md)) to reconstruct exactly what the identity accessed/modified during the anomalous window.
3. **Assess actual impact**: determine what data/systems were touched, whether anything was exfiltrated or modified, and whether the access pattern is consistent with a compromised credential versus a legitimate but unusual automated process that simply wasn't expected to run at that time.
4. **Remediate root cause**: if confirmed compromise, rotate all credentials the compromised identity could have accessed and review its permission scope — this incident likely reveals the identity was over-privileged relative to its actual needs (see [`azure/identity-rbac-pim.md`](../azure/identity-rbac-pim.md) / [`gcp/iam.md`](../gcp/iam.md)), and remediation should include scoping it down, not just rotating the credential.
5. **Notify appropriately**: security team, affected system owners, and — depending on findings — compliance/legal per the organization's incident classification process, without over-notifying before impact is actually understood.

## Trade-offs
- Fast containment risks disrupting a legitimate process if this turns out to be a false positive — an acceptable trade-off given a broadly-privileged identity's potential blast radius, but the disruption should be communicated quickly once identified as a false positive to rebuild trust with the affected team.

## Implementation
Post-incident: implement PIM/JIT for this identity class going forward if it was standing broad access (per [`azure/identity-rbac-pim.md`](../azure/identity-rbac-pim.md)), and add automated anomaly-based alerting tuned to catch this pattern faster in future.

## Outcome (Illustrative)
Investigation confirms the access was a genuine, if unusual, legitimate automated job that had recently changed schedule without updating documentation — a false positive, but the investigation surfaces that the identity's permissions were far broader than the job actually required, and remediation scopes it down regardless.

## Lessons Learned
- Even a false-positive security incident is valuable if it surfaces an over-privileged identity that should be remediated regardless of this specific incident's outcome.
- Fast, disciplined containment followed by careful investigation is the right sequence even when the eventual finding turns out benign — the process, not just the outcome, is what an interviewer is evaluating.
