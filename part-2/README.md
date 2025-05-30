# Kyverno Learn-by-Doing — Part 2: Validation, Mutation, Generate, verifyImages

This part of the series demonstrates 4 practical Kyverno use cases:

- ✅ **Validation** — Require CPU & memory limits
- 🔁 **Mutation** — Inject `runAsNonRoot: true`
- 🧬 **Generate** — Auto-create `NetworkPolicy` on namespace creation
- 🔐 **verifyImages** — Enforce image signing with Cosign

Tested on **Kyverno v1.14.1** with **Minikube**.

---

## 🛠️ Setup

### 1. Clone and enter project:

```bash
git clone https://github.com/<your-org>/kyverno-learn-by-doing.git
cd kyverno-learn-by-doing/part-2
```

### 2. Confirm Kyverno is installed:

```bash
kubectl get pods -n kyverno
```

### 3. If not installed, install with Helm:

```bash
helm repo add kyverno https://kyverno.github.io/kyverno/
helm repo update

helm upgrade --install kyverno kyverno/kyverno \
  --namespace kyverno --create-namespace \
  --version 3.0.1 \
  --set installCRDs=true
```

---

## ✅ Use Case 1: Validation — Require CPU & Memory Limits

### Apply the policy:

```bash
kubectl apply -f uc1.yaml
```

### Test with valid pod:

```bash
kubectl apply -f uc1-complaint.yaml     # ✅ Should pass
```

### Test with invalid pod:

```bash
kubectl apply -f uc1-noncomplaint.yaml  # ❌ Should be blocked
```

### Validate the behavior:

```bash
kubectl get clusterpolicy
kubectl describe clusterpolicy require-resources
kubectl get policyreport -A
kubectl get events --field-selector involvedObject.name=uc1-fail-nolimits
```

---

## 🔁 Use Case 2: Mutation — Enforce `runAsNonRoot: true`

### Apply the mutation policy:

```bash
kubectl apply -f uc2.yaml
```

### Apply compliant pod:

```bash
kubectl apply -f uc2-compliant.yaml
```

### Apply noncompliant pod:

```bash
kubectl apply -f uc2-noncompliant.yaml
```

### Validate mutation:

```bash
kubectl get pod mutated-nginx -o yaml | grep runAsNonRoot
kubectl get pod mutated-nginx -o yaml | grep runAsUser
```

---

## 🧬 Use Case 3: Generate — Auto-create NetworkPolicy

### Apply the generate policy:

```bash
kubectl apply -f uc3.yaml
```

### Create a namespace:

```bash
kubectl create namespace secure-apps
```

### Validate generated NetworkPolicy:

```bash
kubectl get networkpolicy -n secure-apps
kubectl describe networkpolicy default-deny -n secure-apps
```

### Test regeneration:

```bash
kubectl delete networkpolicy default-deny -n secure-apps
sleep 5
kubectl get networkpolicy -n secure-apps
```

---

## 🔐 Use Case 4: verifyImages — Require Cosign Signature

### Install Cosign:

```bash
brew install cosign
# or for Linux:
# curl -sSL https://raw.githubusercontent.com/sigstore/cosign/main/install.sh | sh
```

### Generate key pair:

```bash
cosign generate-key-pair
```

### Push and sign your image:

```bash
docker pull busybox
docker tag busybox:latest yreddy007/busybox:signed-test
docker login
docker push yreddy007/busybox:signed-test
cosign sign --key cosign.key yreddy007/busybox:signed-test
```

### Apply `verifyImages` policy:

Paste your public key into `key: |` block in `uc4.yaml`, then:

```bash
kubectl apply -f uc4.yaml
```

### Test with signed image:

```bash
kubectl apply -f uc4-complaint.yaml
```

### Test with unsigned image:

```bash
kubectl apply -f uc4-noncomplaint.yaml
```

### Validate digest pinning:

```bash
kubectl get pod signed-busybox -o yaml | grep image
kubectl describe pod unsigned-busybox
kubectl get events | grep unsigned-busybox
```

---

## 🧹 Clean Up

```bash
kubectl delete clusterpolicy --all
kubectl delete -f uc4.yaml
kubectl delete ns secure-apps
kubectl delete pod \
  signed-busybox \
  unsigned-busybox \
  non-root-nginx \
  mutated-nginx \
  uc1-pass \
  uc1-fail-nolimits
```

---

## 📁 Included Files

```
├── uc1.yaml
├── uc1-complaint.yaml
├── uc1-noncomplaint.yaml
├── uc2.yaml
├── uc2-compliant.yaml
├── uc2-noncompliant.yaml
├── uc3.yaml
├── uc4.yaml
├── uc4-complaint.yaml
├── uc4-noncomplaint.yaml
```

---

## 📚 References

- 📘 https://kyverno.io/docs/
- 🔐 https://docs.sigstore.dev/cosign/overview
