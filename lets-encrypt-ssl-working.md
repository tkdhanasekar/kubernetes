Below is a complete end-to-end workflow for deploying an application on **Linode Kubernetes Engine (LKE)** using **NGINX Gateway Fabric (NGF)** with:

* Gateway API
* GatewayClass
* Gateway
* HTTPRoute
* Host-based routing for `apple.hashlabs.in`
* HTTP → HTTPS
* Let's Encrypt TLS certificates
* Cert-Manager integration
* NGINX application backend

Architecture:

```text
Internet
    |
    v
apple.hashlabs.in
    |
    v
Linode NodeBalancer (created by NGF Service)
    |
    v
NGINX Gateway Fabric
    |
    v
Gateway
    |
    v
HTTPRoute
    |
    v
nginx application service
    |
    v
nginx pods
```

---

# 1. Prerequisites

## Domain

Create DNS record:

```dns
apple.hashlabs.in  -->  <LKE LoadBalancer IP>
```

Initially you won't know the IP.

After NGF is deployed:

```bash
kubectl get svc -n nginx-gateway
```

Example:

```bash
NAME            TYPE           EXTERNAL-IP
nginx-gateway   LoadBalancer   139.x.x.x
```

Point DNS:

```dns
apple.hashlabs.in A 139.x.x.x
```

Verify:

```bash
dig apple.hashlabs.in
```

---

# 2. Create LKE Cluster

From the Linode Cloud Manager:

* Kubernetes
* Create Cluster
* Region: Chennai / Mumbai / nearest
* Version: latest supported
* Add worker pool

Or CLI:

```bash
linode-cli lke cluster-create \
  --label prod-lke \
  --region in-maa \
  --k8s_version 1.31
```

Download kubeconfig:

```bash
export KUBECONFIG=lke.yaml
kubectl get nodes
```

---

# 3. Install Gateway API CRDs

NGF requires Gateway API.

```bash
kubectl apply -f \
https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.2.1/standard-install.yaml
```

Verify:

```bash
kubectl get crds | grep gateway
```

Expected:

```text
gatewayclasses.gateway.networking.k8s.io
gateways.gateway.networking.k8s.io
httproutes.gateway.networking.k8s.io
```

---

# 4. Install NGINX Gateway Fabric

Create namespace:

```bash
kubectl create namespace nginx-gateway
```

Install CRDs:

```bash
kubectl apply -f \
https://raw.githubusercontent.com/nginx/nginx-gateway-fabric/main/deploy/crds.yaml
```

Install NGF:

```bash
kubectl apply -f \
https://raw.githubusercontent.com/nginx/nginx-gateway-fabric/main/deploy/default/deploy.yaml
```

Verify:

```bash
kubectl get pods -n nginx-gateway
```

Expected:

```text
nginx-gateway-fabric-xxxxx Running
```

---

# 5. Verify GatewayClass

Check:

```bash
kubectl get gatewayclass
```

Expected:

```text
nginx
```

or

```text
nginx-gateway
```

Depending on NGF version.

Example:

```bash
kubectl get gatewayclass
```

```text
NAME      CONTROLLER
nginx     gateway.nginx.org/nginx-gateway-controller
```

---

# 6. Install Cert Manager

Install CRDs:

```bash
kubectl apply -f \
https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.crds.yaml
```

Install:

```bash
helm repo add jetstack https://charts.jetstack.io

helm repo update

helm install cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace
```

Verify:

```bash
kubectl get pods -n cert-manager
```

---

# 7. Create Let's Encrypt ClusterIssuer

File:

```yaml
# clusterissuer.yaml

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
          gatewayHTTPRoute:
            parentRefs:
              - name: public-gateway
                namespace: default
```

Apply:

```bash
kubectl apply -f clusterissuer.yaml
```

---

# 8. Create TLS Certificate

File:

```yaml
# certificate.yaml

apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: apple-hashlabs-cert
  namespace: default

spec:
  secretName: apple-hashlabs-tls

  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer

  dnsNames:
    - apple.hashlabs.in
```

Do not apply yet until Gateway exists.

---

# 9. Deploy NGINX Application

Deployment:

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
```

Apply:

```bash
kubectl apply -f nginx-deployment.yaml
```

---

# 10. Create Service

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-app

spec:
  selector:
    app: nginx-app

  ports:
    - port: 80
      targetPort: 80

  type: ClusterIP
```

Apply:

```bash
kubectl apply -f nginx-service.yaml
```

---

# 11. Create Gateway

File:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway

metadata:
  name: public-gateway
  namespace: default

spec:
  gatewayClassName: nginx

  listeners:

  - name: http

    protocol: HTTP

    port: 80

    hostname: apple.hashlabs.in

    allowedRoutes:
      namespaces:
        from: All

  - name: https

    protocol: HTTPS

    port: 443

    hostname: apple.hashlabs.in

    tls:
      mode: Terminate

      certificateRefs:
      - kind: Secret
        name: apple-hashlabs-tls

    allowedRoutes:
      namespaces:
        from: All
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

---

# 12. Get LoadBalancer IP

```bash
kubectl get gateway
```

or

```bash
kubectl get svc -A
```

Find external IP:

```text
139.x.x.x
```

Update DNS:

```dns
apple.hashlabs.in -> 139.x.x.x
```

Wait for propagation.

Verify:

```bash
nslookup apple.hashlabs.in
```

---

# 13. Create Certificate

Now apply:

```bash
kubectl apply -f certificate.yaml
```

Check:

```bash
kubectl get certificate
```

```bash
kubectl get challenge
```

```bash
kubectl get order
```

Eventually:

```text
READY=True
```

Verify secret:

```bash
kubectl get secret apple-hashlabs-tls
```

---

# 14. Create HTTPRoute

Host-based routing.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute

metadata:
  name: apple-route
  namespace: default

spec:
  parentRefs:
    - name: public-gateway

  hostnames:
    - apple.hashlabs.in

  rules:
    - backendRefs:
        - name: nginx-app
          port: 80
```

Apply:

```bash
kubectl apply -f httproute.yaml
```

---

# 15. HTTP → HTTPS Redirect

Create a dedicated redirect route.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute

metadata:
  name: redirect-http-to-https

spec:
  parentRefs:
    - name: public-gateway
      sectionName: http

  hostnames:
    - apple.hashlabs.in

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

# 16. Validate Gateway

Gateway:

```bash
kubectl get gateway
```

Expected:

```text
PROGRAMMED=True
```

Route:

```bash
kubectl get httproute
```

Expected:

```text
Accepted=True
ResolvedRefs=True
```

---

# 17. Verify

HTTP:

```bash
curl -I http://apple.hashlabs.in
```

Expected:

```text
301 Moved Permanently
Location: https://apple.hashlabs.in
```

HTTPS:

```bash
curl -I https://apple.hashlabs.in
```

Expected:

```text
HTTP/2 200
```

Certificate:

```bash
openssl s_client \
-connect apple.hashlabs.in:443 \
-servername apple.hashlabs.in
```

Issuer:

```text
Let's Encrypt
```

---

# Production-ready directory structure

```text
k8s/
├── cert-manager/
│   ├── clusterissuer.yaml
│   └── certificate.yaml
│
├── gateway/
│   ├── gateway.yaml
│   ├── redirect.yaml
│   └── httproute.yaml
│
├── app/
│   ├── deployment.yaml
│   └── service.yaml
│
└── README.md
```

Deployment order:

```bash
kubectl apply -f gateway-api-crds.yaml

kubectl apply -f nginx-gateway-fabric.yaml

kubectl apply -f clusterissuer.yaml

kubectl apply -f app/

kubectl apply -f gateway/gateway.yaml

# update DNS

kubectl apply -f certificate.yaml

kubectl apply -f gateway/redirect.yaml

kubectl apply -f gateway/httproute.yaml
```

This gives you a fully Gateway API–native setup on LKE using NGINX Gateway Fabric, automatic Let's Encrypt certificates, HTTPS termination at the Gateway, HTTP→HTTPS redirects, and host-based routing for `apple.hashlabs.in`.

