To install **cert-manager with Gateway API support enabled**, you need to ensure two things:

1. Install cert-manager via Helm (recommended)
2. Enable the Gateway API feature flag: `--enable-gateway-api`

Below is a clean, production-safe installation flow for **LKE / any Kubernetes cluster**.

---

# 1. Add Jetstack Helm repo

```bash id="h1r8k2"
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

---

# 2. Create namespace

```bash id="n3k9p1"
kubectl create namespace cert-manager
```

---

# 3. Install cert-manager WITH Gateway API enabled

### Recommended (correct modern way)

```bash id="c7m2v9"
helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.20.2 \
  --set crds.enabled=true \
  --set extraArgs={--enable-gateway-api}
```

---

# 4. Verify installation

```bash id="v8p3k1"
kubectl get pods -n cert-manager
```

You should see:

```text id="ok1"
cert-manager
cert-manager-cainjector
cert-manager-webhook
```

---

# 5. Confirm Gateway API flag is enabled

```bash id="d4n7x2"
kubectl get deployment cert-manager -n cert-manager -o yaml | grep enable-gateway-api
```

Expected:

```text id="ok2"
--enable-gateway-api
```

---

# 6. Verify cert-manager is running correctly

```bash id="z9q1w4"
kubectl get crd | grep cert-manager
```

You should see:

* certificates
* certificaterequests
* challenges
* orders

---

# 7. Confirm ACME + Gateway solver support

Run:

```bash id="k2m9v8"
kubectl api-resources | grep acme
```

You should see:

* Challenge
* Order

---

# 8. IMPORTANT: Install Gateway API CRDs first

If not already installed:

```bash id="g6p3x8"
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml
```

---

# 9. ClusterIssuer example (Gateway API solver)

```yaml id="issuer1"
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: admin@example.com
    server: https://acme-v02.api.letsencrypt.org/directory

    privateKeySecretRef:
      name: letsencrypt-prod

    solvers:
    - http01:
        gatewayHTTPRoute:
          parentRefs:
          - name: public-gateway
            namespace: default
```

---

# 10. Common mistakes (important)

### ❌ Wrong installation (causes your earlier error)

```bash
helm install cert-manager ... --set crds.enabled=true --set extraArgs=...
```

BUT missing:

* Gateway API CRDs
* correct solver config
* correct cert-manager version (≥ v1.13 recommended)

---

### ❌ Missing Gateway API flag

If missing:

```text
gateway api is not enabled
```

Fix requires:

```bash
--set extraArgs={--enable-gateway-api}
```

---

# 11. Quick sanity checklist

After install, verify:

```bash id="check1"
kubectl get pods -n cert-manager
kubectl get gatewayclass
kubectl get crd | grep gateway
kubectl get clusterissuer
```

---

# 12. Minimal working install (copy-paste)

```bash id="final1"
helm repo add jetstack https://charts.jetstack.io
helm repo update

kubectl create namespace cert-manager

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --version v1.20.2 \
  --set crds.enabled=true \
  --set extraArgs={--enable-gateway-api}

kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml
```

