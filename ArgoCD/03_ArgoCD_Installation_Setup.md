**✅ Improved ArgoCD Installation & Setup Guide – Professionally Organized for Enterprise Use**

---

### 🧭 **Table of Contents**

1. 🚀 [Installation Methods](#installation-methods)
2. 🔍 [Component Verification](#component-verification)
3. 🌐 [Accessing ArgoCD UI](#accessing-argocd-ui)
4. 🔐 [Authentication & SSO](#authentication--sso)
5. 🖥️ [CLI Installation & Usage](#cli-installation--usage)
6. ⚙️ [Configuration Best Practices](#configuration-best-practices)
7. 🛠️ [Troubleshooting Guide](#troubleshooting-guide)
8. 💻 [Free Browser-Based Practice](#free-browser-based-practice)
9. 📚 [Official Resources](#official-resources)

---

## 🚀 Installation Methods

> **Choose one method depending on environment: Manifest (simplest), Helm (configurable), Operator (CRD-based automation)**

### 🔹 Method 1: Manifest (Quick Start)

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 🔹 Method 2: Helm

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
helm install argocd argo/argo-cd -n argocd --create-namespace
```

### 🔹 Method 3: Operator

```bash
kubectl apply -f https://raw.githubusercontent.com/argoproj-labs/argocd-operator/master/deploy/crds/
kubectl apply -f https://raw.githubusercontent.com/argoproj-labs/argocd-operator/master/deploy/
```

---

## 🔍 Component Verification

### ✅ Pods Check

```bash
kubectl get pods -n argocd -w
```

* `argocd-server`, `argocd-repo-server`, `argocd-application-controller`, `argocd-redis`, `argocd-dex-server`, `argocd-notifications-controller`

### ✅ Services Check

```bash
kubectl get svc -n argocd
```

---

## 🌐 Accessing ArgoCD UI

### 🔹 Method 1: Port Forwarding (Dev)

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Access via: https://localhost:8080
```

### 🔹 Method 2: LoadBalancer (Cloud)

```bash
kubectl patch svc argocd-server -n argocd -p '{"spec":{"type":"LoadBalancer"}}'
```

### 🔹 Method 3: Ingress (Prod)

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  annotations:
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  rules:
  - host: argocd.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: argocd-server
            port:
              number: 443
```

---

## 🔐 Authentication & SSO

### 🗝️ Get Admin Password

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

### 👤 Default Login

* **Username**: `admin`
* **Password**: Retrieved from secret above

### 🔄 SSO Integration (GitHub Example)

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
data:
  url: https://argocd.example.com
  dex.config: |
    connectors:
    - type: github
      id: github
      name: GitHub
      config:
        clientID: <your-id>
        clientSecret: <your-secret>
```

---

## 🖥️ CLI Installation & Usage

### 📦 Install CLI

```bash
# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
```

### 🔐 Login via CLI

```bash
argocd login <hostname>  # e.g. localhost:8080 or argocd.example.com
```

### 📋 Common Commands

```bash
argocd version
argocd app list
argocd app get <app-name>
argocd app sync <app-name>
```

---

## ⚙️ Configuration Best Practices

### ✅ RBAC

```yaml
data:
  rbac.defaultPolicy: role:readonly
  rbac.policy: |
    g, admins, role:admin
    g, devs, role:developer
```

### ✅ Resource Limits

```yaml
resources:
  requests:
    memory: "128Mi"
    cpu: "100m"
  limits:
    memory: "256Mi"
    cpu: "200m"
```

### ✅ HA Setup

```yaml
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
```

---

## 🛠️ Troubleshooting Guide

| Issue               | Command                                                                               |
| ------------------- | ------------------------------------------------------------------------------------- |
| ❌ Pods not starting | `kubectl get pods -n argocd` <br> `kubectl logs -n argocd <pod>`                      |
| ❌ UI inaccessible   | `kubectl describe svc argocd-server -n argocd` <br> `kubectl get endpoints -n argocd` |
| ❌ Login failed      | Check secret: `kubectl get secret argocd-initial-admin-secret -n argocd -o yaml`      |
| ❌ Network issues    | Port forward: `kubectl port-forward svc/argocd-server -n argocd 8080:443`             |

---

## 💻 Free Browser-Based Practice

| Platform   | Setup   | Duration  | ArgoCD Ready | URL                                                                    |
| ---------- | ------- | --------- | ------------ | ---------------------------------------------------------------------- |
| Katacoda   | Instant | 15 min    | ✅ Yes        | [katacoda.com](https://www.katacoda.com/courses/kubernetes/playground) |
| PWK        | 5 min   | 4 hrs     | ❌ No         | [labs.play-with-k8s.com](https://labs.play-with-k8s.com)               |
| Killercoda | 2 min   | 1 hr      | ✅ Yes        | [killercoda.com](https://killercoda.com)                               |
| GCP Shell  | 10 min  | Unlimited | ❌ No         | [shell.cloud.google.com](https://shell.cloud.google.com)               |
| Minikube   | 15 min  | Local     | ❌ No         | —                                                                      |

---

## 📚 Official Resources

* [🔗 ArgoCD Docs](https://argo-cd.readthedocs.io/en/stable/)
* [📘 Operator Manual](https://argo-cd.readthedocs.io/en/stable/operator-manual/)
* [📖 CLI Guide](https://argo-cd.readthedocs.io/en/stable/user-guide/commands/)

---

✅ **Final Summary**

* Use **Manifest** for quick start, **Helm** for flexibility, **Operator** for CRD-based automation.
* Access UI via port-forward, LoadBalancer, or Ingress.
* Install CLI for scriptable control.
* Secure setup with RBAC, SSO, and resource limits.
* Practice for free via Katacoda, Killercoda, or Minikube.

Let me know if you want this exported as **Confluence markdown**, **PDF**, or **code snippet-based cheat sheet**.
