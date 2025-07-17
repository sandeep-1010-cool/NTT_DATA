**# ArgoCD Hands-on Practice Guide: Step-by-Step Lab**

## 🔍 Overview

This guide provides a structured, time-bound, hands-on lab to learn and practice ArgoCD with focus on:

* UI & CLI application creation
* Deployment methods (YAML, Helm, Kustomize)
* Advanced sync and health policies
* Multi-cluster setups
* Troubleshooting and cleanup

---

## 🔢 Lab Phases

### **Phase 1: Environment Setup (5 min)**

#### ✅ Killercoda Lab

* Visit: [https://killercoda.com](https://killercoda.com/neo-lab/course/scenarios/argocd-killercoda)
* Search for "ArgoCD" or "Kubernetes"
* Launch Kubernetes + ArgoCD scenario
* **Note**: Killercoda environments are pre-configured with Kubernetes and often have ArgoCD pre-installed
* **Time Limit**: 60 minutes per session

#### 👉 Verify Setup:

```bash
# Purpose: Check if Kubernetes cluster is ready and accessible
kubectl cluster-info
# Expected: Shows cluster info and API server URL

# Purpose: Verify ArgoCD pods are running properly
kubectl get pods -n argocd
# Expected: Shows all ArgoCD components (server, repo-server, controller, etc.) in Running state

# Purpose: Check ArgoCD services for networking setup
kubectl get svc -n argocd
# Expected: Shows argocd-server and other services with their cluster IPs
```

#### 🔧 **Killercoda Installation Steps (if ArgoCD not pre-installed):**

```bash
# Purpose: Add ArgoCD Helm repository
helm repo add argo https://argoproj.github.io/argo-helm
# Expected: "argo" has been added to your repositories

# Purpose: Update Helm repositories to get latest charts
helm repo update
# Expected: Hang tight while we grab the latest from your chart repositories...

# Purpose: Render ArgoCD manifest without CRDs (Helm v8.0.17)
helm template argo-cd argo/argo-cd \
  --version 8.0.17 \
  --namespace argocd \
  --set crds.install=false > argo-template.yaml
# Expected: Creates argo-template.yaml file with ArgoCD manifests

# Purpose: Create ArgoCD namespace
kubectl create namespace argocd
# Expected: namespace/argocd created

# Purpose: Apply ArgoCD manifests to cluster
kubectl apply -f argo-template.yaml
# Expected: Multiple resources created (deployments, services, configmaps, etc.)

# Purpose: Verify ArgoCD installation
kubectl get pods -n argocd
# Expected: Shows all ArgoCD pods in Running state

# Purpose: Check ArgoCD server logs if pod is in Error/CrashLoopBackOff state
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows error messages explaining why server is failing

# Purpose: Check application controller logs if not ready (0/1 Running)
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-application-controller -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows application controller startup logs or errors

# Purpose: Check ApplicationSet controller logs if in CrashLoopBackOff
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-applicationset-controller -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows error messages for ApplicationSet controller crashes

# Purpose: Get detailed information about failing pods
kubectl describe pod -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows events, resource limits, and failure reasons

# Purpose: Check resource usage to identify constraints
kubectl top pods -n argocd
# Expected: Shows CPU and memory usage for all pods

# Purpose: Check cluster events for failure clues
kubectl get events -n argocd --sort-by='.lastTimestamp'
# Expected: Shows recent events that might explain the failures

# Purpose: Check if CRDs are missing (common cause of "resource not found" errors)
kubectl get crds | grep argoproj
# Expected: Should show applications.argoproj.io, appprojects.argoproj.io, etc.
# If empty, CRDs are missing and need to be installed
```
```

**Note**: CRDs must be installed separately if not already present in the cluster.

#### 🔧 **Fix Missing CRDs (if troubleshooting shows "resource not found" errors):**

```bash
# Purpose: Install ArgoCD CRDs that are required for operation
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.4/manifests/crds.yaml
# Expected: Multiple CRDs created (applications.argoproj.io, appprojects.argoproj.io, etc.)

# Purpose: Restart ArgoCD deployments to pick up the new CRDs
kubectl rollout restart deployment -n argocd
# Expected: All ArgoCD deployments restart and become healthy

# Purpose: Verify CRDs are now available
kubectl get crds | grep argoproj
# Expected: Shows applications.argoproj.io, appprojects.argoproj.io, etc.

# Purpose: Verify ArgoCD pods are now healthy
kubectl get pods -n argocd
# Expected: All pods should be in Running state with 1/1 Ready
```


---

### **Phase 2: Access ArgoCD UI (5 min)**

```bash
# Purpose: Create secure tunnel to access ArgoCD web UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Expected: Forwarding from 127.0.0.1:8080 -> 443:8080

# Purpose: Retrieve admin password for ArgoCD login
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
# Expected: Outputs the admin password (e.g., "abc123def456")
```

* **URL**: [https://localhost:8080](https://localhost:8080)
* **Username**: `admin`
* **Password**: (from command above)

---

### **Phase 3: CLI Setup & Login (3 min)**

```bash
# Purpose: Download ArgoCD CLI binary for Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
# Expected: Downloads argocd-linux-amd64 file to current directory

# Purpose: Install CLI with execute permissions to system PATH
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
# Expected: argocd command becomes available system-wide

# Purpose: Authenticate with ArgoCD server using CLI
argocd login localhost:8080 --username admin --password <password>
# Expected: Login successful
```

---

### **Phase 4: Deploy First App - Guestbook (CLI) (7 min)**

```bash
# Purpose: Create a guestbook application with automated sync policy
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps \
  --path guestbook \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc \
  --sync-policy automated
# Expected: Application 'guestbook' created

# Purpose: List all ArgoCD applications to verify creation
argocd app list
# Expected: Shows guestbook app with status (Healthy/Synced/OutOfSync)

# Purpose: Get detailed information about the guestbook application
argocd app get guestbook
# Expected: Shows detailed app configuration, sync status, and health information
```

---

### **Phase 5: YAML-Based App Deployment (5 min)**

```bash
cat << EOF > guestbook-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-yaml
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
EOF

kubectl apply -f guestbook-app.yaml
```

---

### **Phase 6: Custom App Deployment (7 min)**

```bash
mkdir my-app && cd my-app

# Create Deployment
cat << EOF > deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: nginx:alpine
        ports:
        - containerPort: 80
EOF

# Create Service
cat << EOF > service.yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 80
  type: ClusterIP
EOF
```

> Commit these to GitHub and deploy using an Application YAML manifest similar to guestbook.

---

### **Phase 7: Monitor & Sync (5 min)**

```bash
argocd app sync guestbook
argocd app resources guestbook
argocd app logs guestbook
```

---

### **Phase 8: Simulate & Resolve Sync Failure (5 min)**

```bash
kubectl delete deployment guestbook -n default
argocd app get guestbook
argocd app sync guestbook
```

---

### **Phase 9: Advanced App Management (5 min)**

```bash
# Enable sync policy & self-heal
argocd app set guestbook --sync-policy automated --self-heal --prune

# Check app history
argocd app history guestbook

# Create hook job
kubectl apply -f - <<EOF
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
      - name: hook
        image: busybox
        command: ["echo", "Running PreSync Hook"]
      restartPolicy: Never
EOF
```

---

### **Phase 10: Cleanup (3 min)**

```bash
argocd app delete guestbook --cascade
kubectl delete all -l app=guestbook -n default
```

---

## 📅 Practice Time Management Summary

| Task               | Time       |
| ------------------ | ---------- |
| Environment Setup  | 5 min      |
| UI Access          | 5 min      |
| CLI Setup          | 3 min      |
| Deploy App via CLI | 7 min      |
| YAML Deployment    | 5 min      |
| Custom App Deploy  | 7 min      |
| Monitoring         | 5 min      |
| Failure Simulation | 5 min      |
| Advanced Settings  | 5 min      |
| Cleanup            | 3 min      |
| **Total**          | **50 min** |

---

## 🛠️ **Troubleshooting Tips**

### **Common Issues & Solutions:**

```bash
# Issue: Port forward fails
# Solution: Check if ArgoCD server is running
kubectl get pods -n argocd | grep argocd-server

# Issue: Login fails
# Solution: Verify password and server URL
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Issue: App sync fails
# Solution: Check application logs
argocd app logs guestbook

# Issue: Pods not starting
# Solution: Check resource constraints
kubectl describe pods -n default | grep guestbook

# Issue: CrashLoopBackOff in ArgoCD pods
# Solution: Check pod logs and events
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].metadata.name}')
kubectl describe pod -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].metadata.name}')

# Issue: Application controller not ready (0/1 Running)
# Solution: Check application controller logs
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-application-controller -o jsonpath='{.items[0].metadata.name}')

# Issue: ApplicationSet controller CrashLoopBackOff
# Solution: Check ApplicationSet controller logs
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-applicationset-controller -o jsonpath='{.items[0].metadata.name}')

# Issue: "resource not found" errors (applications.argoproj.io, appprojects.argoproj.io)
# Solution: Check if CRDs are installed
kubectl get crds | grep argoproj
# Expected: Should show ArgoCD CRDs, if empty run: kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/v2.8.4/manifests/crds.yaml
```
```

### **Quick Verification Commands:**

```bash
# Verify ArgoCD installation
kubectl get all -n argocd

# Check application health
argocd app get guestbook --output yaml

# Test repository connectivity
argocd repo list

# Verify cluster connectivity
argocd cluster list
```

---

## 📄 Practice Checklist

* [x] ArgoCD CLI installed and logged in
* [x] Guestbook deployed via CLI and YAML
* [x] Custom app created from scratch
* [x] Synced, observed, and troubleshot sync issues
* [x] Practiced RBAC, hooks, and self-healing
* [x] Cleaned up all resources

---

## 🔗 Reference

* Docs: [https://argo-cd.readthedocs.io](https://argo-cd.readthedocs.io)
* Examples: [https://github.com/argoproj/argocd-example-apps](https://github.com/argoproj/argocd-example-apps)
