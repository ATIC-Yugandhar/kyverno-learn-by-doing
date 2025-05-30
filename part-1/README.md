# 👩‍💻 Part 1 – Block Privileged Pods (Nina the Platform Engineer)

Hi, I’m Nina — a platform engineer. One of my jobs is to make sure developers don’t accidentally (or intentionally) run containers in privileged mode. That’s a big no-no in our cluster.

In this hands-on example, I’m using **Kyverno** to write a simple policy that blocks privileged containers — and test it locally using Minikube.

---

## 🛠️ Prerequisites

- [Minikube (macOS ARM64 installer)](https://minikube.sigs.k8s.io/docs/start/?arch=%2Fmacos%2Farm64%2Fstable%2Fbinary+download)  
  → For other platforms, visit the [Minikube start guide](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)


---

## 🚀 Step-by-Step

### 1. Start Minikube

```bash
minikube start --memory=4096 --cpus=2
kubectl create namespace kyverno
kubectl apply -f https://github.com/kyverno/kyverno/releases/download/v1.14.0/install.yaml
kubectl get pods -n kyverno