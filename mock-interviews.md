# Mock Interviews

**Label:** `Interview Preparation`

Six mock interview rounds mirroring a typical Principal Cloud & AI Infrastructure Architect loop. Each includes the interviewer question, the expected thinking process, a strong answer outline, likely follow-ups, and what's actually being evaluated.

Practice these out loud or with a peer — reading silently is a weak substitute for verbalizing the answer under mild time pressure.

---

## Round 1 — Cloud Fundamentals

**Interviewer question**: "Walk me through how you'd set up a new Azure subscription for a team building a customer-facing API, from nothing to production-ready."

**Expected thinking**: This is a foundation-level question checking baseline competence and structured thinking before deeper rounds — don't over-engineer the answer, but don't skip governance either.

**Strong answer outline**:
1. Subscription provisioned via the org's subscription-vending process (per [`azure/landing-zones.md`](azure/landing-zones.md)), landing in the correct management group with baseline policy inherited.
2. Identity: no standing Owner access; team gets PIM-eligible Contributor (per [`azure/identity-rbac-pim.md`](azure/identity-rbac-pim.md)); the API's own auth uses managed identity.
3. Networking: subnet within the standard hub-spoke topology, NSGs per tier, private endpoints for any PaaS dependencies.
4. Deployment: Terraform-based provisioning via CI/CD (per [`terraform/ci-cd.md`](terraform/ci-cd.md)), not manual portal changes.
5. Observability: Azure Monitor/Log Analytics wired in from day one, not bolted on after launch.

**Follow-ups**: "What would you do differently if this needed to handle PCI data?" / "How would you estimate the cost before launch?"

**Evaluation criteria**: Structured completeness (identity, network, deployment, observability all mentioned), awareness of governance defaults, not jumping straight to "just create a VM."

---

## Round 2 — Cloud Architecture

**Interviewer question**: "Design DR for a business-critical application with an RTO of 30 minutes and RPO of 5 minutes."

**Expected thinking**: This is the canonical worked scenario in [`azure/disaster-recovery.md`](azure/disaster-recovery.md) — the interviewer wants to see the RTO/RPO-first reasoning process, not a memorized answer.

**Strong answer outline**: See the full worked answer in [`azure/disaster-recovery.md`](azure/disaster-recovery.md) — warm standby (not full active-active), near-continuous replication for the RPO, automated failover, regular drills, layered backup for the corruption/ransomware case replication alone doesn't cover.

**Follow-ups**: "How would you validate RPO is actually being met in production?" / "What changes if RTO drops to 5 minutes?"

**Evaluation criteria**: Correctly maps requirement to pattern (doesn't over-engineer to active-active), addresses both replication and backup, mentions testing as non-optional.

---

## Round 3 — Platform / Kubernetes

**Interviewer question**: "Design a self-service cloud platform that allows developers to provision compliant infrastructure without requiring direct access to cloud subscriptions/projects."

**Expected thinking**: This is the explicitly flagged canonical Platform Architect prompt in this repository — see the full worked answer in [`platform-engineering/self-service.md`](platform-engineering/self-service.md).

**Strong answer outline**: Service catalog interface → scoped, federated pipeline execution identity → policy-as-code gate → audit trail → fast exception path for genuine edge cases.

**Follow-ups**: "How would you extend this to support a genuinely novel infrastructure need the catalog doesn't cover?" / "How do you decide what's safe to fully self-service versus needing manual review?"

**Evaluation criteria**: Recognizes self-service ≠ broad access grant, mentions policy-as-code as the enforcement layer (not just documentation), has a real exception path (not "no exceptions ever").

---

## Round 4 — Security / Governance

**Interviewer question**: "How would you implement Zero Trust across Azure and GCP, given a limited budget and existing legacy systems?"

**Expected thinking**: Tests prioritization under real-world constraint, not textbook Zero Trust recitation — see [`security/zero-trust.md`](security/zero-trust.md).

**Strong answer outline**: Identity first (MFA/Conditional Access) → least-privilege remediation (PIM rollout) → workload identity for service-to-service → micro-segmentation for highest-risk workloads first → multi-year roadmap communicated explicitly to leadership, not a single "done" project.

**Follow-ups**: "How would you measure progress/maturity to report to leadership?" / "How do you handle a legacy system that fundamentally can't support modern identity signals?"

**Evaluation criteria**: Prioritizes correctly under constraint (identity first, not everything at once), acknowledges legacy system limitations honestly rather than hand-waving, frames as ongoing not one-time.

---

## Round 5 — AI/GenAI Architecture

**Interviewer question**: "Design an enterprise RAG platform."

**Expected thinking**: This tests both technical RAG depth (see [`ai-genai/rag.md`](ai-genai/rag.md)) and, critically, honest framing of your actual hands-on maturity per the repository-wide disclaimer.

**Strong answer outline**:
1. Open with an honest maturity statement if relevant (per [`ai-genai/README.md`](ai-genai/README.md)).
2. Data layer with sensitivity metadata preserved through ingestion.
3. **Access control enforced at the retrieval layer**, not just the application layer — this is the single detail that most distinguishes a strong answer from a shallow one.
4. Hybrid search + re-ranking, not vector-only.
5. Source-attributed generation for trust/verification.
6. Ongoing evaluation (multi-dimensional: relevance, faithfulness, safety, cost) — not a one-time launch check.

**Follow-ups**: "How would you handle a business unit's data that must never be retrievable by another's users, even indirectly?" / "How would you diagnose a specific case where RAG confidently produced a wrong answer?"

**Evaluation criteria**: Catches the retrieval-time access-control requirement unprompted, doesn't overclaim production experience, demonstrates real architectural reasoning even if the maturity label is `Reference Architecture`.

---

## Round 6 — Principal Architect / Leadership

**Interviewer question**: "Tell me about a time you disagreed with a technical decision, and how you handled it."

**Expected thinking**: Pure behavioral — tests communication, influence, and self-awareness, not technical depth. See [`leadership/behavioral.md`](leadership/behavioral.md) for a worked example.

**Strong answer outline** (STAR):
- **Situation/Task**: brief, sets stakes.
- **Action**: the majority of the answer — what you specifically did, how you approached the disagreement constructively (asked questions rather than just asserting), what evidence/pilot you used.
- **Result**: quantified outcome.
- **Reflection**: what you'd do differently or what became a standing practice afterward.

**Follow-ups**: "What would you have done if they hadn't changed their mind?" / "How do you know when to stop pushing and just support the decision?"

**Evaluation criteria**: Uses "I" not just "we," shows genuine influence technique (not "I was right and they eventually saw it"), includes authentic reflection, doesn't throw the other person under the bus.

---

## Running These as Practice

1. Set a timer — 3–5 minutes per answer, matching real interview pacing.
2. Answer out loud (recording yourself is more effective than silent reading).
3. After answering, check yourself against the "Evaluation criteria" — did you hit those specific signals?
4. Do the follow-up questions too, not just the primary prompt — most real interviews go at least one level deeper.
