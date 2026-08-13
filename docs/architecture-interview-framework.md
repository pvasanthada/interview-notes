# Whiteboard / Architecture Interview Framework

**Label:** `Interview Preparation`

A practical, repeatable methodology for live whiteboard system-design interviews.

## 1. The 13-Step Whiteboard Method

1. **Clarify requirements** — functional scope, out-of-scope items.
2. **Identify users** — who/what consumes this system (humans, services, batch jobs).
3. **Define workloads** — read-heavy vs write-heavy, batch vs real-time, sync vs async.
4. **Define scale** — requests/sec, data volume, growth rate, peak-to-average ratio.
5. **Define availability** — target SLA, acceptable downtime, RTO/RPO if applicable.
6. **Define data flow** — sketch the path of a single request/event end to end.
7. **Draw high-level architecture** — boxes for major components, not implementation detail yet.
8. **Add networking** — ingress, load balancing, segmentation, private connectivity.
9. **Add identity/security** — auth, authz, secrets, encryption boundaries.
10. **Add observability** — metrics/logs/traces, alerting, SLOs.
11. **Add DR** — backup/replication strategy, failover approach.
12. **Add cost** — call out the 2–3 biggest cost drivers and how you'd control them.
13. **Explain trade-offs** — proactively state what you didn't choose and why.

## 2. Timing Guide (45-minute session)

| Phase | Time |
|---|---|
| Requirements & clarification | 5–7 min |
| High-level architecture | 10–12 min |
| Deep dive (interviewer-directed) | 15–18 min |
| Trade-offs, failure modes, cost | 5–8 min |
| Q&A / wrap-up | remaining |

## 3. What Great Candidates Do Differently

- They **draw before they talk in depth** — a rough box diagram anchors the conversation.
- They **narrate their reasoning**, not just the final answer ("I'm putting a queue here because we don't need synchronous confirmation, which also protects downstream from bursts").
- They **invite interruption**: "Let me know if you want me to go deeper anywhere before I move on."
- They **manage their own time** — principal candidates self-police pacing rather than needing the interviewer to redirect them.
- They **know their own diagram's weak points** and mention them before being asked.

## 4. Common Failure Patterns

- Diving into implementation detail (specific instance types, exact YAML) before the shape of the system is agreed.
- Designing for infinite scale when the prompt implied a modest, well-bounded system.
- Ignoring cost and treating every choice as if budget is unconstrained.
- Not asking about existing platform standards (most orgs already have a cloud, a CI/CD system, a service mesh — reinventing all of it signals inexperience).
- Forgetting to revisit and simplify the diagram at the end if it became overly complex.

## 5. Reusable Diagram Templates

Baseline shapes to have ready mentally (see [`diagrams/`](../diagrams/)):

- Three-tier web app with edge, app, and data layers
- Event-driven pipeline (producer → queue/stream → consumer → store)
- Hub-spoke network topology
- Landing zone (management groups/org → subscriptions/projects → workloads)
- RAG pipeline (ingestion → embedding → vector store → retrieval → LLM → response)

## 6. Closing the Interview Strong

End every design with a one-sentence summary and a stated next step:

> "So to summarize: an event-driven, multi-region-active-passive platform behind a regional load balancer, with Kafka as the backbone, Postgres for transactional state, and object storage for cold data — optimized for a 99.95% SLA at moderate cost. If I had more time I'd want to dig into the exact partitioning strategy for the queue."
