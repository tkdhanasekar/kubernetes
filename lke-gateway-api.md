You can run a Kubernetes cluster on Linode in two ways:

1. **Use LKE (Linode Kubernetes Engine)** → managed Kubernetes (recommended)
2. Install Kubernetes manually on a Linode VM

For your use case — **multiple apps + Gateway API instead of NGINX Ingress** — the best setup is:

* LKE cluster
* Gateway API
* Envoy Gateway / Traefik / Kong as Gateway controller
* HTTPRoute resources for routing apps

This is cleaner and more modern than NGINX Ingress. ([Kubernetes][1])

---

# Recommended Architecture

```text
Internet
   |
Linode LoadBalancer (NodeBalancer)
   |
Gateway API Controller
(Envoy Gateway / Traefik / Kong)
   |
HTTPRoutes
   |
Apps (app1, app2, app3)
```

---

# Step 1 — Create Linode Kubernetes Engine (LKE)

Open:

[Linode Kubernetes Engine](https://www.linode.com/products/kubernetes?utm_source=chatgpt.com)

Create:

* Kubernetes cluster
* Region
* Node pool
* Kubernetes version

Or use CLI:

```bash
linode-cli lke cluster-create \
  --label my-cluster \
  --region ap-south \
  --k8s_version 1.30 \
  --node_pools.type g6-standard-2 \
  --node_pools.count 3
```

Get kubeconfig:

```bash
linode-cli lke kubeconfig-view CLUSTER_ID --text > kubeconfig
export KUBECONFIG=kubeconfig
```

Verify:

```bash
kubectl get nodes
```

---

# Step 2 — Install Gateway API CRDs

Gateway API is separate from Kubernetes core. ([Kubernetes][1])

Install CRDs:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.1.0/standard-install.yaml
```

Verify:

```bash
kubectl get crds | grep gateway
```

---

# Step 3 — Install a Gateway Controller

You need a controller implementation.

Recommended:

* Envoy Gateway → best modern choice
* Traefik → easiest
* Kong → API management focused

---

# Option A — Install Envoy Gateway (Recommended)

Install Helm:

```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

Add repo:

```bash
helm repo add eg https://envoyproxy.github.io/gateway-helm
helm repo update
```

Install:

```bash
helm install eg eg/gateway-helm \
  -n envoy-gateway-system \
  --create-namespace
```

Verify:

```bash
kubectl get pods -n envoy-gateway-system
```

---

# Step 4 — Create GatewayClass

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: envoy
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
```

Apply:

```bash
kubectl apply -f gatewayclass.yaml
```

---

# Step 5 — Create Gateway

This replaces NGINX Ingress.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: main-gateway
  namespace: default
spec:
  gatewayClassName: envoy
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    allowedRoutes:
      namespaces:
        from: All
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

Check external IP:

```bash
kubectl get gateway
```

Linode automatically creates a LoadBalancer/NodeBalancer. ([Akamai][2])

---

# Step 6 — Deploy Multiple Apps

Example:

```bash
kubectl create deployment app1 --image=nginx
kubectl expose deployment app1 --port=80

kubectl create deployment app2 --image=httpd
kubectl expose deployment app2 --port=80
```

---

# Step 7 — Route Traffic with HTTPRoute

## Route `/app1`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: app1-route
spec:
  parentRefs:
  - name: main-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /app1
    backendRefs:
    - name: app1
      port: 80
```

---

## Route `/app2`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: app2-route
spec:
  parentRefs:
  - name: main-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /app2
    backendRefs:
    - name: app2
      port: 80
```

Apply:

```bash
kubectl apply -f app1-route.yaml
kubectl apply -f app2-route.yaml
```

---

# Result

```text
http://LOADBALANCER-IP/app1
→ app1

http://LOADBALANCER-IP/app2
→ app2
```

---

# Optional — Domain Based Routing

```yaml
hostnames:
- api.example.com
```

Then route different domains:

* api.example.com
* admin.example.com
* app.example.com

---

# Optional — HTTPS / TLS

Install cert-manager:

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --set crds.enabled=true
```

Then use Let's Encrypt.

---

# Why Gateway API Instead of NGINX Ingress?

Gateway API advantages:

* cleaner routing model
* better multi-team separation
* TCP/UDP/HTTP/gRPC support
* future Kubernetes standard
* less annotation mess
* weighted routing/canary support

([Kubernetes][1])

---

# Recommended Stack for Production

## Simple + modern

* LKE
* Envoy Gateway
* cert-manager
* external-dns
* ArgoCD

---

# Alternative: Traefik Gateway API

Traefik also supports Gateway API well.

Docs:

[Traefik Hub on LKE](https://doc.traefik.io/traefik-hub/api-gateway/setup/installation/lke?utm_source=chatgpt.com)

---

# Minimal Production Setup

```text
Linode LKE
 ├── Envoy Gateway
 ├── Gateway API
 ├── cert-manager
 ├── ExternalDNS
 ├── Prometheus/Grafana
 └── Your apps
```

---

# If You Want

I can also give you:

* complete YAML manifests
* Terraform setup for LKE
* Helm-based deployment
* Gateway API + TLS setup
* domain configuration
* CI/CD with GitHub Actions
* multi-namespace routing
* autoscaling setup
* production security hardening
* setup using only one cheap Linode VM instead of LKE

[1]: https://kubernetes.io/docs/concepts/services-networking/gateway?utm_source=chatgpt.com "Gateway API | Kubernetes"
[2]: https://www.linode.com/products/kubernetes?utm_source=chatgpt.com "Managed Kubernetes | Linode Kubernetes Engine | Akamai"

