# ArgoCD Hands-on Guide - Step-by-Step Tutorials

## Table of Contents
1. [Creating Your First Application](#creating-your-first-application)
2. [Application Deployment Examples](#application-deployment-examples)
3. [CLI Commands Reference](#cli-commands-reference)
4. [Advanced Configuration](#advanced-configuration)
5. [Multi-Cluster Management](#multi-cluster-management)
6. [Troubleshooting Common Issues](#troubleshooting-common-issues)

---

## Creating Your First Application

### Application Creation via UI:
```
1. Click "Create Application"
2. Fill Application Details:
   - Name: "my-first-application"
   - Project: "default"
   - Sync Policy: "Automatic"
3. Repository Configuration:
   - Repository URL: "https://github.com/argoproj/argocd-example-apps"
   - Path: "guestbook"
4. Destination:
   - Cluster: "in-cluster"
   - Namespace: "default"
```

### Application Creation via CLI:
```bash
# Install ArgoCD CLI
# macOS
brew install argocd

# Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd

# Login to ArgoCD
argocd login <argocd-server-url>

# Create application
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps \
  --path guestbook \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc
```

![Application Creation](https://argoproj.github.io/argo-cd/images/app-creation.png)
*Creating ArgoCD Application*

### Application YAML Example:
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

---

## Application Deployment Examples

### Example 1: Plain Kubernetes Manifests
```yaml
# Repository Structure
guestbook/
├── deployment.yaml
└── service.yaml
```

**deployment.yaml:**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: guestbook
spec:
  replicas: 3
  selector:
    matchLabels:
      app: guestbook
  template:
    metadata:
      labels:
        app: guestbook
    spec:
      containers:
      - name: guestbook
        image: gcr.io/google-samples/gb-frontend:v5
        ports:
        - containerPort: 80
```

**service.yaml:**
```yaml
apiVersion: v1
kind: Service
metadata:
  name: guestbook
spec:
  selector:
    app: guestbook
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

### Example 2: Helm Charts
```yaml
# Repository Structure
helm-guestbook/
├── Chart.yaml
├── values.yaml
└── templates/
    ├── deployment.yaml
    └── service.yaml
```

**Chart.yaml:**
```yaml
apiVersion: v2
name: guestbook
description: A Helm chart for Guestbook application
version: 0.1.0
appVersion: "1.0.0"
```

**values.yaml:**
```yaml
replicaCount: 3
image:
  repository: gcr.io/google-samples/gb-frontend
  tag: v5
service:
  type: LoadBalancer
  port: 80
```

### Example 3: Kustomize
```yaml
# Repository Structure
kustomize-guestbook/
├── base/
│   ├── deployment.yaml
│   └── service.yaml
└── overlays/
    └── production/
        └── kustomization.yaml
```

**kustomization.yaml:**
```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- ../../base
patches:
- path: replica-patch.yaml
  target:
    kind: Deployment
    name: guestbook
```

![Different Manifest Formats](https://argoproj.github.io/argo-cd/images/manifest-formats.png)
*ArgoCD Supports Multiple Manifest Formats*

---

## CLI Commands Reference

### Essential CLI Commands:
```bash
# List applications
argocd app list

# Get application details
argocd app get <app-name>

# Sync application
argocd app sync <app-name>

# Delete application
argocd app delete <app-name>

# Get application logs
argocd app logs <app-name>

# Set sync policy
argocd app set <app-name> --sync-policy automated
```

### Application Status Commands:
```bash
# Check application health
argocd app get <app-name> --output yaml

# View application resources
argocd app resources <app-name>

# Get application events
argocd app events <app-name>

# Check application history
argocd app history <app-name>
```

### Cluster Management:
```bash
# List clusters
argocd cluster list

# Add cluster
argocd cluster add <cluster-name> --server <cluster-url>

# Remove cluster
argocd cluster rm <cluster-name>

# Get cluster info
argocd cluster get <cluster-name>
```

### Repository Management:
```bash
# List repositories
argocd repo list

# Add repository
argocd repo add <repo-url>

# Remove repository
argocd repo rm <repo-url>

# Get repository info
argocd repo get <repo-url>
```

![ArgoCD CLI Interface](https://argoproj.github.io/argo-cd/images/cli-interface.png)
*ArgoCD Command Line Interface*

---

## Advanced Configuration

### Sync Policies:
```yaml
# Automatic sync with pruning
spec:
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - CreateNamespace=true
```

### Application Health Checks:
```yaml
# Custom health checks
spec:
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    syncOptions:
    - PrunePropagationPolicy=foreground
    - PruneLast=true
```

### Resource Hooks:
```yaml
# Pre-sync and post-sync hooks
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-sync-job
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: BeforeHookCreation
spec:
  template:
    spec:
      containers:
      - name: pre-sync
        image: busybox
        command: ["echo", "Pre-sync hook executed"]
      restartPolicy: Never
  backoffLimit: 1
```

### Sync Windows:
```yaml
# Configure sync windows
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook
spec:
  syncPolicy:
    syncOptions:
    - RespectIgnoreDifferences=true
  syncWindows:
  - kind: allow
    schedule: "10 1 * * *"
    duration: 1h
    applications:
    - guestbook
```

![Advanced Configuration](https://argoproj.github.io/argo-cd/images/advanced-config.png)
*Advanced ArgoCD Configuration Options*

---

## Multi-Cluster Management

### Application Sets (Multi-Cluster):
```yaml
# Generate applications for multiple clusters
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: guestbook
spec:
  generators:
  - list:
      elements:
      - cluster: cluster-1
        url: https://cluster-1.example.com
      - cluster: cluster-2
        url: https://cluster-2.example.com
  template:
    metadata:
      name: '{{cluster}}-guestbook'
    spec:
      project: default
      source:
        repoURL: https://github.com/argoproj/argocd-example-apps
        targetRevision: HEAD
        path: guestbook
      destination:
        server: '{{url}}'
        namespace: default
```

### Cluster Management:
```bash
# Add multiple clusters
argocd cluster add cluster-1 --server https://cluster-1.example.com
argocd cluster add cluster-2 --server https://cluster-2.example.com

# List all clusters
argocd cluster list

# Get cluster details
argocd cluster get cluster-1
```

![Multi-Cluster Architecture](https://argoproj.github.io/argo-cd/images/multi-cluster.png)
*GitOps Multi-Cluster Management*

![Cluster Management](https://www.gitops.tech/images/cluster-management.png)
*Centralized Cluster Management with GitOps*

---

## Troubleshooting Common Issues

### Issue 1: Application Sync Failed
```bash
# Check application status
argocd app get <app-name>

# Check application logs
argocd app logs <app-name>

# Manual sync
argocd app sync <app-name>

# Check sync history
argocd app history <app-name>
```

### Issue 2: Repository Connection Issues
```bash
# Test repository connection
argocd repo list

# Check repository logs
argocd repo get <repo-url>

# Re-add repository
argocd repo rm <repo-url>
argocd repo add <repo-url>
```

### Issue 3: Resource Conflicts
```bash
# Check resource status
kubectl get all -n <namespace>

# Check events
kubectl get events -n <namespace>

# Force sync with replace
argocd app sync <app-name> --replace
```

### Issue 4: Authentication Problems
```bash
# Check login status
argocd account get-user-info

# Re-login
argocd logout
argocd login <server-url>

# Check RBAC
argocd account list-roles
```

### Issue 5: Network Connectivity
```bash
# Test cluster connectivity
argocd cluster list

# Check cluster health
argocd cluster get <cluster-name>

# Test repository access
argocd repo list
```

![Troubleshooting Dashboard](https://argoproj.github.io/argo-cd/images/troubleshooting.png)
*ArgoCD Troubleshooting Interface*

---

## Best Practices

### 1. Application Structure
```
Recommended Structure:
├── applications/
│   ├── app1/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── configmap.yaml
│   └── app2/
├── infrastructure/
│   ├── namespaces/
│   ├── nodes/
│   └── policies/
└── argocd/
    └── applications/
```

![Repository Layout](https://www.gitops.tech/images/repository-structure.png)
*Recommended GitOps Repository Structure*

### 2. Security Considerations
```
- Use RBAC for GitOps controller
- Implement proper authentication
- Regular security audits
- Monitor for unauthorized changes
```

![Security Model](https://www.gitops.tech/images/security-model.png)
*GitOps Security Architecture*

### 3. Monitoring and Observability
```
- Monitor GitOps controller health
- Track deployment status
- Alert on drift detection
- Log all reconciliation activities
```

![Monitoring Dashboard](https://argoproj.github.io/argo-cd/images/monitoring-dashboard.png)
*GitOps Monitoring and Observability*

---

## Resources and References

### Official Resources:
- **ArgoCD Examples**: github.com/argoproj/argocd-example-apps
- **ArgoCD CLI Reference**: argoproj.github.io/argo-cd/user-guide/commands/
- **ArgoCD Best Practices**: argoproj.github.io/argo-cd/user-guide/best_practices/

### **🔧 Hands-on Practice Resources:**
![ArgoCD Example Apps](https://github.com/argoproj/argocd-example-apps/raw/main/images/example-apps.png)
*ArgoCD Example Applications Repository*

### Key Learning Path:
1. **Start with Simple Apps**: Use guestbook example
2. **Learn CLI Commands**: Practice with basic operations
3. **Explore Advanced Features**: Sync policies, hooks, windows
4. **Multi-Cluster Setup**: Application sets and cluster management
5. **Troubleshooting**: Common issues and solutions

### Practice Checklist:
- [ ] Create first application via UI
- [ ] Create application via CLI
- [ ] Test different manifest formats
- [ ] Configure sync policies
- [ ] Set up multi-cluster deployment
- [ ] Troubleshoot common issues 