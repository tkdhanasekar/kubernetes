Setting up **Hetzner Managed Kubernetes (HKS)** is much simpler than building your own cluster with kubeadm or Terraform.

This is the **official managed service**, so Hetzner handles:

* Control plane (API server, etcd, scheduler)
* High availability
* Upgrades
* Basic cluster health

You only manage:

* Worker nodes
* Your apps
* Networking / ingress / storage addons

---

# 🚀 Step-by-step: Set up Hetzner Managed Kubernetes

## 1. Create Hetzner Cloud Project

Go to:

* [https://console.hetzner.cloud](https://console.hetzner.cloud)

Create a **Project** (e.g. `prod-k8s`)

---

## 2. Create API Token

Inside your project:

**Security → API Tokens → Generate Token**

Choose:

* ✔ Read & Write permissions

Save it safely:

```bash
export HCLOUD_TOKEN="your-token"
```

---

## 3. Create Kubernetes Cluster (Console)

Go to:

👉 **Kubernetes → Create Cluster**

You will configure:

### Basic settings

* Name: `my-cluster`
* Location: `fsn1` (Germany) or nearest region

---

### Node pools

You define worker nodes:

Example:

* 2–3 nodes minimum for production
* Type: `cx21` or `cx31`

| Setting      | Example     |
| ------------ | ----------- |
| Node type    | cx21        |
| Count        | 2–3         |
| Auto scaling | optional    |
| Location     | same region |

---

## 4. Create Cluster

Click:
👉 **Create & Buy**

Hetzner will automatically:

* Provision nodes
* Install Kubernetes
* Configure networking
* Attach Cloud Controller Manager
* Enable LoadBalancer support

Wait ~5–10 minutes.

---

## 5. Download kubeconfig

After cluster is ready:

Go to:
👉 Kubernetes → your cluster → **Download Kubeconfig**

Then set it locally:

```bash
export KUBECONFIG=~/Downloads/kubeconfig.yaml
kubectl get nodes
```

You should see worker nodes ready.

---

## 6. Test cluster

```bash
kubectl get pods -A
kubectl get svc -A
```

---

# 🌐 7. Enable LoadBalancers (IMPORTANT for apps like nginx / gateway)

Hetzner Managed Kubernetes already supports:

```yaml
type: LoadBalancer
```

Example:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: demo
spec:
  type: LoadBalancer
  selector:
    app: demo
  ports:
  - port: 80
    targetPort: 80
```

Hetzner automatically creates a **public IP Load Balancer**.

---

# 📦 8. Install essential addons (recommended)

## Install kubectl tools

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.0/deploy/static/provider/cloud/deploy.yaml
```

---

## Install Hetzner CSI (storage volumes)

```bash
kubectl apply -f https://github.com/hetznercloud/csi-driver/releases/latest/download/deploy.yaml
```

---

## Install cert-manager (TLS)

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/latest/download/cert-manager.yaml
```

---

# 🌍 9. DNS setup (for your app)

If you later deploy:

`orange.hashlabs.in`

You will:

1. Create LoadBalancer service
2. Get external IP:

```bash
kubectl get svc
```

3. Point DNS:

```
orange.hashlabs.in → <LOAD_BALANCER_IP>
```

---

# ⚙️ 10. What Hetzner manages vs you manage

| Component      | Managed by Hetzner |
| -------------- | ------------------ |
| Control plane  | ✔ Yes              |
| etcd           | ✔ Yes              |
| Upgrades       | ✔ Yes              |
| Worker nodes   | ❌ You              |
| Apps           | ❌ You              |
| Ingress        | ❌ You              |
| Storage claims | ⚠ via CSI          |

---

# 🔥 When to use Managed Kubernetes (vs self-managed)

### Use Managed K8s if:

* You want fast production setup
* You don’t want control plane maintenance
* You’re running apps like:

  * NGINX Gateway
  * microservices
  * SaaS workloads

### Avoid if:

* You need deep networking customization
* You want full control over kubeadm / OS
* You’re building platform-level infra

---

# 🚀 Next step (based on your previous question)

Since you’re working with:
👉 `orange.hashlabs.in`
👉 NGINX Gateway Fabric

I can help you next with:

* 🔥 Install NGINX Gateway Fabric on Hetzner Managed K8s
* 🌐 Route domain → service properly
* 🔐 HTTPS with Let’s Encrypt (fully automated)
* 🚀 production-grade ingress setup
* 📦 multi-app architecture (API + frontend + gateway)

Just tell me 👍

