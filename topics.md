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

<details>
  <summary>Master List of K8S Topics</summary>

---

## 🧱 **Core Kubernetes Architecture**

* Kubernetes architecture
* Control plane components

  * etcd
  * API server
  * Controller manager
  * Scheduler
* Node components

  * Kubelet
  * Kube proxy
* Cluster configuration and initialization
* Infrastructure setup
* Cluster security and management
* Cluster upgrade
* Backup and restore methods
* etcd in HA
* etcdctl

---

## 🐳 **Containers & Runtimes**

* What are containers
* Container orchestration
* Docker vs containerd
* Container runtime interface (CRI)
* Docker storage
* Volume driver plugins in Docker
* Docker service configuration
* Docker securing the daemon
* Docker networking

---

## ⚙️ **Core Kubernetes Concepts**

* Pods
* Pods with YAML
* Multi-container pods
* Multi-container pod design patterns
* Init containers
* Static pods
* Commands and arguments
* Environment variables
* ConfigMaps
* Secrets
* Readiness probes
* Liveness probes
* DaemonSets
* ReplicaSets
* Deployments
* Rolling updates and rollbacks
* Deployment strategies (Blue-Green, Canary)
* Services

  * ClusterIP
  * LoadBalancer
  * Headless services
* Ingress
* Gateway API

  * Need for Gateway API
  * Introduction & resource model
  * Configure gateway resource
  * Expose deployment on gateway
  * Traffic switching

---

## 🧭 **Scheduling and Placement**

* Manual scheduling
* Labels and selectors
* Taints and tolerations
* Node selectors
* Node affinity
* Taints and tolerations vs node affinity
* Multiple schedulers
* Configuring Kubernetes scheduler profiles

---

## 📦 **Storage & Persistence**

* Container storage interface (CSI)
* Volumes
* Persistent Volumes (PV)
* Persistent Volume Claims (PVC)
* Using PVC in pods
* Storage classes
* Dynamic volume provisioning
* StatefulSets

---

## 🔐 **Security**

* Kubernetes security primitives
* Authentication
* Authorization
* Kubeconfig
* API groups
* TLS basics & TLS in Kubernetes
* PKI certificates and API
* Role-based access control (RBAC)

  * Cluster roles
  * Service accounts
* Attribute-based access control (ABAC)
* Image security

  * Scan images for vulnerabilities (Trivy)
  * Sign and validate images
  * ImagePolicyWebhook
  * Minimize base image footprint
* Security contexts
* Pod security
* Admission controllers

  * Validating and mutating admission controllers
* Network policies
* Kubelet security
* Secrets
* Audit logging
* Open Policy Agent (OPA)
* Seccomp
* AppArmor
* SELinux basics
* Linux capabilities
* Container sandboxing (gVisor, Kata containers)
* Runtime classes
* mTLS
* Multi-tenancy
* Control plane isolation
* Trust boundaries and data flow
* Endpoint security
* SSH hardening
* Privilege escalation prevention
* Minimize host OS footprint
* Disable open ports & obsolete packages
* UFW firewall basics
* CIS benchmarks
* Kube-bench
* Falco
* Threat detection and behavioral analytics
* Compliance frameworks & supply chain compliance
* Threat modelling frameworks
* Automation and tooling

---

## 🌐 **Networking**

* Cluster networking
* Pod networking
* Container networking
* Network namespaces
* Container Networking Interface (CNI)
* Weave & IPAM Weave
* CoreDNS
* Switching, routing, gateways in Kubernetes
* Network troubleshooting
* Common networking issues

---

## 📈 **Scaling & Performance**

* Scale applications
* Autoscaling

  * Horizontal Pod Autoscaler (HPA)
  * Vertical Pod Autoscaler (VPA)
  * Event-driven autoscaling with KEDA
* Resource quotas and limits
* Quality of Service (QoS)
* API Priority and Fairness

---

## 🧩 **Configuration & Management**

* Namespaces
* Imperative vs Declarative
* `kubectl apply` command
* Configuring cluster components
* Monitor cluster components
* Managing application/container logs

---

## ⚒️ **Deployment & Delivery**

* Infrastructure setup
* Testing and validation
* Advanced setup & deploy with kubeadm
* Application delivery fundamentals
* GitOps

  * Push vs pull-based deployments
  * CI/CD with GitOps
  * ArgoCD

---

## 📦 **Packaging & Customization**

* Helm overview
* Install Helm
* Helm concepts & components
* Helm charts
* Customizing Helm chart parameters
* Lifecycle management with Helm
* Kustomize overview
* Kustomize vs Helm
* Kustomize installation
* `kustomize.yaml` file
* Kustomize output
* API version and kind
* Managing directories
* Transformers, patches, overlays, components

---

## ⚙️ **Extensibility & Operators**

* Custom Resource Definitions (CRD)
* Custom controllers
* Operator framework
* Kubernetes extension interfaces
* Kubernetes Enhancement Proposal (KEP)
* Kubernetes Special Interest Groups (SIG)

---

## 💡 **Workload Types**

* Jobs
* CronJobs
* DaemonSets
* Deployments
* StatefulSets

---

## 🧠 **Monitoring, Logging & Reliability**

* Monitor cluster components
* Managing application/container logs
* Prometheus
* Application failure
* Control plane failure
* Worker node failure
* Troubleshoot services & networking
* Common networking issues
* Troubleshooting API server, scheduler

---

## ☁️ **Cloud Native Ecosystem**

* What is Cloud Native
* Monoliths vs Microservices
* Sidecars
* Envoy
* Istio
* Serverless

---

## 💰 **Operations & Governance**

* Cost management
* SLO/SLA/SLI
* SBOM (Software Bill of Materials)
* KubeLinter
* Artifact repository security
* Mutable vs Immutable infrastructure

---

</details>

<details>
  <summary>Kubernetes Study Roadmap</summary>
  
---

## **🧩 Stage 1: Foundations — Containers & Cloud-Native Basics**

**Goal:** Understand containers, orchestration, and Kubernetes fundamentals.

### 📘 Concepts

* What are containers
* Monoliths vs microservices
* Cloud Native & CNCF landscape
* Mutable vs immutable infrastructure
* Container orchestration

### 🛠️ Tools & Components

* Docker basics
* Docker vs Containerd
* Container Runtime Interface (CRI)
* Docker storage & networking
* Volume driver plugins in Docker

**Hands-on:**

* Build and run a Docker container
* Inspect images and layers (`docker history`, `docker inspect`)
* Configure Docker networking

---

## **🏗️ Stage 2: Core Kubernetes Architecture**

**Goal:** Learn how Kubernetes works internally.

### ⚙️ Control Plane

* API Server
* Controller Manager
* Scheduler
* etcd
* etcdctl
* Cluster configuration & initialization
* Cluster upgrade & HA setup

### ⚙️ Node Components

* Kubelet
* Kube Proxy

### 🔐 Core Configuration

* kubeconfig
* API groups
* Cluster security & management

**Hands-on:**

* Set up a cluster with `kubeadm`
* Inspect system pods in `kube-system`
* Access the API using `kubectl proxy`

---

## **🧱 Stage 3: Core Kubernetes Resources**

**Goal:** Deploy and manage workloads.

### 🧩 Workloads

* Pods (single/multi-container)
* Multi-container pod design patterns
* Init containers
* Static pods
* ReplicaSets
* Deployments
* DaemonSets
* StatefulSets
* Jobs & CronJobs

### ⚙️ Configuration Management

* Commands & arguments
* Environment variables
* ConfigMaps & Secrets

### 🧭 Scheduling

* Manual scheduling
* Labels & selectors
* Taints & tolerations
* Node selectors & affinity
* Scheduler profiles
* Multiple schedulers

**Hands-on:**

* Deploy workloads with YAML
* Schedule pods on specific nodes
* Configure init & sidecar containers

---

## **🌐 Stage 4: Networking, Services & Storage**

**Goal:** Learn how workloads communicate and persist data.

### 🌐 Networking

* Cluster networking
* Pod networking
* Container networking interface (CNI)
* Weave & IPAM Weave
* CoreDNS
* Network namespaces
* Network policies

### 🚦 Service Exposure

* Services (ClusterIP, NodePort, LoadBalancer, Headless)
* Ingress
* Gateway API

  * Resource model
  * Gateway resource configuration
  * Expose deployments via gateway

### 💾 Storage

* Volumes
* Persistent Volumes (PV)
* Persistent Volume Claims (PVC)
* Storage Classes
* Dynamic volume provisioning
* Container Storage Interface (CSI)

**Hands-on:**

* Create a PersistentVolume and PVC
* Configure and test a NetworkPolicy
* Expose an app via Ingress

---

## **⚙️ Stage 5: Advanced Operations & Scaling**

**Goal:** Operate clusters efficiently and manage workloads dynamically.

### 🔁 Application Lifecycle

* Rolling updates and rollbacks
* Blue-Green & Canary deployments
* Scaling applications

### 📈 Autoscaling

* Horizontal Pod Autoscaler (HPA)
* Vertical Pod Autoscaler (VPA)
* Event-driven autoscaling with KEDA

### ⚖️ Resource Management

* Resource requests, limits & quotas
* Quality of Service (QoS)
* API Priority & Fairness

### 🧠 Monitoring & Troubleshooting

* Monitor cluster components
* Manage logs
* Troubleshoot networking, API server, scheduler
* Application, control plane, worker node failures

**Hands-on:**

* Scale apps manually and via HPA
* Simulate pod failures and observe recovery
* Use metrics server and Prometheus

---

## **🔐 Stage 6: Kubernetes Security & Governance**

**Goal:** Secure the cluster, workloads, and supply chain.

### 🧰 Security Fundamentals

* Kubernetes security primitives
* Authentication & Authorization
* Role-Based Access Control (RBAC)
* Service Accounts
* ABAC
* API groups
* TLS in Kubernetes
* PKI certificates & API

### 🛡️ Workload Security

* Security contexts
* Pod security
* Admission controllers (Validating & Mutating)
* Network policies
* Secrets management
* Image security (Trivy, signing, minimal base image)
* Pod sandboxing (gVisor, Kata)
* Runtime classes
* Seccomp, AppArmor, SELinux
* mTLS, multi-tenancy, control plane isolation

### 🧠 Cluster Hardening

* Kubelet security
* Minimize host OS footprint
* UFW firewall, SSH hardening
* Restrict kernel modules & open ports
* Audit logging
* Kube-bench & CIS benchmarks
* Falco runtime threat detection
* OPA for policy enforcement

### 📦 Supply Chain & Compliance

* SBOM (Software Bill of Materials)
* Artifact repository security
* Compliance & threat modeling frameworks

**Hands-on:**

* Implement RBAC for restricted users
* Apply a network policy to isolate workloads
* Run `kube-bench` and Falco for auditing

---

## **🚀 Stage 7: Packaging, Automation & Ecosystem**

**Goal:** Learn to automate deployments and extend Kubernetes.

### 📦 Packaging & Templating

* Helm overview & installation
* Helm charts & components
* Customizing Helm chart parameters
* Lifecycle management with Helm
* Kustomize overview vs Helm
* Kustomize installation
* Overlays, components, patches, transformers

### 🧩 Extending Kubernetes

* Custom Resource Definitions (CRD)
* Custom controllers
* Operator framework
* Kubernetes Enhancement Proposals (KEP)
* Kubernetes Special Interest Groups (SIG)

### ⚙️ GitOps & Delivery

* GitOps fundamentals
* Push vs Pull-based deployments
* CI/CD with GitOps
* ArgoCD

### 📊 Observability & Cost

* Prometheus
* SLO/SLA/SLI
* Cost management

**Hands-on:**

* Package and deploy apps using Helm and Kustomize
* Implement a GitOps workflow using ArgoCD
* Extend Kubernetes with a simple custom controller

---

## 🌈 **Bonus: Cloud-Native and Service Mesh**

* Sidecars
* Envoy
* Istio
* Serverless on Kubernetes

**Hands-on:**

* Deploy a sample app with Istio sidecars
* Configure mTLS using Istio

---

</details>
