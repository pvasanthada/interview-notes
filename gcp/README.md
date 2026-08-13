# GCP — Interview Preparation

**Label:** `Reference Architecture` / `Learning-Overview` (secondary platform — mark actual depth honestly)

Senior/principal-level GCP architecture material, with Azure comparisons where useful for candidates coming from an Azure-primary background.

## Contents

| File | Covers |
|---|---|
| [landing-zones.md](landing-zones.md) | Organization, folders, projects |
| [networking.md](networking.md) | Shared VPC, Cloud NAT, Private Service Connect |
| [iam.md](iam.md) | IAM, service accounts, workload identity |
| [security.md](security.md) | Security Command Center, encryption |
| [governance.md](governance.md) | Organization Policy |
| [compute.md](compute.md) | Compute Engine, Cloud Run |
| [gke.md](gke.md) | Google Kubernetes Engine |
| [monitoring.md](monitoring.md) | Cloud Logging, Cloud Monitoring |
| [disaster-recovery.md](disaster-recovery.md) | HA/DR on GCP |
| [scenario-questions.md](scenario-questions.md) | GCP-specific scenarios |

## Note on Conceptual Comparisons

Where this repository maps Azure and GCP concepts side by side, these are **conceptual comparisons for architectural reasoning**, not exact feature equivalents:

```text
Azure Management Groups   ↕   GCP Folders / Organization
Azure Subscription        ↕   GCP Project
Azure RBAC                ↕   GCP IAM
```
