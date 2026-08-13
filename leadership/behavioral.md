# Behavioral — Worked Examples

**Label:** `Interview Preparation`

These are illustrative worked examples showing STAR structure applied to common principal-level behavioral prompts. Use the structure — replace the specifics with your own real experiences.

## "Tell me about a time you disagreed with a technical decision."

**Situation**: A team lead wanted to adopt a new, unproven message queue technology for a critical payment pipeline, primarily because a blog post from a well-known company praised it.

**Task**: As the architect reviewing the design, I needed to either validate the choice or make the case for an alternative, without simply overruling the team lead's ownership of their system.

**Action**: I asked what specific requirements were driving the choice, and it became clear the actual need (reliable at-least-once delivery, moderate throughput) was well served by the team's existing, already-operated queue technology. I didn't just say no — I asked the team lead to write a one-page comparison against the requirements, which surfaced the mismatch themselves. I also proposed a fallback: if they still wanted to explore the new technology, we could pilot it on a lower-risk, non-payment workload first to build operational confidence before considering it for the critical path.

**Result**: The team lead chose to stick with the existing technology for the payment pipeline and piloted the new one on an internal analytics workload instead. Six months later, that pilot surfaced real operational gaps that would have caused a production incident on the payment path.

**Reflection**: I learned that leading someone to their own conclusion through structured questions is usually more durable and relationship-preserving than simply overruling a decision, even when you're confident you're right.

## "Tell me about a time you influenced without direct authority."

**Situation**: A security policy I believed was necessary (default-deny network policy across a shared Kubernetes cluster) required buy-in from eight independent application teams I had no authority over.

**Task**: Drive adoption without a mandate, in an organization where teams could reasonably push back.

**Action**: Rather than announcing the policy, I ran a small pilot with one willing team, measured the actual friction it introduced (a handful of missing allow-rules, fixed in under a day), and used that concrete data — not a hypothetical argument — to present to the other teams. I also built the specific NetworkPolicy templates for the most common patterns so adoption was closer to a copy-paste than a from-scratch exercise.

**Result**: Six of eight teams adopted the policy within a month using the templates; the remaining two had genuine architectural reasons for a phased approach, which I worked with them to schedule rather than forcing.

**Reflection**: Concrete evidence from a real pilot, plus removing friction from adoption, moved teams far faster than argument or authority would have.

## "Tell me about a time a decision you made turned out wrong."

**Situation**: I recommended a customer-facing feature be built on a newly-released managed AI service to move fast, before the service had a track record at our required scale.

**Task**: Deliver the feature on an aggressive timeline while managing the risk of an unproven dependency.

**Action**: The service had an unannounced regional capacity limit that we hit during a promotional traffic spike, causing degraded response times for several hours. I owned the decision publicly rather than attributing it to the vendor, worked with the on-call team to add a graceful degradation path (a simpler, cached fallback response) as an immediate mitigation, and then led a retrospective that resulted in a new standing practice: any new managed service adopted for a customer-facing critical path now requires an explicit load test at expected peak before launch, regardless of timeline pressure.

**Result**: The immediate incident was mitigated within a few hours, and the new pre-launch load-testing practice has since caught two other capacity issues before they reached production.

**Reflection**: Moving fast on genuinely new technology for a critical path needs a validation step proportional to the blast radius, even under timeline pressure — and owning the miss publicly built more trust than it cost.
