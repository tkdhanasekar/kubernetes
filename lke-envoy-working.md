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
kubectl get crd | grep gateway
```
```
kubectl create namespace envoy-gateway-system
```
```
helm install eg oci://docker.io/envoyproxy/gateway-helm   --version v1.4.2   -n envoy-gateway-system   --create-namespace   --skip-crds
```
```
kubectl get pods -n envoy-gateway-system
```
```
kubectl get gatewayclass
```
```
vim gatewayclass.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: GatewayClass
metadata:
  name: eg
spec:
  controllerName: gateway.envoyproxy.io/gatewayclass-controller
```
```  
kubectl apply -f gatewayclass.yaml
```
```
kubectl create namespace web
```
```
vim nginx-app.yaml
```
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: web
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.27
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: web
spec:
  selector:
    app: nginx
  ports:
  - name: http
    port: 80
    targetPort: 80
```
```
kubectl apply -f nginx-app.yaml
```
```
vim gateway.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: web-gateway
  namespace: web
spec:
  gatewayClassName: eg
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    hostname: nginx.hashlabs.in
    allowedRoutes:
      namespaces:
        from: Same
```
```
kubectl apply -f gateway.yaml
```
```
vim httproute.yaml
```
```
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: nginx-route
  namespace: web
spec:
  parentRefs:
  - name: web-gateway
  hostnames:
  - nginx.hashlabs.in
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: nginx
      port: 80
```
```
kubectl apply -f httproute.yaml
```
```
kubectl get gateway -n web
```
```
kubectl get gateway web-gateway -n web -o yaml
```
curl -H "Host: nginx.hashlabs.in" http://<LB-IP>
