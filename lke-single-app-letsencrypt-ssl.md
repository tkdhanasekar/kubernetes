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
helm repo add jetstack https://charts.jetstack.io
```
```
helm repo update
```
```
kubectl create namespace cert-manager
```
```
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.20.2 --set crds.enabled=true --set extraArgs={--enable-gateway-api}
```
```
kubectl get pods -n cert-manager
```
```
kubectl get deployment cert-manager -n cert-manager -o yaml | grep enable-gateway-api
```
```
kubectl get crds
```
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
```
kubectl get crds
```
```
vim clusterissuer.yaml
```
```
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
```
kubectl apply -f clusterissuer.yaml
```
```
vim certificate.yaml
```
```
# certificate.yaml

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
```
kubectl apply -f orange-app.yaml
```
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
```
kubectl apply -f gateway.yaml
```
```
kubectl apply -f certificate.yaml
```
```
kubectl get gateway
```
```
kubectl get svc -A
```
```
nslookup orange.hashlabs.in
```
```
kubectl get certificate
```
```
kubectl get challenge
```
```
kubectl get order
```
```
kubectl get certificate
```
```
kubectl get secret orange-hashlabs-tls
```
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
```
kubectl apply -f httproute.yaml
```
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
```
kubectl apply -f redirect.yaml
```
```
kubectl get gateway
```
```
kubectl get httproute
```
```
curl -I http://orange.hashlabs.in
```
```
curl -I https://orange.hashlabs.in
```
