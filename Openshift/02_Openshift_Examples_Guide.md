# OpenShift Examples Guide

## Table of Contents
1. [Basic OpenShift Operations](#basic-openshift-operations)
2. [Application Deployment Examples](#application-deployment-examples)
3. [Build and Image Management](#build-and-image-management)
4. [Networking and Routing](#networking-and-routing)
5. [Security Examples](#security-examples)
6. [Storage Examples](#storage-examples)
7. [CI/CD Examples](#cicd-examples)
8. [Monitoring Examples](#monitoring-examples)
9. [Advanced Features Examples](#advanced-features-examples)

---

## Basic OpenShift Operations

### Example 1: Project Creation and Management

**Purpose**: Learn how to create and manage OpenShift projects, which are the fundamental organizational units that provide isolation, security, and resource management.

**Expected Outcomes**: 
- Understand the difference between OpenShift projects and Kubernetes namespaces
- Learn to create, view, and manage projects using both CLI and YAML
- Configure resource quotas to control project resource consumption
- Apply best practices for project organization and naming

#### 1.1 Create a New Project

```bash
# Create a new project
oc new-project my-app

# Verify project creation
oc get projects

# Switch to the project
oc project my-app
```

#### 1.2 Project Configuration

```yaml
# Project YAML definition
apiVersion: project.openshift.io/v1
kind: Project
metadata:
  name: my-app
  annotations:
    openshift.io/display-name: "My Application"
    openshift.io/description: "Sample application project"
```

#### 1.3 Project Quotas

```yaml
# ResourceQuota for project limits
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
spec:
  hard:
    requests.cpu: "4"
    requests.memory: 8Gi
    limits.cpu: "8"
    limits.memory: 16Gi
    persistentvolumeclaims: "10"
```

### Example 2: Basic Pod Operations

**Purpose**: Master the fundamentals of pod management in OpenShift, including creation, configuration, and lifecycle management.

**Expected Outcomes**:
- Create and manage individual pods using YAML definitions
- Configure environment variables and secrets in pods
- Mount volumes and persistent storage to pods
- Monitor pod status, logs, and resource usage
- Understand pod lifecycle and troubleshooting techniques

#### 2.1 Create a Simple Pod

```yaml
# Simple pod definition
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  labels:
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
```

#### 2.2 Pod with Environment Variables

```yaml
# Pod with environment variables
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: my-app:latest
    env:
    - name: DATABASE_URL
      value: "postgresql://user:pass@db:5432/mydb"
    - name: API_KEY
      valueFrom:
        secretKeyRef:
          name: app-secret
          key: api-key
```

#### 2.3 Pod with Volume Mounts

```yaml
# Pod with persistent volume
apiVersion: v1
kind: Pod
metadata:
  name: app-with-storage
spec:
  containers:
  - name: app
    image: my-app:latest
    volumeMounts:
    - name: app-storage
      mountPath: /app/data
  volumes:
  - name: app-storage
    persistentVolumeClaim:
      claimName: app-pvc
```

### Example 3: Service Creation

**Purpose**: Learn to create and configure Kubernetes services in OpenShift for internal networking and service discovery.

**Expected Outcomes**:
- Understand different service types (ClusterIP, NodePort, LoadBalancer)
- Configure service selectors and endpoints
- Implement service discovery and load balancing
- Test service connectivity and troubleshooting
- Apply networking best practices for microservices

#### 3.1 ClusterIP Service

```yaml
# Internal service
apiVersion: v1
kind: Service
metadata:
  name: app-service
spec:
  selector:
    app: my-app
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
  type: ClusterIP
```

#### 3.2 NodePort Service

```yaml
# External access service
apiVersion: v1
kind: Service
metadata:
  name: app-nodeport
spec:
  selector:
    app: my-app
  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30080
  type: NodePort
```

---

## Application Deployment Examples

### Example 4: DeploymentConfig

**Purpose**: Master OpenShift's DeploymentConfig resource for managing application deployments with advanced features like triggers, rollbacks, and deployment strategies.

**Expected Outcomes**:
- Create and manage DeploymentConfigs for application lifecycle
- Implement different deployment strategies (Rolling, Recreate, Custom)
- Configure health checks and resource limits
- Set up automatic triggers for deployments
- Perform rollbacks and deployment troubleshooting
- Scale applications horizontally and vertically

#### 4.1 Basic DeploymentConfig

```yaml
# DeploymentConfig for application
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app
        image: my-app:latest
        ports:
        - containerPort: 8080
  triggers:
  - type: ConfigChange
  - type: ImageChange
    imageChangeParams:
      automatic: true
      containerNames:
      - my-app
      from:
        kind: ImageStreamTag
        name: my-app:latest
```

#### 4.2 DeploymentConfig with Rolling Strategy

```yaml
# Rolling deployment strategy
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app
spec:
  replicas: 5
  strategy:
    type: Rolling
    rollingParams:
      updatePeriodSeconds: 1
      intervalSeconds: 1
      timeoutSeconds: 600
      maxUnavailable: 25%
      maxSurge: 25%
  # ... rest of configuration
```

#### 4.3 DeploymentConfig with Health Checks

```yaml
# DeploymentConfig with health checks
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app
spec:
  replicas: 3
  template:
    spec:
      containers:
      - name: my-app
        image: my-app:latest
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
```

### Example 5: Using oc new-app

**Purpose**: Learn to use OpenShift's `oc new-app` command for rapid application deployment and prototyping with automatic resource creation.

**Expected Outcomes**:
- Deploy applications directly from Git repositories
- Use different S2I builders for various programming languages
- Configure environment variables and build parameters
- Automate the creation of BuildConfig, ImageStream, DeploymentConfig, and Service
- Apply templates and custom configurations
- Understand the automation benefits of `oc new-app`

#### 5.1 Deploy from Git Repository

```bash
# Deploy Node.js application from Git
oc new-app nodejs~https://github.com/user/my-nodejs-app

# Deploy Python application
oc new-app python~https://github.com/user/my-python-app

# Deploy Java application
oc new-app java~https://github.com/user/my-java-app
```

#### 5.2 Deploy with Custom Parameters

```bash
# Deploy with environment variables
oc new-app nodejs~https://github.com/user/my-app \
  --env DATABASE_URL=postgresql://user:pass@db:5432/mydb \
  --env API_KEY=secret-key

# Deploy with custom build strategy
oc new-app --strategy=docker \
  https://github.com/user/my-app
```

#### 5.3 Deploy from Template

```bash
# Deploy from OpenShift template
oc new-app --template=postgresql-persistent \
  -p DATABASE_SERVICE_NAME=my-db \
  -p POSTGRESQL_DATABASE=myapp \
  -p POSTGRESQL_USER=myuser \
  -p POSTGRESQL_PASSWORD=mypassword

# Deploy from custom template
oc new-app --template=my-app-template \
  -p APP_NAME=my-app \
  -p APP_VERSION=v1.0
```

---

## Build and Image Management

### Example 6: BuildConfig Examples

**Purpose**: Master OpenShift's build system for creating container images from source code, Dockerfiles, and custom build processes.

**Expected Outcomes**:
- Create and manage BuildConfigs for different build strategies
- Use Source-to-Image (S2I) for automated container creation
- Implement Docker-based builds with custom Dockerfiles
- Configure build triggers and webhooks
- Manage build logs and troubleshooting
- Integrate builds with CI/CD pipelines
- Understand image registry integration and tagging

#### 6.1 Source-to-Image (S2I) BuildConfig

```yaml
# S2I BuildConfig for Node.js
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: my-app
spec:
  output:
    to:
      kind: ImageStreamTag
      name: my-app:latest
  source:
    git:
      uri: https://github.com/user/my-app
      ref: main
  strategy:
    type: Source
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: nodejs:16
  triggers:
  - type: ConfigChange
  - type: ImageChange
```

#### 6.2 Docker BuildConfig

```yaml
# Docker BuildConfig
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: my-app
spec:
  output:
    to:
      kind: ImageStreamTag
      name: my-app:latest
  source:
    git:
      uri: https://github.com/user/my-app
    contextDir: app
  strategy:
    type: Docker
    dockerStrategy:
      dockerfilePath: Dockerfile
  triggers:
  - type: ConfigChange
```

#### 6.3 Pipeline BuildConfig

```yaml
# Tekton Pipeline BuildConfig
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: my-app
spec:
  output:
    to:
      kind: ImageStreamTag
      name: my-app:latest
  source:
    git:
      uri: https://github.com/user/my-app
  strategy:
    type: Pipeline
    pipelineStrategy:
      pipelineRef:
        name: build-and-test
  triggers:
  - type: ConfigChange
```

### Example 7: ImageStream Management

**Purpose**: Learn to manage container images in OpenShift using ImageStreams for versioning, tagging, and automated deployments.

**Expected Outcomes**:
- Create and manage ImageStreams for image versioning
- Import images from external registries
- Configure image tags and triggers
- Set up automatic deployments based on image changes
- Implement image security scanning and policies
- Understand the relationship between ImageStreams and deployments
- Apply best practices for image management and organization

#### 7.1 Basic ImageStream

```yaml
# ImageStream for application
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: my-app
spec:
  tags:
  - name: latest
    from:
      kind: ImageStreamTag
      name: my-app:latest
  - name: v1.0
    from:
      kind: ImageStreamTag
      name: my-app:v1.0
```

#### 7.2 ImageStream with External Registry

```yaml
# ImageStream from external registry
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: nginx
spec:
  tags:
  - name: latest
    from:
      kind: DockerImage
      name: nginx:latest
    importPolicy:
      scheduled: true
```

#### 7.3 ImageStream Triggers

```yaml
# ImageStream with deployment triggers
apiVersion: image.openshift.io/v1
kind: ImageStream
metadata:
  name: my-app
spec:
  tags:
  - name: latest
    from:
      kind: ImageStreamTag
      name: my-app:latest
    annotations:
      openshift.io/imported-from: my-app:latest
```

---

## Networking and Routing

### Example 8: Route Configuration

**Purpose**: Master OpenShift's routing system for exposing applications externally with advanced features like TLS, path-based routing, and load balancing.

**Expected Outcomes**:
- Create and configure Routes for external access
- Implement TLS termination and certificate management
- Configure path-based routing for microservices
- Set up custom hostnames and domains
- Implement route security and access controls
- Troubleshoot routing issues and connectivity
- Apply networking best practices for production environments

#### 8.1 Basic HTTP Route

```yaml
# Simple HTTP route
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-route
spec:
  host: my-app.example.com
  to:
    kind: Service
    name: my-app-service
  port:
    targetPort: 8080
```

#### 8.2 HTTPS Route with TLS

```yaml
# HTTPS route with TLS termination
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-route
spec:
  host: my-app.example.com
  to:
    kind: Service
    name: my-app-service
  port:
    targetPort: 8080
  tls:
    termination: edge
    certificate: |
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    key: |
      -----BEGIN PRIVATE KEY-----
      ...
      -----END PRIVATE KEY-----
```

#### 8.3 Route with Path-based Routing

```yaml
# Route with path-based routing
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: my-app-api
spec:
  host: my-app.example.com
  path: /api
  to:
    kind: Service
    name: my-app-api-service
  port:
    targetPort: 8080
```

### Example 9: Network Policies

**Purpose**: Implement network security in OpenShift using NetworkPolicies to control pod-to-pod communication and enforce security boundaries.

**Expected Outcomes**:
- Create NetworkPolicies to control traffic flow
- Implement default deny policies for security
- Configure allow rules for specific services
- Set up multi-tier network security architectures
- Test and validate network policy enforcement
- Troubleshoot network connectivity issues
- Apply network security best practices for microservices

#### 9.1 Deny All Traffic

```yaml
# NetworkPolicy to deny all traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: deny-all
spec:
  podSelector: {}
  policyTypes:
  - Ingress
  - Egress
```

#### 9.2 Allow Specific Traffic

```yaml
# NetworkPolicy to allow specific traffic
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-app-traffic
spec:
  podSelector:
    matchLabels:
      app: my-app
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
    ports:
    - protocol: TCP
      port: 8080
```

#### 9.3 Multi-tier Network Policy

```yaml
# NetworkPolicy for multi-tier architecture
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: database-policy
spec:
  podSelector:
    matchLabels:
      app: database
  policyTypes:
  - Ingress
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: backend
    ports:
    - protocol: TCP
      port: 5432
```

---

## Security Examples

### Example 10: Security Context Constraints

**Purpose**: Master OpenShift's Security Context Constraints (SCCs) to enforce pod-level security policies and protect the cluster from malicious containers.

**Expected Outcomes**:
- Understand the different SCC types and their security implications
- Create custom SCCs for specific application requirements
- Apply SCCs to service accounts and pods
- Configure pod security contexts and capabilities
- Implement least-privilege security principles
- Troubleshoot SCC-related deployment issues
- Apply security best practices for containerized applications

#### 10.1 Restricted SCC

```yaml
# Restricted SCC for applications
apiVersion: security.openshift.io/v1
kind: SecurityContextConstraints
metadata:
  name: restricted
priority: 10
readOnlyRootFilesystem: true
runAsUser:
  type: MustRunAsRange
seLinuxContext:
  type: MustRunAs
supplementalGroups:
  type: RunAsAny
volumes:
- 'configMap'
- 'downwardAPI'
- 'emptyDir'
- 'projected'
- 'secret'
```

#### 10.2 Custom SCC

```yaml
# Custom SCC for specific needs
apiVersion: security.openshift.io/v1
kind: SecurityContextConstraints
metadata:
  name: my-app-scc
priority: 20
allowHostDirVolumePlugin: false
allowHostIPC: false
allowHostNetwork: false
allowHostPID: false
allowHostPorts: false
allowPrivilegeEscalation: false
allowPrivilegedContainer: false
runAsUser:
  type: MustRunAsNonRoot
seLinuxContext:
  type: MustRunAs
supplementalGroups:
  type: RunAsAny
volumes:
- 'configMap'
- 'downwardAPI'
- 'emptyDir'
- 'projected'
- 'secret'
```

### Example 11: RBAC Configuration

**Purpose**: Implement Role-Based Access Control (RBAC) in OpenShift to manage user permissions and secure cluster access.

**Expected Outcomes**:
- Create Roles and ClusterRoles for permission management
- Configure RoleBindings and ClusterRoleBindings
- Implement least-privilege access principles
- Manage user and service account permissions
- Test and validate RBAC configurations
- Troubleshoot permission-related issues
- Apply security best practices for access control

#### 11.1 Role and RoleBinding

```yaml
# Role for developers
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: developer
rules:
- apiGroups: [""]
  resources: ["pods", "services", "routes"]
  verbs: ["get", "list", "create", "update", "delete"]
- apiGroups: ["apps.openshift.io"]
  resources: ["deploymentconfigs"]
  verbs: ["get", "list", "create", "update", "delete"]
---
# RoleBinding to assign role
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
subjects:
- kind: User
  name: developer@example.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: developer
  apiGroup: rbac.authorization.k8s.io
```

#### 11.2 ClusterRole and ClusterRoleBinding

```yaml
# ClusterRole for cluster-wide access
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: cluster-admin
rules:
- apiGroups: ["*"]
  resources: ["*"]
  verbs: ["*"]
---
# ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-binding
subjects:
- kind: User
  name: admin@example.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```

### Example 12: Service Accounts

**Purpose**: Learn to create and manage ServiceAccounts in OpenShift to provide identity and permissions for applications and pods.

**Expected Outcomes**:
- Create ServiceAccounts for application identity
- Configure ServiceAccount permissions and tokens
- Mount ServiceAccount tokens in pods
- Implement pod identity and authentication
- Configure image pull secrets for ServiceAccounts
- Troubleshoot ServiceAccount-related issues
- Apply security best practices for pod identity

#### 12.1 Service Account for Application

```yaml
# ServiceAccount for application
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-app-sa
  annotations:
    openshift.io/oauth-redirectreference: '{"kind":"OAuthRedirectReference","apiVersion":"v1","reference":{"kind":"Route","name":"my-app"}}'
secrets:
- name: my-app-sa-token-abc123
imagePullSecrets:
- name: my-app-sa-dockercfg-abc123
```

#### 12.2 Pod with Service Account

```yaml
# Pod using ServiceAccount
apiVersion: v1
kind: Pod
metadata:
  name: my-app-pod
spec:
  serviceAccountName: my-app-sa
  containers:
  - name: my-app
    image: my-app:latest
    env:
    - name: KUBERNETES_SERVICE_HOST
      value: kubernetes.default.svc
    - name: KUBERNETES_SERVICE_PORT
      value: "443"
```

---

## Storage Examples

### Example 13: Persistent Volume Claims

**Purpose**: Learn to configure persistent storage in OpenShift using PersistentVolumeClaims (PVCs) to provide reliable, persistent data storage for applications.

**Expected Outcomes**:
- Create and manage PVCs for application data storage
- Understand different access modes (ReadWriteOnce, ReadOnlyMany, ReadWriteMany)
- Configure storage classes and capacity requirements
- Mount persistent volumes to pods and containers
- Implement data persistence across pod restarts
- Troubleshoot storage-related issues
- Apply storage best practices for stateful applications

#### 13.1 Basic PVC

```yaml
# PersistentVolumeClaim for application
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-storage
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: fast
```

#### 13.2 PVC with Specific Storage Class

```yaml
# PVC with specific storage class
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: database-storage
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 100Gi
  storageClassName: ssd
```

### Example 14: Storage Classes

**Purpose**: Master OpenShift's StorageClass system to define different storage types and provision persistent volumes automatically based on application requirements.

**Expected Outcomes**:
- Create and configure StorageClasses for different storage types
- Understand storage provisioners and their capabilities
- Configure storage parameters for performance optimization
- Implement dynamic volume provisioning
- Set up storage policies and reclaim strategies
- Optimize storage costs and performance
- Apply storage class best practices for different workloads

#### 14.1 Fast Storage Class

```yaml
# StorageClass for fast storage
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2
  iops: "3000"
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: true
```

#### 14.2 Slow Storage Class

```yaml
# StorageClass for cost-effective storage
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: slow
provisioner: kubernetes.io/aws-ebs
parameters:
  type: st1
reclaimPolicy: Delete
volumeBindingMode: Immediate
allowVolumeExpansion: false
```

---

## CI/CD Examples

### Example 15: OpenShift Pipelines (Tekton)

**Purpose**: Learn to implement CI/CD pipelines in OpenShift using Tekton for automated build, test, and deployment processes.

**Expected Outcomes**:
- Create and manage Tekton pipelines for application lifecycle
- Implement automated build and deployment workflows
- Configure pipeline tasks and workspaces
- Set up pipeline triggers and webhooks
- Monitor pipeline execution and troubleshoot failures
- Integrate pipelines with external tools and services
- Apply CI/CD best practices for OpenShift applications

#### 15.1 Simple Pipeline

```yaml
# Tekton Pipeline for build and deploy
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: build-and-deploy
spec:
  workspaces:
  - name: shared-workspace
  params:
  - name: git-url
    type: string
  - name: git-revision
    type: string
  tasks:
  - name: fetch-repository
    taskRef:
      name: git-clone
    workspaces:
    - name: output
      workspace: shared-workspace
    params:
    - name: url
      value: $(params.git-url)
    - name: revision
      value: $(params.git-revision)
  - name: build-image
    runAfter: ["fetch-repository"]
    taskRef:
      name: buildah
    workspaces:
    - name: source
      workspace: shared-workspace
    params:
    - name: IMAGE
      value: image-registry.openshift-image-registry.svc:5000/my-app/my-app:latest
```

#### 15.2 PipelineRun

```yaml
# PipelineRun to execute pipeline
apiVersion: tekton.dev/v1beta1
kind: PipelineRun
metadata:
  name: build-and-deploy-run
spec:
  pipelineRef:
    name: build-and-deploy
  workspaces:
  - name: shared-workspace
    volumeClaimTemplate:
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
  params:
  - name: git-url
    value: https://github.com/user/my-app
  - name: git-revision
    value: main
```

### Example 16: ArgoCD Integration

**Purpose**: Implement GitOps-based continuous deployment in OpenShift using ArgoCD for declarative application management and automated synchronization.

**Expected Outcomes**:
- Deploy and configure ArgoCD in OpenShift
- Create ArgoCD applications for GitOps workflows
- Implement automated application synchronization
- Configure multi-environment deployments
- Set up application health monitoring and rollbacks
- Integrate ArgoCD with OpenShift security and RBAC
- Apply GitOps best practices for OpenShift applications

#### 16.1 ArgoCD Application

```yaml
# ArgoCD Application for OpenShift
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/user/my-app-manifests
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: my-app
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - CreateNamespace=true
  revisionHistoryLimit: 10
```

#### 16.2 ArgoCD ApplicationSet

```yaml
# ArgoCD ApplicationSet for multiple environments
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: my-app
  namespace: argocd
spec:
  generators:
  - list:
      elements:
      - env: dev
        namespace: my-app-dev
      - env: staging
        namespace: my-app-staging
      - env: prod
        namespace: my-app-prod
  template:
    metadata:
      name: '{{env}}-my-app'
    spec:
      project: default
      source:
        repoURL: https://github.com/user/my-app-manifests
        targetRevision: HEAD
        path: k8s/{{env}}
      destination:
        server: https://kubernetes.default.svc
        namespace: '{{namespace}}'
      syncPolicy:
        automated:
          prune: true
          selfHeal: true
```

---

## Monitoring Examples

### Example 17: Prometheus Monitoring

**Purpose**: Implement comprehensive application monitoring in OpenShift using Prometheus for metrics collection, alerting, and observability.

**Expected Outcomes**:
- Deploy and configure Prometheus monitoring stack
- Create ServiceMonitors for application metrics collection
- Configure PrometheusRules for alerting and monitoring
- Set up custom metrics and dashboards
- Implement monitoring best practices for OpenShift applications
- Troubleshoot monitoring and alerting issues
- Apply observability patterns for microservices

#### 17.1 ServiceMonitor

```yaml
# ServiceMonitor for application monitoring
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: my-app-monitor
  namespace: my-app
spec:
  selector:
    matchLabels:
      app: my-app
  endpoints:
  - port: metrics
    interval: 30s
    path: /metrics
```

#### 17.2 PrometheusRule

```yaml
# PrometheusRule for alerting
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: my-app-rules
  namespace: my-app
spec:
  groups:
  - name: my-app
    rules:
    - alert: HighErrorRate
      expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High error rate detected"
        description: "Error rate is {{ $value }} errors per second"
```

### Example 18: Grafana Dashboards

**Purpose**: Create and manage Grafana dashboards in OpenShift for visualizing application metrics and monitoring data.

**Expected Outcomes**:
- Deploy and configure Grafana in OpenShift
- Create custom dashboards for application monitoring
- Configure data sources and visualization panels
- Implement dashboard templating and variables
- Set up dashboard sharing and access control
- Apply visualization best practices for monitoring
- Integrate Grafana with Prometheus and other data sources

#### 18.1 GrafanaDashboard

```yaml
# GrafanaDashboard for application metrics
apiVersion: integreatly.org/v1alpha1
kind: GrafanaDashboard
metadata:
  name: my-app-dashboard
  namespace: my-app
spec:
  name: my-app-dashboard
  json: |
    {
      "dashboard": {
        "title": "My App Dashboard",
        "panels": [
          {
            "title": "Request Rate",
            "type": "graph",
            "targets": [
              {
                "expr": "rate(http_requests_total[5m])",
                "legendFormat": "{{method}} {{status}}"
              }
            ]
          }
        ]
      }
    }
```

---

## Advanced Features Examples

### Example 19: Service Mesh (Istio)

**Purpose**: Implement service mesh capabilities in OpenShift using Istio for advanced traffic management, security, and observability.

**Expected Outcomes**:
- Deploy and configure Istio service mesh in OpenShift
- Create VirtualServices for traffic routing and load balancing
- Configure DestinationRules for traffic policies
- Implement service-to-service security with mTLS
- Set up distributed tracing and observability
- Apply service mesh patterns for microservices
- Troubleshoot service mesh configuration and connectivity

#### 19.1 VirtualService

```yaml
# Istio VirtualService for traffic routing
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: my-app
spec:
  hosts:
  - my-app.example.com
  gateways:
  - my-app-gateway
  http:
  - match:
    - uri:
        prefix: /api/v1
    route:
    - destination:
        host: my-app-service
        port:
          number: 8080
      weight: 80
    - destination:
        host: my-app-service-v2
        port:
          number: 8080
      weight: 20
```

#### 19.2 DestinationRule

```yaml
# Istio DestinationRule for traffic policies
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: my-app
spec:
  host: my-app-service
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  trafficPolicy:
    loadBalancer:
      simple: ROUND_ROBIN
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 1024
        maxRequestsPerConnection: 10
```

### Example 20: Operators

**Purpose**: Learn to use and create Kubernetes operators in OpenShift for automated application lifecycle management and complex stateful services.

**Expected Outcomes**:
- Deploy and configure operators from OperatorHub
- Create custom operators for application management
- Implement operator patterns for stateful applications
- Configure operator-based database deployments
- Set up operator monitoring and health checks
- Apply operator best practices for OpenShift
- Understand operator development and customization

#### 20.1 PostgreSQL Operator

```yaml
# PostgreSQL cluster using operator
apiVersion: postgresql.cnpg.io/v1
kind: Cluster
metadata:
  name: postgresql-cluster
spec:
  instances: 3
  bootstrap:
    initdb:
      database: myapp
      owner: myuser
  storage:
    size: 10Gi
  monitoring:
    enabled: true
```

#### 20.2 Custom Operator

```yaml
# Custom resource for application operator
apiVersion: myapp.example.com/v1alpha1
kind: MyApp
metadata:
  name: my-app-instance
spec:
  replicas: 3
  image: my-app:latest
  database:
    host: postgresql-cluster-rw
    port: 5432
    name: myapp
  monitoring:
    enabled: true
    prometheus:
      enabled: true
```

### Example 21: Multi-cluster Management

**Purpose**: Implement multi-cluster management strategies in OpenShift for distributed applications, disaster recovery, and workload distribution.

**Expected Outcomes**:
- Deploy applications across multiple OpenShift clusters
- Configure cluster federation and workload distribution
- Implement disaster recovery and backup strategies
- Set up cross-cluster monitoring and observability
- Configure multi-cluster security and access control
- Apply multi-cluster deployment patterns
- Troubleshoot cross-cluster connectivity and synchronization

#### 21.1 Application Distribution

```yaml
# Application distributed across clusters
apiVersion: apps.open-cluster-management.io/v1
kind: PlacementRule
metadata:
  name: my-app-placement
spec:
  clusterConditions:
  - type: Available
    status: "True"
  clusterSelector:
    matchLabels:
      environment: production
---
apiVersion: apps.open-cluster-management.io/v1
kind: Subscription
metadata:
  name: my-app-subscription
spec:
  channel: my-app-channel
  placement:
    placementRef:
      name: my-app-placement
  packageFilter:
    version: "1.0.0"
```

---

## Summary

This examples guide provides practical implementations for:

### Key Learning Points:
1. **Basic Operations**: Project management, pod creation, service configuration
2. **Application Deployment**: DeploymentConfig, oc new-app, templates
3. **Build Management**: S2I, Docker builds, ImageStreams
4. **Networking**: Routes, NetworkPolicies, service mesh
5. **Security**: SCCs, RBAC, ServiceAccounts
6. **Storage**: PVCs, StorageClasses, volume management
7. **CI/CD**: Tekton pipelines, ArgoCD integration
8. **Monitoring**: Prometheus, Grafana, alerting
9. **Advanced Features**: Service mesh, operators, multi-cluster

### Best Practices Demonstrated:
- **Security First**: Proper RBAC and SCC configuration
- **Resource Management**: Appropriate resource limits and requests
- **Monitoring**: Comprehensive application monitoring
- **Automation**: CI/CD pipelines and GitOps
- **Scalability**: Horizontal and vertical scaling strategies

These examples provide a solid foundation for implementing OpenShift solutions in production environments. 