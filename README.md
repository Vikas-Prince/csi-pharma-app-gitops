# 📘 csi-java-pharma-app GitOps Repository

Welcome to the **GitOps repository** for `csi-java-pharma-app` — a full-stack enterprise-ready Java application deployed and managed using **ArgoCD**, **Kustomize**, **Kyverno**, and **progressive deployment strategies** with **Argo Rollouts**.

This repository follows a **Hub-and-Spoke GitOps architecture** where:
- **Dev environment** (hub cluster) hosts the central ArgoCD instance.
- ArgoCD from dev manages deployments to **QA**, **Staging**, and **Production** (spoke clusters).

---

## 📁 Repository Structure

```bash
java-app-gitops/
├── base/                   # Base manifests (common across all envs)
├── environments/          # Overlay configurations per environment
│   ├── dev/
│   ├── qa/
│   ├── staging/
│   └── prod/
├── apps/                  # ArgoCD Application definitions per environment
├── policies/              # Kyverno admission control policies
│   ├── kyverno/
│   │   ├── validation/
│   │   ├── mutation/
│   │   └── generation/
│   └── kustomization.yaml
└── README.md              # This file
```

---

## 🚀 Environments & Deployment Strategies

| Environment | Deployment Type     | Strategy       | Description                              |
|-------------|---------------------|----------------|------------------------------------------|
| **Dev**     | Rollout (Recreate)  | Fast Replace   | Immediate testing, no downtime concerns  |
| **QA**      | Deployment (Rolling)| RollingUpdate  | Stable rollout for automated testing     |
| **Staging** | Rollout (Canary)    | Progressive    | Weighted rollout with pause & checks     |
| **Prod**    | Rollout (Canary)    | Full Canary    | Safe deployment with multi-step analysis |

- **Services:** Canary and stable traffic split via `canary`/`stable` services.
- **Argo Rollouts UI:** Optionally integrated to visualize rollout progress.

---

## 🔄 GitOps with ArgoCD

ArgoCD is installed on the **dev cluster** and manages apps across all clusters.

- Each environment has its own **Application manifest** under `apps/`
- **Auto-sync**, **pruning**, and **self-healing** are enabled
- Uses **Kustomize overlays** to parameterize differences per environment

---

## 📦 Kubernetes Resources (Per Environment)

Each environment includes the following:

- ✅ `Deployment` or `Rollout` (depending on strategy)
- ✅ `Service` (Stable + Canary)
- ✅ `HPA` (Horizontal Pod Autoscaler)
- ✅ `PodDisruptionBudget` (PDB)
- ✅ `NetworkPolicy` (Default Deny)
- ✅ `Ingress` or IngressRoute
- ✅ `Secrets` / `ConfigMaps`
- ✅ Health Probes (`liveness`, `readiness`)

---

## 🛡️ Policy Enforcement with Kyverno

Kyverno policies ensure **security**, **compliance**, and **operational consistency**:

### 🔍 Validation Policies
- Block images using `:latest` tag

### ✏️ Mutation Policies
- Auto-inject default CPU & memory limits

### ⚙️ Generation Policies
- Auto-generate a `default-deny` NetworkPolicy for every new namespace

These are defined under `policies/kyverno/` and are automatically synced via ArgoCD.

---

## ✅ Kustomize Base-Overlay Pattern

All environments share a **common base** in `/base/`, and individual overlays patch only what’s needed:

- `rollout-strategy`, `image`, `replica count`, and env-specific labels are overridden in overlays
- Ensures **reusability**, **scalability**, and **clean separation** of concerns


---

## 📌 Conclusion

This GitOps repository implements:
- Enterprise-grade **multi-environment deployment strategies**
- Clean and scalable **Kustomize overlays**
- Secure and compliant **Kyverno-based admission control**
- Centralized **ArgoCD hub-spoke architecture**
- Real-world patterns from modern cloud-native delivery pipelines

---

For questions or improvements, feel free to raise a PR or contact the maintainer.
