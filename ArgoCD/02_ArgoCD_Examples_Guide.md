# ArgoCD Examples Guide - Concept-Based Practical Examples

## 🎯 **Learning Approach**

This guide provides **ordered examples** for each ArgoCD concept, aligned with the theory for easy mapping. Each example builds upon the previous one, creating a progressive learning path.

---

## 📚 **Phase 1: Basic Application Examples**

### 🔹 **Example 1: Simple Guestbook Application**

**Concept**: Basic ArgoCD Application with manual sync

**Purpose**: Learn fundamental Application structure and manual sync workflow

```yaml
# guestbook-basic.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-basic
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
  syncPolicy: {}  # Manual sync only
```

**Expected Outcome**: Application created, requires manual sync to deploy

**CLI Commands**:
```bash
# Apply the application
kubectl apply -f guestbook-basic.yaml

# Check application status
argocd app get guestbook-basic

# Manual sync
argocd app sync guestbook-basic
```

---

### 🔹 **Example 2: Automated Sync Application**

**Concept**: Automated sync with drift detection

**Purpose**: Learn automated sync policies and drift detection

```yaml
# guestbook-automated.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-automated
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
      prune: true        # Remove resources not in Git
      selfHeal: true     # Automatically fix drift
      allowEmpty: false  # Don't sync if no resources
```

**Expected Outcome**: Application automatically syncs and self-heals

**CLI Commands**:
```bash
# Apply and watch sync
kubectl apply -f guestbook-automated.yaml
argocd app sync guestbook-automated

# Simulate drift and observe self-heal
kubectl delete deployment guestbook -n default
# Watch ArgoCD automatically recreate the deployment
```

---

### 🔹 **Example 3: Multi-Environment Application**

**Concept**: Same application across different environments

**Purpose**: Learn environment-specific configurations

```yaml
# guestbook-dev.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-dev
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: dev
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

```yaml
# guestbook-prod.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: guestbook-prod
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: guestbook
  destination:
    server: https://kubernetes.default.svc
    namespace: prod
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

**Expected Outcome**: Same application deployed to different namespaces

---

## 🏗️ **Phase 2: Project and RBAC Examples**

### 🔹 **Example 4: Project with Restrictions**

**Concept**: AppProject with source and destination restrictions

**Purpose**: Learn access control and project scoping

```yaml
# my-project.yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: my-project
  namespace: argocd
spec:
  description: My restricted project
  sourceRepos:
  - 'https://github.com/my-org/my-repo'  # Only this repo allowed
  destinations:
  - namespace: my-apps
    server: https://kubernetes.default.svc
  - namespace: my-apps-staging
    server: https://kubernetes.default.svc
  clusterResourceWhitelist:
  - group: ''
    kind: Namespace
  namespaceResourceWhitelist:
  - group: ''
    kind: ConfigMap
  - group: ''
    kind: Secret
  - group: 'apps'
    kind: Deployment
  - group: ''
    kind: Service
```

**Expected Outcome**: Project restricts which repos and resources can be used

---

### 🔹 **Example 5: RBAC Configuration**

**Concept**: Role-based access control setup

**Purpose**: Learn user access management

```yaml
# argocd-rbac-cm.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    # Admin can do everything
    p, role:admin, applications, *, */*, allow
    p, role:admin, repositories, *, *, allow
    p, role:admin, clusters, *, *, allow
    p, role:admin, projects, *, *, allow
    
    # Developer can manage applications in my-project
    p, role:developer, applications, *, my-project/*, allow
    p, role:developer, repositories, get, *, allow
    p, role:developer, clusters, get, *, allow
    
    # Readonly can only view
    p, role:readonly, applications, get, */*, allow
    p, role:readonly, repositories, get, *, allow
    p, role:readonly, clusters, get, *, allow
```

**Expected Outcome**: Different user roles with appropriate permissions

---

## 🚀 **Phase 3: Advanced Application Examples**

### 🔹 **Example 6: Helm Chart Application**

**Concept**: Deploying Helm charts through ArgoCD

**Purpose**: Learn Helm integration

```yaml
# nginx-helm.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: nginx-helm
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://charts.bitnami.com/bitnami
    targetRevision: 13.2.23
    chart: nginx
    helm:
      values: |
        replicaCount: 3
        service:
          type: LoadBalancer
        resources:
          requests:
            memory: 128Mi
            cpu: 100m
          limits:
            memory: 256Mi
            cpu: 200m
  destination:
    server: https://kubernetes.default.svc
    namespace: nginx
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

**Expected Outcome**: Nginx deployed via Helm chart with custom values

---

### 🔹 **Example 7: Kustomize Application**

**Concept**: Using Kustomize for environment-specific overlays

**Purpose**: Learn Kustomize integration

```yaml
# kustomize-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: kustomize-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/argoproj/argocd-example-apps
    targetRevision: HEAD
    path: kustomize-guestbook/overlays/production
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

**Expected Outcome**: Application deployed using Kustomize production overlay

---

### 🔹 **Example 8: ApplicationSet with List Generator**

**Concept**: Automatically create multiple applications

**Purpose**: Learn ApplicationSet for multi-cluster deployments

```yaml
# guestbook-appset.yaml
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
        namespace: default
      - cluster: cluster-2
        url: https://cluster-2.example.com
        namespace: default
      - cluster: cluster-3
        url: https://cluster-3.example.com
        namespace: staging
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
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

**Expected Outcome**: Three applications created automatically for different clusters

---

## 🔐 **Phase 4: Security and Hooks Examples**

### 🔹 **Example 9: Pre-Sync Hook**

**Concept**: Execute actions before application sync

**Purpose**: Learn sync hooks for deployment workflows

```yaml
# pre-sync-hook.yaml
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
          # Add your validation logic here
          echo "Validation completed successfully"
      restartPolicy: Never
  backoffLimit: 1
```

**Expected Outcome**: Hook runs before each sync operation

---

### 🔹 **Example 10: Post-Sync Hook**

**Concept**: Execute actions after successful sync

**Purpose**: Learn post-deployment workflows

```yaml
# post-sync-hook.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: post-sync-notification
  annotations:
    argocd.argoproj.io/hook: PostSync
    argocd.argoproj.io/hook-delete-policy: BeforeHookCreation
spec:
  template:
    spec:
      containers:
      - name: notification
        image: curlimages/curl
        command: ["curl", "-X", "POST"]
        args:
        - "-H"
        - "Content-Type: application/json"
        - "-d"
        - '{"text":"Application sync completed successfully"}'
        - "https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK"
      restartPolicy: Never
  backoffLimit: 1
```

**Expected Outcome**: Slack notification sent after successful sync

---

## 📊 **Phase 5: Monitoring and Health Examples**

### 🔹 **Example 11: Custom Health Check**

**Concept**: Custom health check for specific resources

**Purpose**: Learn health check customization

```yaml
# custom-health-check.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-cm
  namespace: argocd
data:
  resource.customizations.health.my-custom-resource: |
    hs = {}
    if obj.status ~= nil then
      if obj.status.phase == "Running" then
        hs.status = "Healthy"
        hs.message = "Resource is running"
      elseif obj.status.phase == "Failed" then
        hs.status = "Degraded"
        hs.message = "Resource has failed"
      else
        hs.status = "Progressing"
        hs.message = "Resource is progressing"
      end
    else
      hs.status = "Unknown"
      hs.message = "Status unknown"
    end
    return hs
```

**Expected Outcome**: Custom health check for specific resource types

---

### 🔹 **Example 12: Notification Configuration**

**Concept**: Configure notifications for application events

**Purpose**: Learn notification system

```yaml
# argocd-notifications-cm.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-notifications-cm
  namespace: argocd
data:
  service.slack: |
    token: $slack-token
  template.app-sync-succeeded: |
    message: |
      Application {{.app.metadata.name}} sync succeeded.
      Environment: {{.app.spec.destination.namespace}}
      Revision: {{.app.status.sync.revision}}
  trigger.on-sync-succeeded: |
    - when: app.status.operationState.phase in ['Succeeded']
      send: [app-sync-succeeded]
  subscriptions: |
    - recipients:
      - slack:my-channel
      triggers:
      - on-sync-succeeded
```

**Expected Outcome**: Slack notifications for sync events

---

## 🔧 **Phase 6: Best Practice Examples**

### 🔹 **Example 13: Repository Structure**

**Concept**: Organized Git repository for GitOps

**Purpose**: Learn proper repository organization

```
my-gitops-repo/
├── applications/
│   ├── guestbook/
│   │   ├── base/
│   │   │   ├── deployment.yaml
│   │   │   ├── service.yaml
│   │   │   └── kustomization.yaml
│   │   └── overlays/
│   │       ├── development/
│   │       │   ├── kustomization.yaml
│   │       │   └── patch.yaml
│   │       └── production/
│   │           ├── kustomization.yaml
│   │           └── patch.yaml
│   └── nginx/
│       ├── deployment.yaml
│       └── service.yaml
├── infrastructure/
│   ├── namespaces/
│   │   ├── development.yaml
│   │   └── production.yaml
│   └── network-policies/
│       └── default-deny.yaml
└── argocd/
    ├── applications/
    │   ├── guestbook-dev.yaml
    │   ├── guestbook-prod.yaml
    │   └── nginx.yaml
    └── projects/
        └── default.yaml
```

**Expected Outcome**: Well-organized repository structure

---

### 🔹 **Example 14: Complete Application with All Features**

**Concept**: Comprehensive application with all ArgoCD features

**Purpose**: Learn complete application configuration

```yaml
# comprehensive-app.yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: comprehensive-app
  namespace: argocd
  annotations:
    argocd.argoproj.io/sync-wave: "0"
spec:
  project: my-project
  source:
    repoURL: https://github.com/my-org/my-repo
    targetRevision: main
    path: applications/comprehensive-app
    helm:
      values: |
        replicaCount: 3
        image:
          tag: latest
        resources:
          requests:
            memory: 128Mi
            cpu: 100m
          limits:
            memory: 256Mi
            cpu: 200m
  destination:
    server: https://kubernetes.default.svc
    namespace: comprehensive-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
      allowEmpty: false
    syncOptions:
    - CreateNamespace=true
    - PrunePropagationPolicy=foreground
    - PruneLast=true
  revisionHistoryLimit: 10
  ignoreDifferences:
  - group: apps
    kind: Deployment
    jsonPointers:
    - /spec/replicas
```

**Expected Outcome**: Production-ready application with all features

---

## 📚 **Summary**

This examples guide provides:
- ✅ **Progressive learning** from basic to advanced concepts
- ✅ **Real-world scenarios** for each ArgoCD feature
- ✅ **Complete configurations** with explanations
- ✅ **Best practices** integrated throughout
- ✅ **Practical outcomes** for each example

**Next Steps**: Use these examples as reference for hands-on labs in Killercoda. 