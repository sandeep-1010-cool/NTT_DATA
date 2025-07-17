# ArgoCD Installation & Setup Guide

## Table of Contents
1. [Installation Methods](#installation-methods)
2. [Component Verification](#component-verification)
3. [Accessing ArgoCD UI](#accessing-argocd-ui)
4. [Authentication Setup](#authentication-setup)
5. [CLI Installation](#cli-installation)
6. [Troubleshooting](#troubleshooting)

---

## Installation Methods

### Method 1: Plain Kubernetes Manifest Installation
```bash
# Create ArgoCD namespace
kubectl create namespace argocd

# Install ArgoCD using official manifest
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

**📊 Installation Components Created:**
- Custom Resource Definitions (CRDs)
- Service Accounts, Roles, Role Bindings
- ConfigMaps, Secrets
- Deployments, Services
- Network Policies



### Method 2: Helm Chart Installation
```bash
# Add ArgoCD Helm repository
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update

# Install ArgoCD using Helm
helm install argocd argo/argo-cd -n argocd --create-namespace
```

### Method 3: Operator Installation
```bash
# Install ArgoCD Operator
kubectl apply -f https://raw.githubusercontent.com/argoproj-labs/argocd-operator/master/deploy/crds/
kubectl apply -f https://raw.githubusercontent.com/argoproj-labs/argocd-operator/master/deploy/
```



---

## Component Verification

### Check Pod Status:
```bash
# Watch pods coming up
kubectl get pods -n argocd -w

# Expected components:
# - argocd-server (API Server)
# - argocd-repo-server (Git interface)
# - argocd-application-controller (Reconciliation engine)
# - argocd-redis (Caching)
# - argocd-dex-server (Authentication)
# - argocd-notifications-controller (Notifications)
```



### Verify Services:
```bash
# Check services
kubectl get svc -n argocd

# Expected services:
# - argocd-server (UI/CLI access)
# - argocd-repo-server (Git operations)
# - argocd-redis (Cache)
# - argocd-dex-server (Authentication)
```

---

## Accessing ArgoCD UI

### Method 1: Port Forwarding (Local Development)
```bash
# Change service type to NodePort
kubectl patch svc argocd-server -n argocd -p '{"spec":{"type":"NodePort"}}'

# For Minikube - Create tunnel
minikube service argocd-server -n argocd

# For other clusters - Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### Method 2: Load Balancer (Cloud)
```bash
# Change service type to LoadBalancer
kubectl patch svc argocd-server -n argocd -p '{"spec":{"type":"LoadBalancer"}}'
```

### Method 3: Ingress (Production)
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-server-ingress
  namespace: argocd
  annotations:
    nginx.ingress.kubernetes.io/ssl-passthrough: "true"
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
spec:
  ingressClassName: nginx
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

## Authentication Setup

### Get Admin Password:
```bash
# Method 1: Using kubectl
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Method 2: Using describe
kubectl -n argocd describe secret argocd-initial-admin-secret
```

**🔐 Login Credentials:**
- **Username**: `admin`
- **Password**: Generated from secret (see above)



### Configure SSO (Optional):
```yaml
# Configure Dex for SSO
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  url: https://argocd.example.com
  dex.config: |
    connectors:
    - type: github
      id: github
      name: GitHub
      config:
        clientID: your-github-client-id
        clientSecret: your-github-client-secret
```



---

## 🆓 Free Browser-Based Practice Options

### Option 1: Katacoda (Recommended)
**🌐 URL**: https://www.katacoda.com/courses/kubernetes/playground
**✅ Benefits**:
- Free Kubernetes cluster in browser
- Pre-installed ArgoCD
- No setup required
- Interactive tutorials
- 15-minute sessions

**🚀 Quick Start**:
```bash
# Katacoda provides a ready-to-use cluster
# ArgoCD is often pre-installed
kubectl get pods -n argocd
```

### Option 2: Play with Kubernetes (PWK)
**🌐 URL**: https://labs.play-with-k8s.com/
**✅ Benefits**:
- Free 4-hour sessions
- Full Kubernetes cluster
- Can install ArgoCD manually
- Multiple nodes available

**🚀 Quick Start**:
```bash
# Install ArgoCD in PWK
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Option 3: Killercoda
**🌐 URL**: https://killercoda.com/
**✅ Benefits**:
- Free browser-based environments
- Pre-configured scenarios
- ArgoCD tutorials available
- No registration required

### Option 4: Google Cloud Shell
**🌐 URL**: https://shell.cloud.google.com/
**✅ Benefits**:
- Free tier available
- Integrated with GKE
- Can install ArgoCD
- Persistent storage

**🚀 Quick Start**:
```bash
# Enable GKE API and create cluster
gcloud container clusters create argocd-demo --zone us-central1-a
gcloud container clusters get-credentials argocd-demo --zone us-central1-a

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Option 5: Local Browser with Minikube
**🖥️ Local Setup**:
```bash
# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start cluster
minikube start

# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access UI
minikube service argocd-server -n argocd
```

### 📊 Comparison Table

| Platform | Setup Time | Session Duration | ArgoCD Pre-installed | Best For |
|----------|------------|------------------|---------------------|----------|
| **Katacoda** | 0 min | 15 min | ✅ Yes | Quick demos |
| **PWK** | 5 min | 4 hours | ❌ No | Extended practice |
| **Killercoda** | 2 min | 1 hour | ✅ Yes | Tutorials |
| **Cloud Shell** | 10 min | Unlimited | ❌ No | Production-like |
| **Minikube** | 15 min | Unlimited | ❌ No | Local development |

---

## CLI Installation

### Install ArgoCD CLI:
```bash
# macOS
brew install argocd

# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

# Windows
# Download from GitHub releases
```

### Login to ArgoCD:
```bash
# Login using CLI
argocd login <argocd-server-url>

# Example for local setup
argocd login localhost:8080

# Example for cloud setup
argocd login argocd.example.com
```



### Essential CLI Commands:
```bash
# Check version
argocd version

# List applications
argocd app list

# Get application details
argocd app get <app-name>

# Sync application
argocd app sync <app-name>
```

---

## Troubleshooting

### Issue 1: Pods Not Starting
```bash
# Check pod status
kubectl get pods -n argocd

# Check pod logs
kubectl logs -n argocd <pod-name>

# Check events
kubectl get events -n argocd --sort-by='.lastTimestamp'
```

### Issue 2: Service Not Accessible
```bash
# Check service status
kubectl get svc -n argocd

# Check endpoints
kubectl get endpoints -n argocd

# Check service details
kubectl describe svc argocd-server -n argocd
```

### Issue 3: Authentication Issues
```bash
# Reset admin password
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData":{"admin.password":"new-password"}}'

# Check secret
kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
```

### Issue 4: Network Connectivity
```bash
# Test connectivity
kubectl port-forward svc/argocd-server -n argocd 8080:443

# Check firewall rules
# Ensure ports 443/8080 are accessible
```



---

## Configuration Best Practices

### 1. Security Configuration
```yaml
# Enable RBAC
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  rbac.defaultPolicy: role:readonly
  rbac.policy: |
    g, admin, role:admin
    g, developer, role:developer
```

### 2. Resource Limits
```yaml
# Set resource limits
apiVersion: apps/v1
kind: Deployment
metadata:
  name: argocd-server
  namespace: argocd
spec:
  template:
    spec:
      containers:
      - name: argocd-server
        resources:
          requests:
            memory: "128Mi"
            cpu: "100m"
          limits:
            memory: "256Mi"
            cpu: "200m"
```

### 3. High Availability
```yaml
# Configure HA deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: argocd-server
  namespace: argocd
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
```



---

## Resources and References

### Official Resources:
- **ArgoCD Installation**: argoproj.github.io/argo-cd/getting_started/
- **ArgoCD Configuration**: argoproj.github.io/argo-cd/operator-manual/
- **ArgoCD CLI**: argoproj.github.io/argo-cd/user-guide/commands/

### Key Installation Steps:
1. **Choose Installation Method**: Manifest, Helm, or Operator
2. **Verify Components**: Ensure all pods are running
3. **Configure Access**: Set up UI/CLI access
4. **Setup Authentication**: Configure admin access or SSO
5. **Test Installation**: Create first application

### Troubleshooting Checklist:
- [ ] All pods are running
- [ ] Services are accessible
- [ ] Authentication is working
- [ ] CLI can connect
- [ ] UI is accessible 