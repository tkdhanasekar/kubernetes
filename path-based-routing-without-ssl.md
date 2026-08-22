```
export KUBECONFIG=$PWD/lke-demo-kubeconfig.yaml
```
```
kubectl get nodes
```
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
```
kubectl get crds | grep gateway
```
```
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric --namespace nginx-gateway --create-namespace
```
```
kubectl wait --timeout=5m -n nginx-gateway deployment/ngf-nginx-gateway-fabric --for=condition=Available
```
```
kubectl get gatewayclass
```
```
kubectl get pods -n nginx-gateway
```
```
kubectl get gatewayclass
```
```
kubectl create namespace apps
```
```
vim nginx.yaml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app
  namespace: apps
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
  namespace: apps
spec:
  selector:
    app: nginx-app
  ports:
  - port: 80
    targetPort: 80
```
```   
kubectl apply -f nginx.yaml
```
```
vim httpd.yaml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpd-app
  namespace: apps
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpd-app
  template:
    metadata:
      labels:
        app: httpd-app
    spec:
      containers:
      - name: httpd
        image: httpd:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpd-service
  namespace: apps
spec:
  selector:
    app: httpd-app
  ports:
  - port: 80
    targetPort: 80
```
```   
kubectl apply -f httpd.yaml
```
```
vim gateway.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: demo-gateway
  namespace: apps
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    hostname: demo.hashlabs.in
```
```    
kubectl apply -f gateway.yaml
```
```
kubectl get gateway -n apps
```
```
vim httproute-app1.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: app1-route
  namespace: apps
spec:
  parentRefs:
  - name: demo-gateway

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
    - name: nginx-service
      port: 80
```
```     
kubectl apply -f httproute-app1.yaml
```
```
vim httproute-app2.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: app2-route
  namespace: apps
spec:
  parentRefs:
  - name: demo-gateway

  hostnames:
  - demo.hashlabs.in

  rules:
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
    - name: httpd-service
      port: 80
```
```
kubectl apply -f httproute-app2.yaml
```
```
kubectl get svc -A
```
```
kubectl get gateway -n apps
```
```
curl http://demo.hashlabs.in/app1
```
```
curl http://demo.hashlabs.in/app2
```
https://chatgpt.com/s/t_6a29bbf435e08191a0aa3222d2e0e7ba

---

To enable TLS with **Let's Encrypt** for `demo.hashlabs.in` using the Kubernetes Gateway API and NGINX Gateway Fabric, you'll typically use **cert-manager** to automatically obtain and renew certificates.

One important limitation:

* Let's Encrypt issues certificates for **hostnames**, not URL paths.
* `/app1` and `/app2` will share the same certificate because both are served from the same host: `demo.hashlabs.in`.

## Architecture

```text
Internet
    |
Let's Encrypt
    |
cert-manager
    |
TLS Certificate
    |
Secret (tls-secret)
    |
Gateway
    |
+-------------------+
| demo.hashlabs.in  |
+-------------------+
       |
  HTTPRoute
   /      \
 /app1   /app2
 nginx   httpd
```

---

# 1. Install cert-manager

Install CRDs:

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.crds.yaml
```

Install cert-manager:

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

All pods should be Running.

---

# 2. Find Gateway External IP

Check your Gateway:

```bash
kubectl get gateway -n apps
```

Example:

```text
NAME           CLASS   ADDRESS         PROGRAMMED
demo-gateway   nginx   139.162.x.x     True
```

Alternatively:

```bash
kubectl get svc -n nginx-gateway
```

Find the LoadBalancer IP.

---

# 3. Create DNS Record

Create an A record:

```text
demo.hashlabs.in -> <Gateway External IP>
```

Verify:

```bash
dig demo.hashlabs.in
```

or

```bash
nslookup demo.hashlabs.in
```

Must return your Gateway IP.

---

# 4. Install Gateway API Support for cert-manager

Recent cert-manager versions support Gateway API.

Check:

```bash
kubectl get crd gateways.gateway.networking.k8s.io
```

Already installed in your setup.

Restart cert-manager after Gateway API installation if needed:

```bash
kubectl rollout restart deployment cert-manager -n cert-manager
```

---

# 5. Create ClusterIssuer

Create:

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
        gatewayHTTPRoute:
          parentRefs:
          - name: demo-gateway
            namespace: apps
            kind: Gateway
```

Apply:

```bash
kubectl apply -f clusterissuer.yaml
```

Verify:

```bash
kubectl get clusterissuer
```

---

# 6. Request Certificate

Create:

```yaml
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: demo-hashlabs-cert
  namespace: apps
spec:
  secretName: demo-hashlabs-tls

  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer

  dnsNames:
  - demo.hashlabs.in
```

Apply:

```bash
kubectl apply -f certificate.yaml
```

Check:

```bash
kubectl get certificate -n apps
```

Watch:

```bash
kubectl get certificaterequest -n apps
```

Check challenge:

```bash
kubectl get challenges -A
```

Wait until:

```text
READY=True
```

---

# 7. Configure TLS on Gateway

Modify `gateway.yaml`:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: demo-gateway
  namespace: apps
spec:
  gatewayClassName: nginx

  listeners:
  - name: https
    protocol: HTTPS
    port: 443

    hostname: demo.hashlabs.in

    tls:
      mode: Terminate
      certificateRefs:
      - kind: Secret
        name: demo-hashlabs-tls

    allowedRoutes:
      namespaces:
        from: Same
```

Apply:

```bash
kubectl apply -f gateway.yaml
```

---

# 8. Keep HTTP for Redirect (Optional)

You can keep port 80 and redirect to HTTPS:

```yaml
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
    - name: demo-hashlabs-tls
```

---

# 9. Verify Certificate Secret

```bash
kubectl get secret demo-hashlabs-tls -n apps
```

Expected:

```text
TYPE
kubernetes.io/tls
```

---

# 10. Test

```bash
curl -I https://demo.hashlabs.in/app1
```

```bash
curl -I https://demo.hashlabs.in/app2
```

Or:

```bash
openssl s_client -connect demo.hashlabs.in:443 -servername demo.hashlabs.in
```

You should see a valid Let's Encrypt certificate.

---

# Optional: Redirect HTTP → HTTPS

Add a dedicated HTTPRoute:

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: redirect-http
  namespace: apps
spec:
  parentRefs:
  - name: demo-gateway

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

Then:

```bash
http://demo.hashlabs.in/app1
```

automatically becomes:

```text
https://demo.hashlabs.in/app1
```

This gives you a production-ready setup with NGINX Gateway Fabric + Gateway API + cert-manager + Let's Encrypt for both `/app1` and `/app2` under the same TLS-enabled hostname `demo.hashlabs.in`.

