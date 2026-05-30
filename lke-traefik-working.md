check for the validation kubernetes cluster
```
kubectl get nodes
```
Install Gateway API
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
Add the Traefik Helm Repository
```
helm repo add traefik https://helm.traefik.io/traefik
```
update the repo
```
helm repo update
```
Create a Helm Values File
```
vim traefik-values.yaml
```
```
providers:
  kubernetesGateway:
    enabled: true

service:
  type: LoadBalancer

gatewayClass:
  enabled: true

gateway:
  enabled: false

ports:
  web:
    port: 80
  websecure:
    port: 443
```
Install Traefik
```
helm install traefik traefik/traefik   --namespace traefik   --create-namespace   -f traefik-values.yaml
```
Verify GatewayClass
```
kubectl get gatewayclass
```
Create a Gateway
```
vim gateway.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: default
spec:
  gatewayClassName: traefik
  listeners:
  - name: http
    protocol: HTTP
    port: 80
```
Apply:
```
kubectl apply -f gateway.yaml
```
Verify:
```
kubectl get gateway
```
create service and deployment file for nginx app
```
vim nginx-app.yaml
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
```
kubectl apply -f nginx-app.yaml
```
Create HTTPRoute
```
vim httproute.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route

spec:
  parentRefs:
    - name: web-gateway

  hostnames:
    - "nginx.hashlabs.in"

  rules:
    - backendRefs:
        - name: nginx-service
          port: 80
```
Apply:
```
kubectl apply -f httproute.yaml
```
Verify:
```
kubectl get httproute
```
point load balancer ip to dns
nginx.hashlabs.in -> <LKE_LOADBALANCER_IP>

check:
http://nginx.hashlabs.in

