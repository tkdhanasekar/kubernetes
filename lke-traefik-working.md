```
kubectl get nodes
```
```
kubectl apply -f https://github.com/kubernetes-sigs/gateway-api/releases/download/v1.5.1/standard-install.yaml
```
```
helm repo add traefik https://helm.traefik.io/traefik
```
```
helm repo update
```
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
```
helm install traefik traefik/traefik   --namespace traefik   --create-namespace   -f traefik-values.yaml
```
```
kubectl get gatewayclass
```
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
```
kubectl apply -f gateway.yaml
```
```
kubectl get gateway
```
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
```
kubectl apply -f nginx-app.yaml
```
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
```
kubectl apply -f httproute.yaml
```
```
kubectl get httproute
```
nginx.hashlabs.in -> <LKE_LOADBALANCER_IP>

http://nginx.hashlabs.in

