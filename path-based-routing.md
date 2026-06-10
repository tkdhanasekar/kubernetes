Below is a production-oriented workflow for deploying applications on Akamai (LKE) using NGINX Gateway Fabric, cert-manager, and the Kubernetes Gateway API.

### Target Architecture

```text
Internet
    │
    ▼
demo.hashlabs.in
    │
    ▼
LKE LoadBalancer
    │
    ▼
NGINX Gateway Fabric
    │
    ├── /app1 → app1-service
    │
    └── /app2 → app2-service

TLS:
Let's Encrypt
      │
      ▼
cert-manager
      │
      ▼
Gateway HTTPS Listener
```

Traffic flow:

```text
http://demo.hashlabs.in/app1
                │
                ▼
       301 Redirect HTTPS
                │
                ▼
https://demo.hashlabs.in/app1
                │
                ▼
            app1-service

https://demo.hashlabs.in/app2
                │
                ▼
            app2-service
```

---

# 1. Create LKE Cluster

Create an LKE cluster:

```bash
linode-cli lke cluster-create \
  --label ngf-demo \
  --region ap-south \
  --k8s_version 1.31
```

Download kubeconfig:

```bash
linode-cli lke kubeconfig-view <cluster-id> --json
```

Verify:

```bash
kubectl get nodes
```

---

# 2. Install Gateway API CRDs

```bash
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.4.0/standard-install.yaml
```

Verify:

```bash
kubectl get crds | grep gateway
```

Gateway API is the successor to Kubernetes Ingress and provides resources such as GatewayClass, Gateway, and HTTPRoute. ([kubernetes.nginx.org][1])

---

# 3. Install NGINX Gateway Fabric

Add Helm repository:

```bash
helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
```

Install NGF:

```bash
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric \
  --namespace nginx-gateway \
  --create-namespace
```

Verify:

```bash
kubectl get pods -n nginx-gateway
```

NGINX Gateway Fabric is a conformant implementation of the Kubernetes Gateway API. ([kubernetes.nginx.org][1])

---

# 4. Create GatewayClass

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: nginx
spec:
  controllerName: gateway.nginx.org/nginx-gateway-controller
```

Apply:

```bash
kubectl apply -f gatewayclass.yaml
```

Verify:

```bash
kubectl get gatewayclass
```

---

# 5. Deploy Sample Applications

## App1

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app1-service
spec:
  selector:
    app: app1
  ports:
  - port: 80
    targetPort: 80
```

---

## App2

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  selector:
    app: app2
  ports:
  - port: 80
    targetPort: 80
```

Apply:

```bash
kubectl apply -f app1.yaml
kubectl apply -f app2.yaml
```

---

# 6. Install cert-manager

Install CRDs:

```bash
kubectl apply -f \
https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.crds.yaml
```

Install Helm chart:

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace
```

Gateway API integration is supported by cert-manager for automatic certificate issuance. ([cert-manager][2])

---

# 7. Create Let's Encrypt ClusterIssuer

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: admin@hashlabs.in
    server: https://acme-v02.api.letsencrypt.org/directory

    privateKeySecretRef:
      name: letsencrypt-prod

    solvers:
    - http01:
        gatewayHTTPRoute: {}
```

Apply:

```bash
kubectl apply -f clusterissuer.yaml
```

---

# 8. Create Gateway

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: demo-gateway
  annotations:
    cert-manager.io/cluster-issuer: letsencrypt-prod
spec:
  gatewayClassName: nginx

  listeners:

  - name: http
    protocol: HTTP
    port: 80
    hostname: demo.hashlabs.in

  - name: https
    protocol: HTTPS
    port: 443
    hostname: demo.hashlabs.in

    tls:
      mode: Terminate
      certificateRefs:
      - kind: Secret
        name: demo-hashlabs-tls
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

cert-manager can automatically populate the TLS Secret referenced by the Gateway listener. ([cert-manager][2])

---

# 9. Configure HTTP → HTTPS Redirect

Create an HTTPRoute:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: redirect-http
spec:
  parentRefs:
  - name: demo-gateway
    sectionName: http

  hostnames:
  - demo.hashlabs.in

  rules:
  - filters:
    - type: RequestRedirect
      requestRedirect:
        scheme: https
        statusCode: 301
```

Apply:

```bash
kubectl apply -f redirect.yaml
```

---

# 10. Configure Path-Based Routing

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: demo-route
spec:
  parentRefs:
  - name: demo-gateway
    sectionName: https

  hostnames:
  - demo.hashlabs.in

  rules:

  - matches:
    - path:
        type: PathPrefix
        value: /app1
        
    filters:
    - type: URLRewrite
      urlRewrite:
        path:
          type: ReplacePrefixMatch
          replacePrefixMatch: /

    backendRefs:
    - name: app1-service
      port: 80

  - matches:
    - path:
        type: PathPrefix
        value: /app2
        
    filters:
    - type: URLRewrite
      urlRewrite:
        path:
          type: ReplacePrefixMatch
          replacePrefixMatch: /

    backendRefs:
    - name: app2-service
      port: 80
```

Apply:

```bash
kubectl apply -f httproute.yaml
```

---

# 11. Configure DNS

Get external IP:

```bash
kubectl get gateway
```

or

```bash
kubectl get svc -A
```

Find the LoadBalancer IP created by NGF.

Create DNS:

```text
A Record

demo.hashlabs.in
      →
<gateway-public-ip>
```

Wait for propagation.

---

# 12. Verify Certificate

```bash
kubectl get certificate
kubectl get certificaterequests
kubectl get challenges
```

Expected:

```text
READY=True
```

Verify:

```bash
curl -I https://demo.hashlabs.in/app1
```

---

# 13. Validate Routing

```bash
curl https://demo.hashlabs.in/app1
```

Response from App1.

```bash
curl https://demo.hashlabs.in/app2
```

Response from App2.

HTTP redirect:

```bash
curl -I http://demo.hashlabs.in/app1
```

Expected:

```text
301 Moved Permanently
Location: https://demo.hashlabs.in/app1
```

---

## Final Resource Flow

```text
GatewayClass
     │
     ▼
Gateway
     │
     ├── Listener HTTP:80
     │        └── Redirect → HTTPS
     │
     └── Listener HTTPS:443
              │
              ▼
        Let's Encrypt TLS
              │
              ▼
          HTTPRoute
           /      \
          /        \
      /app1      /app2
        │          │
        ▼          ▼
    app1-svc   app2-svc
```

This setup gives you:

* Kubernetes Gateway API
* NGINX Gateway Fabric
* Automatic Let's Encrypt certificates
* HTTP → HTTPS redirection
* Path-based routing (`/app1`, `/app2`)
* Single domain (`demo.hashlabs.in`)
* Production-ready TLS automation via cert-manager.

[1]: https://kubernetes.nginx.org/?utm_source=chatgpt.com "NGINX on Kubernetes"
[2]: https://cert-manager.io/docs/usage/gateway/?utm_source=chatgpt.com "Annotated Gateway resource - cert-manager Documentation"

