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
