<details>
  <summary>KCSA</summary>

# KCSA

## Overview of Cloud Native Security

* The 4Cs of Cloud Native Security
* Cloud Provider Security
* Infrastructure Security
* Kubernetes Isolation Techniques
* Artifact Repository Security
* Image Security
* Workload and Application Code Security

## Kubernetes Cluster Component Security

* API Server
* Controller Manager
* Scheduler
* Kubelet Security
* Container Runtime Interface(CRI)
* Kube Proxy
* etcd
* Container Networking
* Kubectl Proxy and Port Forward
* Kubeconfig
* Storage

## Kubernetes Security Fundamentals

* Pod Security
* Authentication
* Authorization
* Role Based Access Controls (RBAC)
* Secrets
* Namespaces
* Resource Quotas and Limits
* Security Contexts
* Audit Logging
* Network Policies

## Kubernetes Threat Model

* Trust Boundaries and Data Flow
* Persistence
* Denial of Service (DoS)
* Malicious Code Execution
* Compromised Applications in Containers
* Attacker on the Network
* Access to Sensitive Data
* Privilege Escalation

## Platform Security

* Minimize base image footprint
* Scan images for known vulnerabilities(Trivy)
* Falco
* Istio
* PKI Certificates and API
* TLS in Kubernetes
* mTLS
* Admission Controllers

## Compliance and Security Frameworks

* Compliance Frameworks
* Threat Modelling Frameworks
* Supply Chain Compliance
* Automation and Tooling
</details>
<details>
  <summary>KCNA</summary>
  
# KCNA

## Kubernetes Fundamentals

* what is Cloud Native?
* what are Containers?
* Container Orchestration
* Kubernetes Architecture
* Container Runtime Interface (CRI)
* Docker vs ContainerD

## Kubernetes Resources

* Pods
* ReplicaSets
* Deployments
* Rolling Updates and Rollbacks
* Imperative vs Declarative
* Kubectl Apply Command
* Namespaces

## Scheduling

* Manual Scheduling
* Labels and Selectors
* Taints and Tolerations
* Node Selectors
* Node Affinity
* Taints and Tolerations vs Node Affinity
* Resource Quotas and Limits
* DaemonSets
* Static Pods
* Multiple Schedulers
* Configuring Kubernetes Scheduler Profiles

## Security

* Kubernetes Security Primitives
* Authentication
* KubeConfig
* API Groups
* Authorization
* Role Based Access Controls (RBAC)
* Cluster Roles
* Sercice Accounts
* Image Security
* Security Contexts
* Network Policies

## Networking

* Cluster Networking
* Pod Networking
* Container Networking Interface (CNI)
* Weave
* DNS
* Ingress

## Service Mesh

* Services
* Sidecars
* Envoy
* Monoliths and Microservices
* Istio

## Storage

* Storage
* Docker Storage
* Volume Driver Plugins in Docker
* Container Storage Interface (CSI)
* Volumes
* Persistent Volumes (PV)
* Persistent Volume Claims (PVC)
* Storage Classes

## Cloud Native Architecture

* Autoscaling
* Horizontal Pod Autoscaler(HPA)
* Vertical Pod Autoscaler(VPA)
* Serverless
* Kubernetes Enhancement Proposal(KEP)
* Kubernetes Special Interest Groups (SIG)
* Open Standards

## Cloud Native Observability

* Falco
* SLO/SLA/SLI
* Prometheus
* Cost Management

## Cloud Native Application Delivery

* Application Delivery Fundamentals
* GitOps
* Push vs Pull-based Deployments
* CI/CD with GitOps
* ArgoCD
</details>
<details>
  <summary>CKA</summary>

# CKA

## core concepts

* K8S architecture
* Docker vs ContainerD
* etcd
* API Server
* Controller Manager
* Scheduler
* Kubelet
* Kube Proxy
* K8S Extension Interfaces
* Container Runtime Interface (CRI)
* Container Storage Interface (CSI)
* Pods
* Pods with YAML
* ReplicaSets
* Deployments
* Services
* ClusterIP
* Load Balancer
* Namespaces
* Imperative vs Declarative
* Kubectl Apply Command

## Scheduling

* Manual Scheduling
* Labels and Selectors
* Taints and Tolerations
* Node Selectors
* Node Affinity
* Taints and Tolerations vs Node Affinity
* Resource Quotas and Limits
* DaemonSets
* Static Pods
* Multiple Schedulers
* Configuring K8S Scheduler Profiles

## Logging and Monitoring

* Monitor Cluster Components
* Managing Application/Container Logs

## Application Lifecycle Management

* Rolling Updates and Rollbacks
* Commands and Arguments
* Environment Variables
* ConfigMaps
* Secrets
* Scale Applications
* Autoscaling
* Horizontal Pod Autoscaler (HPA)
* Vertical Pod Autoscaler (VPA)
* Cluster Autoscaler
* Event-Driven Autoscaling with KEDA
* Readiness Probes
* Liveness Probes
* Multi Container PODs
* Multi Container PODs Design Patterns
* Init Containers

## Cluster Maintenance

* OS Upgrades
* Kubernetes Software Versions
* Cluster Upgrade
* Backup and Restore Methods
* ETCDCTL

## Security

* K8S Security Primitives
* Authentication
* TLS Basics
* TLS in K8S
* PKI Certificates and API
* KubeConfig
* API Groups
* Authorization
* Role Based Access Controls (RBAC)
* Cluster Roles
* Service Accounts
* Image Security
* Security in Docker
* Security Contexts
* Network Policies
* Admission Controllers
* Validation and Mutating Admission Controllers
* Kubectx and Kubens

## Storage

* Volume Driver Plugins in Docker
* Docker Storage
* Volumes
* Persistent Volumes (PV)
* Persistent Volume Claims (PVC)
* Using PVC in Pods
* Storage Classes
* Dynamic Volume Provisioning

## Networking

* Switching, Routing, Gateways CNI Kunbernetes
* CoreDNS
* Network Namespaces
* Container Networking Interface (CNI)
* Docker Networing
* Cluster Networking
* Pod Networking
* Weave
* IPAM Weave
* Ingress
* The Need for Gateway API
* Introduction to Gateway API and Resource Model
* Congfigure a Gateway Resource
* Expose a deployment on the Gateway
* Traffic Switching

## Install

* Infrastructure Setup
* Cluster Configuration and Initialization
* Cluster Security and Management
* Core Services and Tools
* Testing and Validation
* Advanced Setup-Deploy with Kubeadm
* ETCD in HA
* Helm Overview
* Helm Installtion
* Helm Concepts
* Kustomize Overview
* Kustomize vs Helm
* Kustomize Installation
* Kustomize.yaml file
* Kustomize Output
* Kustomize ApiVersion and Kind
* Managing Directories with Kustomize
* Common Kustomize Transformers
* Kustomize Patches
* Kustomize Different Types of Patches
* Kustomize Patches List
* Kustomize Patches Dictionary
* Kustomize Overlays
* Kustomize Components
* Custom Resource Definitions (CRD)
* Operator Framework

## Troubelshooting

* Application Failure
* Control Plane Failure
* Worker Node Failure
* Troubleshoot Services and Networking
* Common Networking Issues
* Troubleshooting the API Server, Scheduler
* Networking Troubleshooting
</details>
<details>
  <summary>CKAD</summary>
  
# CKAD

## Core Concepts

* Docker vs ContainerD
* Pods
* ReplicaSets
* Deployments
* Namespaces
* Imperative Commands

## Configuration

* Docker Images
* Commands and Arguments
* Environment Variables
* ConfigMaps
* Secrets
* Security Contexts
* Resource Quotas and Limits
* Service Accounts
* Taints and Tolerations
* Node Selectors
* Node Affinity

## Multi-Container Pods

* Multicontainer PODs
* init Containers

## Observability

* Readiness Probes
* Liveness Probes
* Managing Application/Container Logs
* Monitor Cluster Components

## Pod Design

* Labels and Selectors
* Rolling Updates and Rollbacks
* Deployment Strategy-Blue Green
* Deployment Strategy-Canary
* Kustomize Overview
* Kustomize vs Helm
* Kustomize Installation
* Kustomize Overlays
* Kustomize Components
* Jobs
* Cron Jobs

## Services and Networking

* Services
* Network Policies
* Ingress

## State Persistence

* Volume Driver Plugins in Docker
* Persistent Volumes (PV)
* Persistent Volume Claims (PVC)s
* Storage Classes
* Stateful Sets
* Headless Services

## Security

* Authentication
* Kubeconfig
* API Groups
* Authorization
* Role Based Access Controls (RBAC)
* Cluster Roles
* Admission Controllers
* API Versions/Deprecations
* Custom Resource Definition (CRD)
* Custom Controllers
* Operator Framework

## Helm

* Helm Overview
* Install Helm
* Helm Charts
* Helm Components
* Customizing Helm Chart Params
* Lifecycle Management with Helm
</details>
<details>
  <summary>CKS</summary>
  
# CKS

## Understanding the Kubernets Attack Surface

* The 4Cs if Cloud Native Security

## Cluster Setup and Hardening

* CIS benchmarks
* Kube-bench
* Kubernetes Security Primitives
* Authentication
* Service Accounts
* TLS in Kubernetes
* PKI Certificates and API
* Kubeconfig
* API Groups
* Authoriztion
* Role Based Access Controls (RBAC)
* Cluster Roles
* Attribute Based Access Control (ABAC)
* Kubelet Security
* Kubectl Proxy and Port Forward
* Kubernetes Dashboard
* Verify platform binaries before deploying
* Update Kubernetes frequently
* Kubernetes Software Versions
* Cluster Upgrade
* Network Policies
* Ingress
* Docker Service Configuration
* Docker-Securing the Daemon
* Securing Node Metadata
* Protection Strategies
* Endpoint Security
* Audit Logging

## System Hardening

* Least Privilege Principle
* Minimize Host OS footprint
* Limit Node Access
* SSH Hardening
* Privilege Escalation
* Remove Obsolete Packages and Services
* Restrict Kernel Modules
* Identify and Disable Open Ports
* Minimize IAM roles
* Minimize external access to the network
* UFW Firewall Basics
* Linux Syscalls
* AquaSec Tracee
* Restrict syscalls using seccomp
* Implement Seccomp in Kubernetes
* AppArmor
* Linux Capabilities
* SELinux Basics

## Minimize Microservice Vulnerabilities

* Security Contexts
* Admission Controllers
* Pod Security
* Open Policy Agent (OPA)
* Secrets
* Container Sandboxing
* gVisor
* kata Containers
* Runtime Classes
* Container Runtime Interface (CRI)
* mTLS
* Multi-Tenancy
* Control Plane Isolation
* Namespaces
* Resource Quotas and Limits
* Storage
* Taints and Tolerations
* API Priority and Fairness
* Quality of Service
* Cilium

## Supply Chain Security

* SBOM
* KubeLinter
* Minimize base image footprint
* Image Security
* ImagePolicyWebhook
* Sign and Validate Images
* Use static analysis of user workloads
* Scan images for known vulerabilities (Trivy)
* Artifact Repository Security

## Monitoring, Logging and Runtime Security

* Perform behavioral analytics of syscall process
* falco
* Detect threats across infrastructure, apps, networks, data, users, and workloads
* Detect all attack phases, regardless of location or spread
* Conduct deep analysis to identify bad actors in the environment
* Mutable vs Immutable Infrastructure
</details>
