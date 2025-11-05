<details>
  <summary>Topics in K8S</summary>

## 1. Core Kubernetes & Cloud-Native Fundamentals

* what is cloud native
* what are containers
* container orchestration
* kubernetes architecture
* infrastructure setup
* cluster configuration and initialization
* cluster security and management
* testing and validation
* advanced setup deploy with kubeadm
* kubernetes software versions
* cluster upgrade
* backup and restore methods
* update kubernetes frequently
* verify platform binaries before deploying

---

## 2. Core Kubernetes Components

* api server
* controller manager
* scheduler
* etcd
* kubelet
* kube proxy

---

## 3. Container Runtime & Storage Foundations

* docker vs containerd
* container runtime interface (cri)
* volume driver plugins in docker
* docker storage
* docker storage volumes
* container storage interface (csi)
* persistent volumes (pv)
* persistent volume claims (pvc)
* storage classes
* dynamic volume provisioning
* using pvc in pods

---

## 4. Kubernetes Workloads & Controllers

* pods
* pods with yaml
* multi container pods
* multi container pods design patterns
* init containers
* replicasets
* deployments
* daemonsets
* stateful sets
* jobs
* cron jobs
* static pods

---

## 5. Deployment & Update Strategies

* rolling updates and rollbacks
* blue green deployment strategy
* canary deployment strategy
* scale applications
* autoscaling
* horizontal pod autoscaler (hpa)
* vertical pod autoscaler (vpa)
* event-driven autoscaling with keda

---

## 6. Configuration & Environment Management

* commands and arguments
* environment variables
* configmaps
* secrets
* lifecycle management with helm

---

## 7. Scheduling & Node Management

* manual scheduling
* labels and selectors
* taints and tolerations
* node selectors
* node affinity
* taints and tolerations vs node affinity
* multiple schedulers
* configuring kubernetes scheduler profiles
* resource quotas and limits
* quality of service
* node

---

## 8. Networking

* docker networking
* container networking
* cluster networking
* pod networking
* network namespaces
* container networking interface (cni)
* weave
* ipam weave
* dns
* coredns
* services
* cluster ip
* load balancer
* headless services
* ingress
* the need for gateway api
* introduction to gateway api and resource model
* configure a gateway resource
* expose a deployment on the gateway
* traffic switching
* network policies
* network troubleshooting
* common networking issues

---

## 9. Kubernetes Security

### 9.1 Authentication & Authorization

* kubernetes security primitives
* authentication
* authorization
* api groups
* role based access controls (rbac)
* cluster roles
* service accounts
* attribute based access control (abac)
* kubeconfig

### 9.2 Certificates & Encryption

* tls basics
* tls in kubernetes
* pki certificates and api
* mtls

### 9.3 Pod & Workload Security

* security contexts
* image security
* imagepolicywebhook
* secrets
* pod security
* admission controllers
* validating and mutating admission controllers
* runtime classes
* container sandboxing
* gvisor
* kata containers
* restrict syscalls using seccomp
* implement seccomp in kubernetes
* apparmor
* selinux basics
* linux capabilities
* kubelet security

### 9.4 Infrastructure & Node Security

* ssh hardening
* minimize host os footprint
* minimize base image footprint
* minimize external access to the network
* minimize iam roles
* limit node access
* restrict kernel modules
* identify and disable open ports
* protection strategies
* endpoint security
* ufw firewall basics

### 9.5 Security Monitoring & Compliance

* audit logging
* falco
* detect threats across infrastructure, apps, networks, data, users, and workloads
* detect all attack phases, regardless of location or spread
* perform behavioral analytics of syscall process
* cis benchmarks
* kube-bench
* open policy agent (opa)
* compliance frameworks
* threat modelling frameworks
* supply chain compliance
* artifact repository security
* image security
* scan images for known vulnerabilities (trivy)
* use static analysis of user workloads

### 9.6 Advanced Security

* kubernetes isolation techniques
* control plane isolation
* multi-tenancy
* least privilege principle
* mutable vs immutable infrastructure
* cloud provider security
* infrastructure security
* network policies
* trust boundaries and data flow
* privilege escalation
* denial of service (dos)
* malicious code execution
* compromised applications in containers
* access to sensitive data

---

## 10. Observability & Operations

* monitor cluster components
* managing application / container logs
* slo/sla/sli
* prometheus
* cost management

---

## 11. GitOps, Helm & Kustomize

* helm overview
* helm installation
* helm charts
* helm components
* helm concepts
* customizing helm chart params
* kustomize overview
* kustomize vs helm
* kustomize installation
* kustomize.yaml file
* kustomize apiversion and kind
* kustomize output
* managing directories with kustomize
* common kustomize transformers
* kustomize patches
* kustomize different types of patches
* kustomize patches list
* kustomize dictionary patches
* kustomize overlays
* kustomize components

---

## 12. Kubernetes Extensibility & Ecosystem

* custom resource definition (crd)
* custom controllers
* operator framework
* kubernetes enhancement proposal (kep)
* kubernetes special interest groups (sig)
* open standards

---

## 13. Service Mesh, Networking & API Gateways

* sidecars
* envoy
* istio
* cilium
* serverless

---

## 14. Security & Compliance Tools

* trivy
* falco
* aquasec tracee
* kubelinter
* sbom

---

## 15. DevSecOps & Cloud-Native Security Principles

* the 4cs of cloud native security
* workload and application code security
* cloud provider security
* infrastructure security
* artifact repository security
* image security
* api server
* kubeconfig
* audit logging

---

## 16. Failure & Troubleshooting Scenarios

* application failure
* control plane failure
* worker node failure
* troubleshoot services and networking
* network troubleshooting
* troubleshooting the api server, scheduler

---

</details>

