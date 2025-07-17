# ArgoCD Killercoda Labs - Step-by-Step Hands-on Practice

## 🎯 **Learning Approach**

This guide provides **guided exercises** in Killercoda with each step explained with **underlying concepts**. The labs match the theory and examples for seamless learning.

---

## 🚀 **Lab 1: ArgoCD Installation & Basic Setup**

### **Objective**: Install ArgoCD and access the UI

### **Step 1: Access Killercoda Environment**

```bash
# Purpose: Navigate to Killercoda ArgoCD scenario
# Visit: https://killercoda.com/neo-lab/course/scenarios/argocd-killercoda
# Expected: Killercoda environment loads with Kubernetes cluster
```

### **Step 2: Verify Kubernetes Cluster**

```bash
# Purpose: Check if Kubernetes cluster is ready and accessible
kubectl cluster-info
# Expected: Shows cluster info and API server URL

# Purpose: Verify cluster nodes are available
kubectl get nodes
# Expected: Shows cluster nodes in Ready state
```

### **Step 3: Install ArgoCD**

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
```

### **Step 4: Install CRDs (Required)**

```bash
# Purpose: Install ArgoCD CRDs that are required for operation
kubectl apply -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/crds.yaml
# Expected: Multiple CRDs created (applications.argoproj.io, appprojects.argoproj.io, etc.)

# Purpose: Restart ArgoCD deployments to pick up the new CRDs
kubectl rollout restart deployment -n argocd
# Expected: All ArgoCD deployments restart and become healthy
```

### **Step 5: Verify Installation**

```bash
# Purpose: Check if all ArgoCD pods are running properly
kubectl get pods -n argocd
# Expected: Shows all ArgoCD components (server, repo-server, controller, etc.) in Running state

# Purpose: Check ArgoCD services for networking setup
kubectl get svc -n argocd
# Expected: Shows argocd-server and other services with their cluster IPs
```

### **Step 6: Access ArgoCD UI**

```bash
# Purpose: Create secure tunnel to access ArgoCD web UI
kubectl port-forward svc/argo-cd-argocd-server -n argocd 8080:443
# Expected: Forwarding from 127.0.0.1:8080 -> 443:8080

# Purpose: Retrieve admin password for ArgoCD login
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
# Expected: Outputs the admin password (e.g., "abc123def456")
```

**UI Access**:
- **URL**: https://localhost:8080
- **Username**: `admin`
- **Password**: (from command above)

---

## 🛠️ **Lab 2: CLI Setup & First Application**

### **Objective**: Install CLI and create your first application

### **Step 1: Install ArgoCD CLI**

```bash
# Purpose: Download ArgoCD CLI binary for Linux
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
# Expected: Downloads argocd-linux-amd64 file to current directory

# Purpose: Install CLI with execute permissions to system PATH
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
# Expected: argocd command becomes available system-wide

# Purpose: Verify CLI installation
argocd version
# Expected: Shows ArgoCD CLI version
```

### **Step 2: Login to ArgoCD**

```bash
# Purpose: Authenticate with ArgoCD server using CLI
argocd login localhost:8080 --username admin --password <password-from-step-6>
# Expected: Login successful
```

### **Step 3: Create Your First Application**

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

### **Step 4: Monitor Application Deployment**

```bash
# Purpose: Manually trigger synchronization of the guestbook application
argocd app sync guestbook
# Expected: Shows sync progress and result (e.g., "Application 'guestbook' synced successfully")

# Purpose: List all Kubernetes resources managed by the guestbook application
argocd app resources guestbook
# Expected: Shows table of resources (deployments, services, etc.) with their sync status

# Purpose: View application deployment logs and sync history
argocd app logs guestbook
# Expected: Shows detailed logs of application deployment and sync operations
```

---

## 🔄 **Lab 3: YAML-Based Application Deployment**

### **Objective**: Learn YAML-based application creation

### **Step 1: Create Application YAML**

```bash
# Purpose: Create ArgoCD Application YAML manifest for guestbook deployment
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
# Expected: Creates guestbook-app.yaml file with ArgoCD Application definition
```

### **Step 2: Apply YAML Application**

```bash
# Purpose: Apply the ArgoCD Application manifest to create the application
kubectl apply -f guestbook-app.yaml
# Expected: application.argoproj.io/guestbook-yaml created

# Purpose: Verify application was created
argocd app list
# Expected: Shows both guestbook and guestbook-yaml applications
```

### **Step 3: Compare Applications**

```bash
# Purpose: Compare the two applications to see differences
argocd app get guestbook
argocd app get guestbook-yaml
# Expected: Shows different application configurations and states
```

---

## 🧪 **Lab 4: Drift Detection & Self-Healing**

### **Objective**: Understand drift detection and self-healing

### **Step 1: Simulate Drift**

```bash
# Purpose: Simulate a deployment failure by deleting the guestbook deployment
kubectl delete deployment guestbook -n default
# Expected: deployment.apps/guestbook deleted

# Purpose: Check application status after the deletion to see drift detection
argocd app get guestbook
# Expected: Shows application as "OutOfSync" due to missing deployment
```

### **Step 2: Observe Self-Healing**

```bash
# Purpose: Wait for automated self-healing to occur
sleep 30
# Expected: ArgoCD automatically recreates the deployment

# Purpose: Verify the deployment was recreated
kubectl get deployment guestbook -n default
# Expected: Shows deployment is running again
```

### **Step 3: Manual Sync (Alternative)**

```bash
# Purpose: Manually sync to restore the deleted deployment (if self-healing didn't work)
argocd app sync guestbook
# Expected: Recreates the deployment and shows "Application 'guestbook' synced successfully"
```

---

## 🔐 **Lab 5: Projects and RBAC**

### **Objective**: Learn access control with projects

### **Step 1: Create a Project**

```bash
# Purpose: Create a restricted project for applications
cat << EOF > my-project.yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: my-project
  namespace: argocd
spec:
  description: My restricted project
  sourceRepos:
  - 'https://github.com/argoproj/argocd-example-apps'
  destinations:
  - namespace: my-apps
    server: https://kubernetes.default.svc
  clusterResourceWhitelist:
  - group: ''
    kind: Namespace
  namespaceResourceWhitelist:
  - group: ''
    kind: ConfigMap
  - group: 'apps'
    kind: Deployment
  - group: ''
    kind: Service
EOF

# Purpose: Apply the project configuration
kubectl apply -f my-project.yaml
# Expected: appproject.argoproj.io/my-project created
```

### **Step 2: Create Application in Project**

```bash
# Purpose: Create application within the restricted project
cat << EOF > project-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: project-guestbook
  namespace: argocd
spec:
  project: my-project
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: my-apps
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF

# Purpose: Apply the project application
kubectl apply -f project-app.yaml
# Expected: application.argoproj.io/project-guestbook created
```

### **Step 3: Verify Project Restrictions**

```bash
# Purpose: Check that the application is in the correct project
argocd app get project-guestbook
# Expected: Shows project: my-project

# Purpose: Verify namespace creation
kubectl get namespace my-apps
# Expected: Shows my-apps namespace created
```

---

## 🚀 **Lab 6: Advanced Features - ApplicationSets**

### **Objective**: Learn ApplicationSets for multi-environment deployment

### **Step 1: Create ApplicationSet**

```bash
# Purpose: Create ApplicationSet for multi-environment deployment
cat << EOF > guestbook-appset.yaml
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: guestbook
spec:
  generators:
  - list:
      elements:
      - env: development
        namespace: dev
      - env: staging
        namespace: staging
      - env: production
        namespace: prod
  template:
    metadata:
      name: 'guestbook-{{env}}'
    spec:
      project: default
      source:
        repoURL: https://github.com/argoproj/argocd-example-apps
        targetRevision: HEAD
        path: guestbook
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
EOF

# Purpose: Apply the ApplicationSet
kubectl apply -f guestbook-appset.yaml
# Expected: applicationset.argoproj.io/guestbook created
```

### **Step 2: Verify Generated Applications**

```bash
# Purpose: Check that applications were generated
argocd app list
# Expected: Shows guestbook-development, guestbook-staging, guestbook-production

# Purpose: Check application status
argocd app get guestbook-development
# Expected: Shows application details for development environment
```

---

## 🔧 **Lab 7: Sync Hooks and Advanced Features**

### **Objective**: Learn sync hooks and advanced application features

### **Step 1: Create Pre-Sync Hook**

```bash
# Purpose: Create a pre-sync hook job for validation
cat << EOF > pre-sync-hook.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pre-sync-validation
  annotations:
    argocd.argoproj.io/hook: PreSync
    argocd.argoproj.io/hook-delete-policy: BeforeHookCreation
spec:
  template:
    spec:
      containers:
      - name: validation
        image: busybox
        command: ["/bin/sh", "-c"]
        args:
        - |
          echo "Running pre-sync validation..."
          echo "Validation completed successfully"
      restartPolicy: Never
  backoffLimit: 1
EOF

# Purpose: Apply the pre-sync hook
kubectl apply -f pre-sync-hook.yaml
# Expected: job.batch/pre-sync-validation created
```

### **Step 2: Create Application with Hooks**

```bash
# Purpose: Create application that includes the hook
cat << EOF > hook-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hook-guestbook
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: hook-test
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
EOF

# Purpose: Apply the application with hooks
kubectl apply -f hook-app.yaml
# Expected: application.argoproj.io/hook-guestbook created
```

### **Step 3: Monitor Hook Execution**

```bash
# Purpose: Check hook job execution
kubectl get jobs -n hook-test
# Expected: Shows pre-sync-validation job

# Purpose: Check hook logs
kubectl logs job/pre-sync-validation -n hook-test
# Expected: Shows validation messages
```

---

## 📊 **Lab 8: Monitoring and Troubleshooting**

### **Objective**: Learn monitoring and troubleshooting techniques

### **Step 1: Check Application Health**

```bash
# Purpose: Get detailed application health information
argocd app get guestbook --output yaml
# Expected: Shows complete YAML configuration with health status and sync state

# Purpose: Check application resources
argocd app resources guestbook
# Expected: Shows all resources managed by the application
```

### **Step 2: Troubleshoot Common Issues**

```bash
# Purpose: Simulate a sync failure
kubectl delete deployment guestbook -n default
# Expected: deployment.apps/guestbook deleted

# Purpose: Check application status
argocd app get guestbook
# Expected: Shows OutOfSync status

# Purpose: Check application logs
argocd app logs guestbook
# Expected: Shows detailed logs

# Purpose: Force sync to resolve issue
argocd app sync guestbook --force
# Expected: Forces sync and resolves the issue
```

### **Step 3: Monitor ArgoCD Components**

```bash
# Purpose: Check ArgoCD component health
kubectl get pods -n argocd
# Expected: Shows all ArgoCD components in Running state

# Purpose: Check ArgoCD server logs
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-server -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows server logs

# Purpose: Check application controller logs
kubectl logs -n argocd $(kubectl get pods -n argocd -l app.kubernetes.io/name=argocd-application-controller -o jsonpath='{.items[0].metadata.name}')
# Expected: Shows controller logs
```

---

## 🧹 **Lab 9: Cleanup and Best Practices**

### **Objective**: Learn proper cleanup and best practices

### **Step 1: Clean Up Applications**

```bash
# Purpose: Delete ArgoCD applications
argocd app delete guestbook --cascade
# Expected: Application 'guestbook' deleted

# Purpose: Delete other applications
argocd app delete guestbook-yaml --cascade
argocd app delete project-guestbook --cascade
# Expected: Applications deleted

# Purpose: Verify cleanup
argocd app list
# Expected: Shows remaining applications or empty list
```

### **Step 2: Clean Up Kubernetes Resources**

```bash
# Purpose: Remove any remaining Kubernetes resources
kubectl delete all -l app=guestbook -n default
# Expected: Removes any remaining deployments, services, etc. with app=guestbook label

# Purpose: Clean up namespaces
kubectl delete namespace my-apps hook-test dev staging prod
# Expected: Removes test namespaces
```

### **Step 3: Best Practices Verification**

```bash
# Purpose: Verify ArgoCD installation is still healthy
kubectl get all -n argocd
# Expected: Shows all ArgoCD resources (pods, services, deployments) in healthy state

# Purpose: Check cluster resources
kubectl top nodes
# Expected: Shows node resource usage

# Purpose: Verify no orphaned resources
kubectl get all --all-namespaces | grep guestbook
# Expected: No guestbook resources remaining
```

---

## 📚 **Lab Summary**

### **What You've Learned:**

1. ✅ **ArgoCD Installation**: Complete setup and verification
2. ✅ **CLI Operations**: Login, app creation, monitoring
3. ✅ **YAML Applications**: Declarative application management
4. ✅ **Drift Detection**: Understanding sync states and self-healing
5. ✅ **Projects & RBAC**: Access control and organization
6. ✅ **ApplicationSets**: Multi-environment automation
7. ✅ **Sync Hooks**: Pre/post deployment workflows
8. ✅ **Monitoring**: Health checks and troubleshooting
9. ✅ **Cleanup**: Proper resource management

### **Key Concepts Applied:**

- **GitOps Workflow**: Git as source of truth
- **Declarative Management**: YAML-based configuration
- **Automated Sync**: Self-healing and drift detection
- **Access Control**: Projects and RBAC
- **Multi-Environment**: ApplicationSets for scaling
- **Monitoring**: Health checks and observability

### **Next Steps:**

1. **Practice**: Repeat labs with different applications
2. **Explore**: Try Helm charts and Kustomize
3. **Advanced**: Implement notifications and custom health checks
4. **Production**: Apply security best practices

---

## 🎯 **Success Criteria**

You have successfully completed the ArgoCD labs when you can:

- ✅ Install and configure ArgoCD
- ✅ Create applications via CLI and YAML
- ✅ Understand sync policies and drift detection
- ✅ Use projects for access control
- ✅ Implement ApplicationSets for multi-environment
- ✅ Use sync hooks for advanced workflows
- ✅ Monitor and troubleshoot applications
- ✅ Clean up resources properly

**Congratulations! You're now ready to work with ArgoCD in production environments.** 🚀 