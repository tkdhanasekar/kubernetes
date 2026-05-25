Here’s a complete setup guide for deploying NGINX Gateway Fabric on Linode Kubernetes Engine (LKE), then deploying a sample NGINX app exposed through Gateway API.

Useful docs:

* [NGINX Gateway Fabric Docs](https://docs.nginx.com/nginx-gateway-fabric/?utm_source=chatgpt.com)
* [NGINX Gateway Fabric Helm Install](https://docs.nginx.com/nginx-gateway-fabric/install/helm/?utm_source=chatgpt.com)
* [Linode Kubernetes Engine](https://www.linode.com/products/kubernetes/?utm_source=chatgpt.com)

---

# 1. Prerequisites

Install locally:

```bash
kubectl
helm
linode-cli   # optional
```

Verify access:

```bash
kubectl get nodes
```

You should see your LKE worker nodes.

---

# 2. Create LKE Cluster

Via UI or CLI.

Example CLI:

```bash
linode-cli lke cluster-create \
  --label lke-gateway \
  --region ap-south \
  --k8s_version 1.30 \
  --node_pools.type g6-standard-2 \
  --node_pools.count 3
```

Download kubeconfig:

```bash
linode-cli lke kubeconfig-view <cluster-id> --json \
| jq -r '.[0].kubeconfig' | base64 -d > kubeconfig
```

Use it:

```bash
export KUBECONFIG=$PWD/kubeconfig
```

Test:

```bash
kubectl get nodes
```

---

# 3. Install Gateway API CRDs

Install standard Gateway API resources:

```bash
kubectl kustomize \
"https://github.com/nginx/nginx-gateway-fabric/config/crd/gateway-api/standard?ref=v2.6.2" \
| kubectl apply -f -
```

This installs:

* GatewayClass
* Gateway
* HTTPRoute
* ReferenceGrant
* etc. ([NGINX Docs][1])

Verify:

```bash
kubectl get crd | grep gateway
```

---

# 4. Install NGINX Gateway Fabric

Create namespace:

```bash
kubectl create namespace nginx-gateway
```

Install using Helm:

```bash
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric \
  --namespace nginx-gateway \
  --create-namespace
```

Wait for pods:

```bash
kubectl get pods -n nginx-gateway
```

You should see:

```text
ngf-nginx-gateway-fabric-xxxx   Running
```

([NGINX Docs][1])

---

# 5. Verify GatewayClass

Check:

```bash
kubectl get gatewayclass
```

Expected:

```text
NAME           CONTROLLER
nginx          gateway.nginx.org/nginx-gateway-controller
```

---

# 6. Deploy Sample NGINX App

Create deployment:

```yaml
# nginx-app.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx-app
  template:
    metadata:
      labels:
        app: nginx-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.27
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx-app
  ports:
  - port: 80
    targetPort: 80
```

Apply:

```bash
kubectl apply -f nginx-app.yaml
```

Verify:

```bash
kubectl get pods
kubectl get svc
```

---

# 7. Create Gateway

Create Gateway resource:

```yaml
# gateway.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: nginx-gateway
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    allowedRoutes:
      namespaces:
        from: Same
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

Check:

```bash
kubectl get gateway
```

Expected:

```text
PROGRAMMED=True
```

---

# 8. Create HTTPRoute

Route traffic to the app.

```yaml
# httproute.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
spec:
  parentRefs:
  - name: nginx-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: nginx-service
      port: 80
```

Apply:

```bash
kubectl apply -f httproute.yaml
```

---

# 9. Get External IP

Find LoadBalancer IP:

```bash
kubectl get svc -n nginx-gateway
```

You’ll see something like:

```text
nginx-gateway   LoadBalancer   139.x.x.x
```

Get it directly:

```bash
kubectl get svc -n nginx-gateway
```

Open browser:

```text
http://<EXTERNAL-IP>
```

You should see:

```text
Welcome to nginx!
```

---

# 10. Test with curl

```bash
curl http://<EXTERNAL-IP>
```

---

# 11. Useful Debug Commands

Gateway status:

```bash
kubectl describe gateway nginx-gateway
```

Route status:

```bash
kubectl describe httproute nginx-route
```

NGF logs:

```bash
kubectl logs -n nginx-gateway deploy/ngf-nginx-gateway-fabric
```

---

# Architecture

```text
Internet
    ↓
Linode LoadBalancer
    ↓
NGINX Gateway Fabric
    ↓
Gateway API
    ↓
HTTPRoute
    ↓
NGINX Service
    ↓
NGINX Pods
```

---

# Optional Next Steps

You can later add:

* HTTPS/TLS with cert-manager
* Multiple domains
* Path-based routing
* Canary deployments
* Rate limiting
* WAF
* mTLS

Example:

```text
api.example.com -> app1
admin.example.com -> app2
```

---

# Full Apply Order

```bash
kubectl apply -f nginx-app.yaml
kubectl apply -f gateway.yaml
kubectl apply -f httproute.yaml
```

---

# Cleanup

```bash
kubectl delete -f httproute.yaml
kubectl delete -f gateway.yaml
kubectl delete -f nginx-app.yaml

helm uninstall ngf -n nginx-gateway
kubectl delete ns nginx-gateway
```

---

NGINX Gateway Fabric implements Kubernetes Gateway API using NGINX as the data plane. ([NGINX Docs][2])

[1]: https://docs.nginx.com/nginx-gateway-fabric/install/helm/?utm_source=chatgpt.com "Install NGINX Gateway Fabric with Helm | NGINX Documentation"
[2]: https://docs.nginx.com/nginx-gateway-fabric?utm_source=chatgpt.com "NGINX Gateway Fabric | NGINX Documentation"

