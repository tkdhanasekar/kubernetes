export the kubeconfig file
```
export KUBECONFIG=$PWD/lke-demo-kubeconfig.yaml
```
check for the validation of the cluster
```
kubectl get nodes
```
## Install Gateway API
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
Verify:
```
kubectl get crds | grep gateway
```
## Install NGINX Gateway Fabric
```
helm install ngf oci://ghcr.io/nginx/charts/nginx-gateway-fabric --namespace nginx-gateway --create-namespace
```
Wait:
```
kubectl wait --timeout=5m -n nginx-gateway deployment/ngf-nginx-gateway-fabric --for=condition=Available
```
Verify GatewayClass
```
kubectl get gatewayclass
```
## Install cert-manager
Add Jetstack Helm repo
```
helm repo add jetstack https://charts.jetstack.io
```
update helm repo
```
helm repo update
```
Create namespace
```
kubectl create namespace cert-manager
```
Install cert-manager WITH Gateway API enabled
```
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.20.2 --set crds.enabled=true --set extraArgs={--enable-gateway-api}
```
Verify installation
```
kubectl get pods -n cert-manager
```
Verify:
```
kubectl get deployment cert-manager -n cert-manager -o yaml | grep enable-gateway-api
```
verify:
```
kubectl get crds
```
update:
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
verify:
```
kubectl get crds
```
## Create Let's Encrypt ClusterIssuer
```
vim clusterissuer.yaml
```
```
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
```
kubectl apply -f clusterissuer.yaml
```
## Deploy orange Application
```
vim orange-app.yaml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-app

spec:
  replicas: 2

  selector:
    matchLabels:
      app: orange-app

  template:
    metadata:
      labels:
        app: orange-app

    spec:
      containers:
        - name: orange

          image: dnadna/orange:latest

          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: orange-app

spec:
  selector:
    app: orange-app

  ports:
    - port: 80
      targetPort: 80

  type: ClusterIP
```
apply:
```
kubectl apply -f orange-app.yaml
```
## Create Gateway
```
vim gateway.yaml
```
```
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

    hostname: orange.hashlabs.in

    allowedRoutes:
      namespaces:
        from: All

  - name: https

    protocol: HTTPS

    port: 443

    hostname: orange.hashlabs.in

    tls:
      mode: Terminate

      certificateRefs:
      - kind: Secret
        name: orange-hashlabs-tls

    allowedRoutes:
      namespaces:
        from: All
```
apply:
```
kubectl apply -f gateway.yaml
```
## Create TLS Certificate
```
vim certificate.yaml
```
```
apiVersion: cert-manager.io/v1
kind: Certificate
metadata:
  name: orange-hashlabs-cert
  namespace: default

spec:
  secretName: orange-hashlabs-tls

  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer

  dnsNames:
    - orange.hashlabs.in
```
apply certificate.yaml
```
kubectl apply -f certificate.yaml
```
verify gateway:
```
kubectl get gateway
```
Get LoadBalancer IP
```
kubectl get svc -A
```
Verify:
```
nslookup orange.hashlabs.in
```
Check:
```
kubectl get certificate
```
```
kubectl get challenge
```
```
kubectl get order
```
Verify secret:
```
kubectl get secret orange-hashlabs-tls
```
## Create HTTPRoute
```
vim httproute.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute

metadata:
  name: orange-route
  namespace: default

spec:
  parentRefs:
    - name: public-gateway

  hostnames:
    - orange.hashlabs.in

  rules:
    - backendRefs:
        - name: orange-app
          port: 80
```
Apply:
```
kubectl apply -f httproute.yaml
```
## Create a dedicated redirect route.
```
vim redirect.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute

metadata:
  name: redirect-http-to-https

spec:
  parentRefs:
    - name: public-gateway
      sectionName: http

  hostnames:
    - orange.hashlabs.in

  rules:
    - filters:
        - type: RequestRedirect

          requestRedirect:
            scheme: https
            statusCode: 301
```
Apply:
```
kubectl apply -f redirect.yaml
```
Validate Gateway
```
kubectl get gateway
```
Route:
```
kubectl get httproute
```
Verify http:
```
curl -I http://orange.hashlabs.in
```
Verify https:
```
curl -I https://orange.hashlabs.in
```
