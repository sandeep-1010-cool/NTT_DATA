# ArgoCD Theory Guide - Complete Conceptual Foundation

## 🎯 **Learning Objectives**

By the end of this guide, you will understand:
- GitOps principles and ArgoCD's role
- ArgoCD architecture and components
- Core concepts: Applications, Projects, Sync Policies
- Advanced features: ApplicationSets, RBAC, Security

---

## 📚 **Phase 1: GitOps Fundamentals**

### 🔹 **What is GitOps?**

**Definition**: GitOps is a modern DevOps methodology where Git serves as the single source of truth for declarative infrastructure and applications.

#### **Core Principles:**

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Git Repo      │    │   ArgoCD        │    │   Kubernetes    │
│   (Source of    │───▶│   (Controller)  │───▶│   (Target)      │
│   Truth)        │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

1. **Declarative**: Everything is described as code
2. **Version Controlled**: All changes tracked in Git
3. **Automated**: Changes automatically applied
4. **Auditable**: Complete change history

#### **Declarative vs Imperative Deployment:**

**Declarative (GitOps)**: Describe the desired state, let the system figure out how to achieve it
**Imperative (Traditional)**: Provide step-by-step commands to execute

GitOps declarative model provides better consistency, reproducibility, and auditability compared to imperative scripts.

#### **GitOps vs Traditional Deployment:**

| Aspect | Traditional | GitOps |
|--------|-------------|---------|
| **Source of Truth** | CI/CD Pipeline | Git Repository |
| **Deployment Trigger** | Manual/CI | Git Push |
| **Change Tracking** | Limited | Complete History |
| **Rollback** | Complex | Git Revert |
| **Audit** | Partial | Complete |

### 🔹 **Why ArgoCD?**

ArgoCD is a **declarative GitOps continuous delivery tool** for Kubernetes that:

- ✅ **Automatically syncs** applications to their desired state
- ✅ **Detects drift** between Git and cluster
- ✅ **Provides UI/CLI/API** for management
- ✅ **Supports multiple sources** (Git, Helm, Kustomize)

---

## 🏗️ **Phase 2: ArgoCD Architecture**

### 🔹 **High-Level Architecture**

```
┌─────────────────────────────────────────────────────────────────┐
│                        ArgoCD Architecture                     │
├─────────────────────────────────────────────────────────────────┤
│  ┌─────────────┐    ┌─────────────┐    ┌─────────────┐       │
│  │   Git Repo  │    │   ArgoCD    │    │ Kubernetes  │       │
│  │             │◄───┤   Server    │───▶│   Cluster   │       │
│  │  (Source)   │    │  (UI/API)   │    │  (Target)   │       │
│  └─────────────┘    └─────────────┘    └─────────────┘       │
│         │                   │                   ▲              │
│         │                   │                   │              │
│         ▼                   ▼                   │              │
│  ┌─────────────┐    ┌─────────────┐            │              │
│  │ Repo Server │    │ Application │            │              │
│  │ (Git Pull)  │    │ Controller  │            │              │
│  └─────────────┘    └─────────────┘            │              │
└─────────────────────────────────────────────────────────────────┘
```

### 🔹 **Core Components**

#### **1. ArgoCD Server (`argocd-server`)**
- **Purpose**: Web UI, API, and authentication
- **Responsibilities**:
  - Serves the web interface
  - Provides REST API
  - Handles authentication/authorization
  - Manages RBAC

#### **2. Application Controller (`argocd-application-controller`)**
- **Purpose**: Sync logic and drift detection
- **Responsibilities**:
  - Compares desired state (Git) vs actual state (K8s)
  - Performs sync operations
  - Manages application lifecycle
  - Detects and reports drift

#### **3. Repo Server (`argocd-repo-server`)**
- **Purpose**: Git repository operations
- **Responsibilities**:
  - Clones Git repositories
  - Generates manifests from Helm/Kustomize
  - Caches repository data
  - Handles different source types

#### **4. Dex Server (`argocd-dex-server`)**
- **Purpose**: Authentication and SSO
- **Responsibilities**:
  - OIDC authentication
  - SSO integration (GitHub, LDAP, etc.)
  - User session management

#### **5. Redis (`argocd-redis`)**
- **Purpose**: Caching and session storage
- **Responsibilities**:
  - Caches application state
  - Stores session data
  - Improves performance

---

## 🎯 **Phase 3: Core Concepts**

### 🔹 **Application**

An **Application** is the primary ArgoCD resource that defines:
- **Source**: Where to get manifests (Git, Helm, etc.)
- **Destination**: Where to deploy (cluster, namespace)
- **Sync Policy**: How to handle changes

#### **Application Structure:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/user/repo
    targetRevision: HEAD
    path: k8s/
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

#### **Application States:**
- **Healthy**: All resources are in desired state
- **Synced**: Git state matches cluster state
- **OutOfSync**: Drift detected between Git and cluster
- **Degraded**: Some resources have issues

### 🔹 **Project**

A **Project** provides logical grouping and access control:
- **Scopes**: Which repositories and clusters to allow
- **Destinations**: Which namespaces to allow
- **Roles**: Who can access what

#### **Project Structure:**
```yaml
apiVersion: argoproj.io/v1alpha1
kind: AppProject
metadata:
  name: my-project
  namespace: argocd
spec:
  description: My application project
  sourceRepos:
  - 'https://github.com/user/repo'
  destinations:
  - namespace: default
    server: https://kubernetes.default.svc
  clusterResourceWhitelist:
  - group: ''
    kind: Namespace
  namespaceResourceWhitelist:
  - group: ''
    kind: ConfigMap
```

### 🔹 **Sync Policies**

#### **Manual Sync**
- User triggers sync via UI/CLI
- Full control over when changes are applied
- Good for production environments

#### **Automated Sync**
```yaml
syncPolicy:
  automated:
    prune: true        # Remove resources not in Git
    selfHeal: true     # Automatically fix drift
    allowEmpty: false  # Don't sync if no resources
```

#### **Sync Options**
- `CreateNamespace=true`: Create namespace if missing
- `PrunePropagationPolicy=foreground`: Safe deletion
- `PruneLast=true`: Prune after sync

---

## 🔐 **Phase 4: Security & RBAC**

### 🔹 **Authentication Methods**

1. **Local Users**: Built-in admin user
2. **SSO Integration**: GitHub, GitLab, LDAP
3. **OIDC**: OpenID Connect providers

### 🔹 **RBAC (Role-Based Access Control)**

#### **Built-in Roles:**
- **admin**: Full access to all projects
- **developer**: Can create/update applications
- **readonly**: Can only view applications

#### **Custom Roles:**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:developer, applications, *, */*, allow
    p, role:developer, repositories, get, *, allow
    p, role:developer, clusters, get, *, allow
```

---

## 🚀 **Phase 5: Advanced Features**

### 🔹 **ApplicationSets**

**Purpose**: Automatically create multiple applications from templates

#### **List Generator Example:**
```yaml
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

### 🔹 **Health Checks**

ArgoCD provides built-in health checks for:
- **Deployments**: Checks replica count and availability
- **Services**: Validates endpoints
- **Pods**: Checks container status
- **Custom Resources**: Configurable health checks

### 🔹 **Notifications**

**Purpose**: Alert users about application changes

#### **Supported Integrations:**
- Slack
- Microsoft Teams
- Email
- Webhooks

#### **Notification Triggers:**
- Application created/deleted
- Sync started/completed
- Health status changed
- Drift detected

---

## 📊 **Phase 6: Monitoring & Observability**

### 🔹 **Metrics**

ArgoCD exposes Prometheus metrics:
- `argocd_app_health_status`: Application health
- `argocd_app_sync_total`: Sync operations
- `argocd_app_sync_duration_seconds`: Sync duration

### 🔹 **Logging**

**Log Levels:**
- `debug`: Detailed debugging information
- `info`: General operational information
- `warn`: Warning messages
- `error`: Error conditions

### 🔹 **Audit Trail**

ArgoCD maintains audit logs for:
- Application changes
- Sync operations
- Authentication events
- RBAC decisions

---

## 🔧 **Phase 7: Best Practices**

### 🔹 **Repository Structure**
```
my-gitops-repo/
├── applications/
│   ├── app1/
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   │   └── configmap.yaml
│   └── app2/
├── infrastructure/
│   ├── namespaces/
│   ├── network-policies/
│   └── storage-classes/
└── argocd/
    └── applications/
```

### 🔹 **Security Best Practices**

1. **Use Projects**: Limit access with AppProjects
2. **RBAC**: Implement proper role assignments
3. **SSO**: Use external authentication
4. **Network Policies**: Restrict pod communication
5. **Secrets Management**: Use external secrets operators

### 🔹 **Performance Optimization**

1. **Repository Caching**: Configure repo server caching
2. **Resource Limits**: Set appropriate CPU/memory limits
3. **HA Setup**: Run multiple replicas for critical components
4. **Monitoring**: Set up alerts for component health

---

## 📚 **Summary**

This theoretical foundation covers:
- ✅ GitOps principles and ArgoCD's role
- ✅ Complete architecture understanding
- ✅ Core concepts (Applications, Projects, Sync)
- ✅ Security and RBAC fundamentals
- ✅ Advanced features (ApplicationSets, Health Checks)
- ✅ Monitoring and best practices

**Next Steps**: Move to practical examples and hands-on labs to apply these concepts. 