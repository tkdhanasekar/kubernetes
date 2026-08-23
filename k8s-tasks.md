## 50 Advanced Kubernetes Hands-On Tasks

These are designed as real troubleshooting/engineering labs, not just kubectl exercises. You can do most of them on kind, minikube, k3d, or a multi-node cluster.

## Cluster & Node Management

Build a multi-node cluster from scratch — Create a 3-control-plane/3-worker cluster and verify control-plane and etcd health.

Simulate node failure — Drain and shut down a worker node. Observe pod rescheduling and recover the node without data loss.

Configure node taints and tolerations — Create dedicated nodes for workloads such as databases, monitoring, and GPU-like workloads.

Implement node affinity — Force specific workloads onto particular node groups using required and preferred node affinity.

Practice topology spread constraints — Deploy a highly available application and distribute replicas across zones/nodes.

Perform a Kubernetes version upgrade — Upgrade a lab cluster one minor version at a time while maintaining application availability.

Recover a broken node — Deliberately corrupt kubelet configuration or stop kubelet/container runtime and troubleshoot it.

Configure resource reservations — Configure kubelet/system resource reservations and observe their effect under resource pressure.

Create and recover from disk pressure — Fill a node's disk and troubleshoot DiskPressure, eviction, and image garbage collection.

Investigate pod eviction behavior — Create memory/CPU pressure and determine which pods Kubernetes evicts and why.

## Networking

Build a NetworkPolicy zero-trust environment — Deny all traffic by default and explicitly allow only required application communication.

Debug DNS failures — Break CoreDNS configuration and troubleshoot service discovery using nslookup, dig, and temporary debugging pods.

Create a custom CNI lab — Install a CNI such as Cilium or Calico and investigate pod networking and network interfaces.

Implement ingress routing — Deploy multiple applications behind an Ingress controller using host-based and path-based routing.

Configure TLS for Ingress — Create Kubernetes TLS secrets and configure HTTPS with certificate rotation.

Debug a Service with no endpoints — Deliberately introduce selector/label mismatches and troubleshoot why traffic isn't reaching pods.

Compare ClusterIP, NodePort, and LoadBalancer — Deploy the same application using all three service types and inspect packet flow.

Implement headless services — Deploy a StatefulSet behind a headless Service and investigate DNS records for individual pods.

Test service-to-service latency — Measure latency between pods on the same node versus different nodes and investigate the network path.

Implement egress restrictions — Allow an application to communicate with only specific external destinations.

## Workloads & Scheduling

Perform a zero-downtime Deployment rollout — Configure readiness probes, rolling-update parameters, and PodDisruptionBudgets.

Create a failed rollout and recover it — Deploy a broken image/configuration, identify the failure, and perform a safe rollback.

Implement canary deployments manually — Run stable and canary versions simultaneously and control traffic using Services.

Implement blue-green deployment — Maintain two application versions and switch production traffic between them with minimal downtime.

Build a StatefulSet application — Deploy a stateful workload with stable pod identities and persistent volumes.

Perform StatefulSet rolling updates — Update the container image and investigate ordered pod replacement.

Build a CronJob with failure handling — Configure retries, deadlines, concurrency policies, and history limits.

Investigate CrashLoopBackOff — Create several intentionally broken pods and diagnose each using events, logs, probes, and container state.

Investigate ImagePullBackOff — Reproduce authentication, registry, tag, and network-related image pull failures.

Tune QoS classes — Create Guaranteed, Burstable, and BestEffort pods and observe their behavior during resource pressure.

## Storage

Build dynamic persistent storage — Configure a StorageClass and dynamically provision PVCs for an application.

Test PVC expansion — Expand a PVC without recreating the application and verify filesystem expansion.

Simulate a failed volume mount — Diagnose problems involving PV/PVC binding, mount paths, permissions, and storage classes.

Implement StatefulSet persistent storage — Give every replica its own PVC and verify that data survives pod recreation.

Test backup and restore — Back up Kubernetes resources and persistent application data, delete the workload, and restore it.

Investigate volume permissions — Run a non-root container against a persistent volume and solve permission problems using securityContext/fsGroup.

Create a storage failure scenario — Make a volume unavailable and investigate how Kubernetes handles the affected pod.

## Security

Implement Pod Security Standards — Enforce restricted security settings and modify workloads until they comply.

Run containers as non-root — Harden an application using runAsNonRoot, dropped capabilities, read-only filesystems, and seccomp.

Implement RBAC least privilege — Create a ServiceAccount that can only read Pods and Services in one namespace.

Perform RBAC troubleshooting — Intentionally create permission failures and use kubectl auth can-i to diagnose them.

Secure Secrets properly — Deploy an application using Secrets, investigate how they are exposed, and implement safer access patterns.

Create an admission-control policy — Use Kyverno or Gatekeeper to prevent workloads from running privileged containers.

Audit Kubernetes API activity — Configure/inspect audit logging and identify who performed specific API operations.

Compromise-and-harden lab — Deploy an intentionally overprivileged workload, identify its security weaknesses, then harden it.

## Observability & Troubleshooting

Build a Prometheus/Grafana monitoring stack — Monitor nodes, pods, CPU, memory, filesystem usage, and application metrics.

Create Kubernetes alerts — Build alerts for pod crashes, high CPU, high memory, unavailable replicas, and node failures.

Debug a production-style outage — Create a scenario involving bad DNS, failing pods, incorrect Services, and resource exhaustion. Diagnose it without looking at the configuration first.

Implement distributed tracing — Deploy several microservices and use OpenTelemetry plus a tracing backend to follow a request across services.

Complete a full production incident simulation — Combine Ingress + DNS + NetworkPolicy + RBAC + Secrets + StatefulSets + PVCs + resource limits + monitoring. Introduce 5–10 failures simultaneously and troubleshoot the cluster systematically.

Suggested progression

For maximum learning, do them roughly in this order:

1–10: Cluster administration
11–20: Networking
21–30: Workloads and scheduling
31–37: Storage
38–45: Security
46–50: Observability and production troubleshooting

For each lab, give yourself a constraint: don't look at the manifests first. Start with symptoms (kubectl get, describe, logs, events, metrics, DNS/network tests), identify the root cause, fix it, and then document the solution. That turns these into genuine CKA/CKS + production SRE-style practice rather than memorizing commands.
