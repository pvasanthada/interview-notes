# GCP — Scenario Questions

**Label:** `Interview Preparation`

## Foundation
1. Explain the GCP resource hierarchy and how you'd map it for a 3-business-unit company.
2. Walk through securing a newly created GCP project from default settings.

## Intermediate
3. A workload needs to call a third-party API and also authenticate to BigQuery — design the identity approach.
4. Compare Cloud Run and GKE Autopilot for a new set of stateless microservices.

## Senior
5. Design a Shared VPC topology for an organization with 20 service projects across 3 environments.
6. Your GCP bill spiked due to an inefficient BigQuery query pattern used across multiple teams. Diagnose and remediate.
7. Design a defense-in-depth approach to prevent data exfiltration from a sensitive BigQuery dataset.

## Principal
8. Design a GCP landing zone that must interoperate with an existing Azure-centric identity and logging platform.
9. Design multi-region DR for a globally distributed application requiring strict consistency.
10. Propose an organization-wide strategy to eliminate all downloadable service account keys within one quarter.

## Architecture Scenario
11. Design a self-service platform allowing application teams to provision GCP projects and baseline resources without direct console access, enforcing Organization Policy guardrails automatically.
12. Design an enterprise data platform on GCP (ingestion → BigQuery → serving) with appropriate governance, cost control, and security boundaries.
