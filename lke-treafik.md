Here’s a complete working setup to deploy an NGINX app on a Kubernetes cluster in Akamai Linode Linode Kubernetes Engine using Traefik with **host-based routing** via the Kubernetes Gateway API.

This uses:

* LKE cluster
* Traefik as Gateway Controller
* Gateway API (`Gateway`, `HTTPRoute`)
* Host-based routing (`nginx.example.com`)

---

# 1. Create LKE Cluster

Open:

[Linode Kubernetes Engine](https://cloud.linode.com/kubernetes/clusters?utm_source=chatgpt.com)

Create:

* Kubernetes version: latest stable
* Node pool: at least 2 nodes
* Region: nearest to your users

After cluster creation:

Download kubeconfig:

```bash
export KUBECONFIG=~/Downloads/lke-cluster.yaml
```

Verify:

```bash
kubectl get nodes
```

You should see worker nodes in `Ready` state.

---

# 2. Install Gateway API CRDs

Traefik Gateway Controller requires Kubernetes Gateway API CRDs. ([Traefik Docs][1])

Install them:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml
```

Verify:

```bash
kubectl get crds | grep gateway
```

---

# 3. Install Traefik with Gateway API Enabled

Add Helm repo:

```bash
helm repo add traefik https://helm.traefik.io/traefik
helm repo update
```

Create namespace:

```bash
kubectl create namespace traefik
```

Install Traefik:

```bash
helm install traefik traefik/traefik \
  --namespace traefik \
  --set providers.kubernetesGateway.enabled=true
```

Traefik supports Gateway API through the `kubernetesGateway` provider. ([Traefik Docs][1])

---

# 4. Verify Traefik Installation

Check pods:

```bash
kubectl get pods -n traefik
```

Check service:

```bash
kubectl get svc -n traefik
```

You should see something like:

```text
NAME      TYPE           EXTERNAL-IP
traefik   LoadBalancer   139.x.x.x
```

Copy the external IP.

---

# 5. Create GatewayClass

Create `gatewayclass.yaml`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: traefik
spec:
  controllerName: traefik.io/gateway-controller
```

Apply:

```bash
kubectl apply -f gatewayclass.yaml
```

---

# 6. Create Gateway

Create `gateway.yaml`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: default
spec:
  gatewayClassName: traefik
  listeners:
    - name: web
      protocol: HTTP
      port: 80
      hostname: "nginx.example.com"
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

Verify:

```bash
kubectl get gateway
```

You should see:

```text
PROGRAMMED=True
```

---

# 7. Deploy NGINX Application

Create `nginx-deploy.yaml`

```yaml
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
          image: nginx:latest
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
kubectl apply -f nginx-deploy.yaml
```

Verify:

```bash
kubectl get pods
kubectl get svc
```

---

# 8. Create HTTPRoute for Host-Based Routing

Create `httproute.yaml`

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
spec:
  parentRefs:
    - name: web-gateway
  hostnames:
    - "nginx.example.com"
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

# 9. Configure DNS

Point your domain/subdomain:

```text
nginx.example.com
```

to the Traefik LoadBalancer external IP.

Example:

```text
A Record:
nginx.example.com -> 139.x.x.x
```

If you don’t have DNS yet, test locally using `/etc/hosts`.

Linux/macOS:

```bash
sudo nano /etc/hosts
```

Add:

```text
139.x.x.x nginx.example.com
```

---

# 10. Test

Open:

```text
http://nginx.example.com
```

You should see:

```text
Welcome to nginx!
```

---

# 11. Useful Verification Commands

Check Gateway:

```bash
kubectl describe gateway web-gateway
```

Check HTTPRoute:

```bash
kubectl describe httproute nginx-route
```

Check Traefik logs:

```bash
kubectl logs -n traefik deploy/traefik
```

---

# Architecture

```text
Internet
   |
   v
Traefik LoadBalancer Service
   |
Gateway
   |
HTTPRoute (Host: nginx.example.com)
   |
NGINX Service
   |
NGINX Pods
```

---

# Optional: Enable HTTPS with Let's Encrypt

Later you can add:

* cert-manager
* Traefik TLS listener
* ACME/Let's Encrypt

for automatic HTTPS.

---

# Complete File Structure

```text
gatewayclass.yaml
gateway.yaml
nginx-deploy.yaml
httproute.yaml
```

---

# Official References

* [Traefik Gateway API Docs](https://doc.traefik.io/traefik/reference/install-configuration/providers/kubernetes/kubernetes-gateway/?utm_source=chatgpt.com)
* [Traefik Helm Chart](https://helm.traefik.io/traefik?utm_source=chatgpt.com)
* [Gateway API Project](https://gateway-api.sigs.k8s.io/?utm_source=chatgpt.com)
* [Linode Kubernetes Engine Docs](https://www.linode.com/docs/guides/kubernetes/?lang=es&utm_source=chatgpt.com)

([Traefik Docs][2])

[1]: https://doc.traefik.io/traefik/reference/install-configuration/providers/kubernetes/kubernetes-gateway/?utm_source=chatgpt.com "Traefik Kubernetes Gateway API Documentation - Traefik"
[2]: https://doc.traefik.io/traefik-hub/api-gateway/setup/installation/lke?utm_source=chatgpt.com "Deploy Traefik Hub Gateway & API Management on Linode Kubernetes Engine (LKE) | Traefik Hub Documentation"

