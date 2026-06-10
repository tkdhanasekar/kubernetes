There is no fixed, universal list of Kubernetes objects because new objects can be added through **Custom Resource Definitions (CRDs)** and extensions. However, for a standard Kubernetes cluster, the objects can be grouped as follows.

# Core Kubernetes Objects

## Workload Objects

These run and manage applications.

| Object                | Purpose                              |
| --------------------- | ------------------------------------ |
| Pod                   | Smallest deployable unit             |
| ReplicaSet            | Maintains a specified number of Pods |
| Deployment            | Manages stateless applications       |
| StatefulSet           | Manages stateful applications        |
| DaemonSet             | Runs one Pod per node                |
| Job                   | Runs a task to completion            |
| CronJob               | Runs Jobs on a schedule              |
| ReplicationController | Legacy replica manager               |

---

## Service Discovery & Networking Objects

| Object        | Purpose                            |
| ------------- | ---------------------------------- |
| Service       | Stable network endpoint            |
| Endpoints     | List of backend Pod IPs            |
| EndpointSlice | Scalable replacement for Endpoints |
| Ingress       | HTTP/HTTPS routing                 |
| IngressClass  | Defines Ingress controller         |
| NetworkPolicy | Pod traffic control                |

---

## Configuration & Storage Objects

| Object                      | Purpose                      |
| --------------------------- | ---------------------------- |
| ConfigMap                   | Non-sensitive configuration  |
| Secret                      | Sensitive configuration      |
| PersistentVolume (PV)       | Cluster storage resource     |
| PersistentVolumeClaim (PVC) | Storage request              |
| StorageClass                | Dynamic storage provisioning |
| VolumeAttachment            | Tracks volume attachment     |
| CSIDriver                   | CSI driver information       |
| CSINode                     | CSI information per node     |
| CSIStorageCapacity          | CSI storage capacity         |

---

## Cluster Objects

| Object        | Purpose                       |
| ------------- | ----------------------------- |
| Namespace     | Logical isolation             |
| Node          | Worker machine                |
| ResourceQuota | Resource limits per namespace |
| LimitRange    | Default resource constraints  |

---

## RBAC (Security) Objects

| Object             | Purpose                  |
| ------------------ | ------------------------ |
| ServiceAccount     | Pod identity             |
| Role               | Namespace permissions    |
| ClusterRole        | Cluster-wide permissions |
| RoleBinding        | Assign Role              |
| ClusterRoleBinding | Assign ClusterRole       |

---

## Scheduling Objects

| Object        | Purpose                     |
| ------------- | --------------------------- |
| PriorityClass | Pod scheduling priority     |
| RuntimeClass  | Container runtime selection |

---

## Autoscaling Objects

| Object                        | Purpose                  |
| ----------------------------- | ------------------------ |
| HorizontalPodAutoscaler (HPA) | Scale Pods automatically |

---

## Policy Objects

| Object                    | Purpose                        |
| ------------------------- | ------------------------------ |
| PodDisruptionBudget (PDB) | Controls voluntary disruptions |

---

## Admission & API Extension Objects

| Object                         | Purpose               |
| ------------------------------ | --------------------- |
| CustomResourceDefinition (CRD) | Create custom APIs    |
| APIService                     | Extend Kubernetes API |
| ValidatingWebhookConfiguration | Request validation    |
| MutatingWebhookConfiguration   | Request mutation      |

---

## Certificate Objects

| Object                          | Purpose              |
| ------------------------------- | -------------------- |
| CertificateSigningRequest (CSR) | Request certificates |

---

## Event & Coordination Objects

| Object | Purpose                             |
| ------ | ----------------------------------- |
| Event  | Records cluster events              |
| Lease  | Leader election and node heartbeats |

---

## Authentication / Authorization Objects

| Object                   | Purpose                       |
| ------------------------ | ----------------------------- |
| TokenReview              | Token validation              |
| SubjectAccessReview      | Authorization check           |
| LocalSubjectAccessReview | Namespace authorization check |
| SelfSubjectAccessReview  | Self authorization            |
| SelfSubjectRulesReview   | User permissions              |
| SelfSubjectReview        | User identity information     |

---

## Flow Control Objects

| Object                     | Purpose                    |
| -------------------------- | -------------------------- |
| FlowSchema                 | API request classification |
| PriorityLevelConfiguration | API request prioritization |

---

## Legacy Networking Objects

| Object    | Purpose                  |
| --------- | ------------------------ |
| Endpoints | Legacy endpoint resource |

---

# Commonly Used Objects in Daily Operations

In real-world Kubernetes work, you'll use these most frequently:

1. Pod
2. Deployment
3. ReplicaSet
4. StatefulSet
5. DaemonSet
6. Job
7. CronJob
8. Service
9. Ingress
10. ConfigMap
11. Secret
12. Namespace
13. PersistentVolume
14. PersistentVolumeClaim
15. StorageClass
16. ServiceAccount
17. Role
18. RoleBinding
19. ClusterRole
20. ClusterRoleBinding
21. NetworkPolicy
22. HorizontalPodAutoscaler
23. PodDisruptionBudget
24. Node

---

# How to List All Objects in a Cluster

To see all API resources supported by your cluster:

```bash
kubectl api-resources
```

To see all objects in a namespace:

```bash
kubectl get all -n <namespace>
```

To see absolutely every resource type and instance:

```bash
kubectl api-resources --verbs=list --namespaced -o name | \
xargs -n 1 kubectl get --show-kind --ignore-not-found -n default
```

For Kubernetes v1.30+ (and similar recent versions), there are roughly **50–70 built-in resource types**, and production clusters often have **hundreds more** due to CRDs installed by tools like Istio, Argo CD, Prometheus Operator, and cert-manager.

