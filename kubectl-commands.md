There are many kubectl commands and subcommands, and the exact set can vary by Kubernetes version and installed plugins. Here’s a comprehensive practical reference, grouped by purpose.

1. Basic kubectl commands
kubectl version — Display client/server version
kubectl cluster-info — Display cluster information
kubectl config — Manage kubeconfig
kubectl api-resources — List supported API resources
kubectl api-versions — List supported API versions
kubectl explain — Explain Kubernetes resources
kubectl help — Show help
kubectl options — List global command-line options
2. Create and apply resources
kubectl create — Create resources
kubectl apply — Create/update resources from manifests
kubectl replace — Replace a resource
kubectl patch — Update specific fields
kubectl edit — Edit a resource interactively
kubectl delete — Delete resources

Common examples:

kubectl apply -f deployment.yaml
kubectl create -f deployment.yaml
kubectl delete -f deployment.yaml
kubectl edit deployment nginx
kubectl patch deployment nginx -p '{"spec":{"replicas":3}}'

3. Get and inspect resources
kubectl get — List resources
kubectl describe — Detailed resource information
kubectl explain — Resource/API documentation
kubectl get pods
kubectl get pods -A
kubectl get pods -o wide
kubectl get deployments
kubectl get services
kubectl describe pod nginx
kubectl describe node node-1


You can generally use:

kubectl get <resource>
kubectl get <resource> <name>
kubectl describe <resource> <name>

4. Pods
kubectl get pods
kubectl describe pod
kubectl logs
kubectl exec
kubectl port-forward
kubectl attach
kubectl cp
kubectl delete pod
kubectl run

Examples:

kubectl run nginx --image=nginx
kubectl get pods
kubectl logs nginx
kubectl logs -f nginx
kubectl exec -it nginx -- /bin/bash
kubectl exec -it nginx -- /bin/sh
kubectl port-forward pod/nginx 8080:80
kubectl cp nginx:/tmp/file ./file
kubectl delete pod nginx

5. Deployments
kubectl get deployments
kubectl describe deployment
kubectl create deployment
kubectl scale
kubectl rollout
kubectl set
kubectl autoscale
kubectl delete deployment

Examples:

kubectl create deployment nginx --image=nginx
kubectl scale deployment nginx --replicas=5
kubectl set image deployment/nginx nginx=nginx:1.27
kubectl rollout status deployment/nginx
kubectl rollout history deployment/nginx
kubectl rollout undo deployment/nginx
kubectl rollout restart deployment/nginx

6. Rollouts

kubectl rollout has several subcommands:

kubectl rollout status
kubectl rollout history
kubectl rollout pause
kubectl rollout resume
kubectl rollout restart
kubectl rollout undo
kubectl rollout status deployment/nginx
kubectl rollout history deployment/nginx
kubectl rollout pause deployment/nginx
kubectl rollout resume deployment/nginx
kubectl rollout restart deployment/nginx
kubectl rollout undo deployment/nginx

7. Services
kubectl get services
kubectl describe service
kubectl expose
kubectl delete service
kubectl expose deployment nginx --port=80 --type=ClusterIP
kubectl get svc
kubectl describe svc nginx


Service types include:

ClusterIP
NodePort
LoadBalancer
ExternalName
8. Nodes
kubectl get nodes
kubectl describe node
kubectl cordon
kubectl uncordon
kubectl drain
kubectl top node
kubectl get nodes -o wide
kubectl describe node node-1
kubectl cordon node-1
kubectl drain node-1 --ignore-daemonsets
kubectl uncordon node-1
kubectl top nodes

9. Namespaces
kubectl get namespaces
kubectl create namespace
kubectl delete namespace
kubectl describe namespace
kubectl get ns
kubectl create ns development
kubectl delete ns development
kubectl get pods -n development

10. ConfigMaps
kubectl get configmaps
kubectl create configmap
kubectl describe configmap
kubectl edit configmap
kubectl delete configmap
kubectl create configmap app-config --from-literal=ENV=production
kubectl get configmap
kubectl describe configmap app-config

11. Secrets
kubectl get secrets
kubectl create secret
kubectl describe secret
kubectl edit secret
kubectl delete secret
kubectl create secret generic db-secret \
  --from-literal=username=admin \
  --from-literal=password=secret

kubectl get secrets
kubectl describe secret db-secret

12. StatefulSets
kubectl get statefulsets
kubectl describe statefulset
kubectl scale statefulset
kubectl rollout
kubectl delete statefulset
kubectl get sts
kubectl describe sts mysql
kubectl scale sts mysql --replicas=3

13. DaemonSets
kubectl get daemonsets
kubectl describe daemonset
kubectl rollout
kubectl delete daemonset
kubectl get ds
kubectl describe ds fluentd
kubectl rollout status daemonset/fluentd

14. Jobs
kubectl get jobs
kubectl create job
kubectl describe job
kubectl delete job
kubectl create job test --image=busybox -- echo "Hello Kubernetes"
kubectl get jobs
kubectl describe job test

15. CronJobs
kubectl get cronjobs
kubectl create cronjob
kubectl describe cronjob
kubectl delete cronjob
kubectl create cronjob hello \
  --image=busybox \
  --schedule="*/5 * * * *" \
  -- echo "Hello"

kubectl get cronjobs

16. Storage
PersistentVolumes
kubectl get pv
kubectl describe pv <name>
kubectl delete pv <name>

PersistentVolumeClaims
kubectl get pvc
kubectl describe pvc <name>
kubectl delete pvc <name>

StorageClasses
kubectl get storageclass
kubectl describe storageclass <name>

17. Labels and selectors
kubectl label
kubectl annotate
kubectl label pod nginx environment=production
kubectl label pod nginx environment-
kubectl annotate pod nginx description="web server"


Select resources:

kubectl get pods -l app=nginx
kubectl get pods -l environment=production

18. Debugging

Useful commands include:

kubectl logs
kubectl exec
kubectl attach
kubectl port-forward
kubectl cp
kubectl debug
kubectl describe
kubectl events
kubectl logs nginx
kubectl logs nginx -c nginx
kubectl logs nginx --previous
kubectl exec -it nginx -- sh
kubectl debug pod/nginx -it --image=busybox
kubectl events

19. Networking
kubectl get svc
kubectl get endpoints
kubectl get endpointslices
kubectl get ingress
kubectl describe ingress <name>
kubectl port-forward svc/nginx 8080:80


You can also inspect:

kubectl get networkpolicies
kubectl describe networkpolicy <name>

20. Ingress
kubectl get ingress
kubectl describe ingress <name>
kubectl create ingress
kubectl delete ingress <name>


Example:

kubectl create ingress my-ingress \
  --rule="example.com/=my-service:80"

21. RBAC

Resources include:

Roles
RoleBindings
ClusterRoles
ClusterRoleBindings
ServiceAccounts
kubectl get roles
kubectl get rolebindings
kubectl get clusterroles
kubectl get clusterrolebindings
kubectl get serviceaccounts


Useful commands:

kubectl create role
kubectl create rolebinding
kubectl create clusterrole
kubectl create clusterrolebinding
kubectl auth can-i


For example:

kubectl auth can-i get pods
kubectl auth can-i create deployments
kubectl auth can-i get pods --as=user@example.com

22. Contexts and kubeconfig

kubectl config includes:

kubectl config view
kubectl config get-contexts
kubectl config current-context
kubectl config use-context
kubectl config set-context
kubectl config set-cluster
kubectl config set-credentials
kubectl config delete-context
kubectl config rename-context
kubectl config unset

Examples:

kubectl config get-contexts
kubectl config current-context
kubectl config use-context production
kubectl config view

23. Scaling
kubectl scale
kubectl autoscale
kubectl scale deployment nginx --replicas=10

kubectl autoscale deployment nginx \
  --min=2 \
  --max=10 \
  --cpu-percent=70

24. Resource usage

With Metrics Server installed:

kubectl top nodes
kubectl top pods
kubectl top pods -A

25. Events
kubectl get events
kubectl get events -A
kubectl events


Useful for diagnosing things like:

ImagePullBackOff
CrashLoopBackOff
Pending pods
Failed scheduling
Failed mounts
Failed probes
26. API discovery
kubectl api-resources
kubectl api-versions
kubectl explain pod
kubectl explain pod.spec
kubectl explain deployment.spec


For example:

kubectl explain deployment.spec.strategy

27. Output formats

Most kubectl get commands support:

-o wide
-o yaml
-o json
-o name
-o custom-columns=...
-o jsonpath=...


Examples:

kubectl get pods -o wide
kubectl get pod nginx -o yaml
kubectl get pod nginx -o json
kubectl get pods -o name


JSONPath:

kubectl get pods -o jsonpath='{.items[*].metadata.name}'

28. Wait and status
kubectl wait
kubectl rollout status
kubectl get
kubectl describe


Example:

kubectl wait --for=condition=Ready pod/nginx --timeout=60s

29. Certificate commands
kubectl certificate approve
kubectl certificate deny
kubectl certificate renew


These operate on Kubernetes CertificateSigningRequests (CSRs).

30. Common resource shortcuts

kubectl supports many short names:

po       pods
deploy   deployments
svc      services
rs       replicasets
sts      statefulsets
ds       daemonsets
cm       configmaps
secret   secrets
ns       namespaces
no       nodes
pv       persistentvolumes
pvc      persistentvolumeclaims
ing      ingresses
sa       serviceaccounts
job      jobs
cj       cronjobs


So:

kubectl get pods


can become:

kubectl get po


and:

kubectl get deployments


can become:

kubectl get deploy

31. Global options

Common options available across commands include:

--namespace / -n
--all-namespaces / -A
--context
--kubeconfig
--server
--user
--cluster
--output / -o
--selector / -l
--field-selector
--v
--request-timeout
--dry-run
--force


Examples:

kubectl get pods -n production
kubectl get pods -A
kubectl get pods -l app=nginx
kubectl apply -f app.yaml --dry-run=client

32. The easiest way to see every command available on your installation

Run:

kubectl


or:

kubectl --help


Then, for a particular command:

kubectl get --help
kubectl create --help
kubectl config --help
kubectl rollout --help
kubectl auth --help


And for the resources supported by your specific Kubernetes API server:

kubectl api-resources

Quick command hierarchy

A useful mental model is:

kubectl
├── apply
├── create
├── delete
├── edit
├── get
├── describe
├── explain
├── logs
├── exec
├── run
├── expose
├── scale
├── autoscale
├── rollout
├── set
├── label
├── annotate
├── patch
├── replace
├── wait
├── debug
├── cp
├── attach
├── port-forward
├── drain
├── cordon
├── uncordon
├── top
├── events
├── auth
├── certificate
├── config
├── cluster-info
├── api-resources
├── api-versions
├── version
├── explain
└── options


If you're learning Kubernetes, the 20 commands worth memorizing first are:

kubectl get
kubectl describe
kubectl create
kubectl apply
kubectl delete
kubectl edit
kubectl logs
kubectl exec
kubectl run
kubectl expose
kubectl scale
kubectl rollout
kubectl set
kubectl label
kubectl patch
kubectl port-forward
kubectl cp
kubectl top
kubectl config
kubectl explain
