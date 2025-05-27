# 🛡️ Kyverno Learn-by-Doing Series

Welcome! This repo contains a step-by-step Kyverno hands-on learning series designed to help you **understand and apply policies** with real Kubernetes resources using Minikube.

> Built for DevSecOps engineers, platform teams, and curious K8s users.

---

## 📚 Series Overview

| Part | Topic                             | Folder                              |
|------|-----------------------------------|-------------------------------------|
| 1    | Block Privileged Pods             | `part-1-block-privileged-pods/`     |
| 2    | Kyverno Architecture & Flow       | `part-2-architecture-flow/`         |
| 3    | Verify Signed Images with Cosign  | `part-3-verify-signed-images/`      |
| 4    | Auto Inject Labels (Mutate)       | `part-4-auto-label-injection/`      |
| 5    | Scan + Report Violations          | `part-5-policy-reports/`            |
| 6    | Auto Generate ConfigMaps/RBAC     | `part-6-generate-resources/`        |

Each folder has:
- 📄 `README.md` with hands-on steps
- 🔐 Kyverno policy YAMLs
- 🧪 Test resources
- 🎨 Visuals (for relevant parts)

---

## 🚀 Getting Started

```bash
minikube start --memory=4096 --cpus=2
kubectl apply -f https://github.com/kyverno/kyverno/releases/download/v1.14.0/install.yaml