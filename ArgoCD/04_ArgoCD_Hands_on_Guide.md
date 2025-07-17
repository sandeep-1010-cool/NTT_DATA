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

* Visit: [https://killercoda.com](https://killercoda.com)
* Search for "ArgoCD"
* Launch Kubernetes + ArgoCD scenario

#### 👉 Verify Setup:

```bash
kubectl cluster-info
kubectl get pods -n argocd
kubectl get svc -n argocd
```

---

### **Phase 2: Access ArgoCD UI (5 min)**

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

* **URL**: [https://localhost:8080](https://localhost:8080)
* **Username**: `admin`
* **Password**: (from command above)

---

### **Phase 3: CLI Setup & Login (3 min)**

```bash
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
argocd login localhost:8080 --username admin --password <password>
```

---

### **Phase 4: Deploy First App - Guestbook (CLI) (7 min)**

```bash
argocd app create guestbook \
  --repo https://github.com/argoproj/argocd-example-apps \
  --path guestbook \
  --dest-namespace default \
  --dest-server https://kubernetes.default.svc \
  --sync-policy automated

argocd app list
argocd app get guestbook
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
