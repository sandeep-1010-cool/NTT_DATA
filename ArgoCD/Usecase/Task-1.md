# Task 1: ArgoCD Installation & Initial Setup

## 🎯 **Objective**
Install and configure ArgoCD in a production-ready manner using Helm with enterprise-grade configurations.

---

## 📋 **Prerequisites**

### **System Requirements**
- Kubernetes cluster (v1.19+)
- Helm v3.8+
- kubectl configured
- Git repository access
- Storage class for persistent volumes
- Load balancer or ingress controller

### **Required Tools**
```bash
# Install Helm (if not already installed)
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash

# Verify installations
kubectl version --client
helm version
```

---

## 🚀 **Step-by-Step Installation**

### **Step 1: Add ArgoCD Helm Repository**
```bash
# Add the official ArgoCD Helm repository
helm repo add argo https://argoproj.github.io/argo-helm

# Update the repository
helm repo update

# Verify the repository
helm search repo argo/argo-cd
```

### **Step 2: Create Namespace and RBAC**
```bash
# Create dedicated namespace for ArgoCD
kubectl create namespace argocd

# Create namespace for ArgoCD applications
kubectl create namespace argocd-apps
```

### **Step 3: Create Production Values File**
Create a file named `argocd-production-values.yaml`:

```yaml
# ArgoCD Production Configuration
global:
  image:
    repository: quay.io/argoproj/argocd
    tag: v2.8.4  # Use latest stable version

# Server Configuration
server:
  # High availability configuration
  replicas: 3
  
  # Resource limits for production
  resources:
    limits:
      cpu: 1000m
      memory: 2Gi
    requests:
      cpu: 500m
      memory: 1Gi
  
  # Security configurations
  securityContext:
    runAsNonRoot: true
    runAsUser: 999
  
  # Service configuration
  service:
    type: LoadBalancer  # or ClusterIP with ingress
    port: 80
    targetPort: 8080
  
  # Ingress configuration (if using ingress)
  ingress:
    enabled: true
    className: nginx
    annotations:
      kubernetes.io/ingress.class: nginx
      cert-manager.io/cluster-issuer: letsencrypt-prod
      nginx.ingress.kubernetes.io/ssl-redirect: "true"
      nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    hosts:
      - host: argocd.your-domain.com
        paths:
          - path: /
            pathType: Prefix
    tls:
      - secretName: argocd-tls
        hosts:
          - argocd.your-domain.com

# Application Controller Configuration
controller:
  replicas: 2
  
  resources:
    limits:
      cpu: 1000m
      memory: 1Gi
    requests:
      cpu: 500m
      memory: 512Mi
  
  securityContext:
    runAsNonRoot: true
    runAsUser: 999

# Repository Server Configuration
repoServer:
  replicas: 2
  
  resources:
    limits:
      cpu: 1000m
      memory: 1Gi
    requests:
      cpu: 500m
      memory: 512Mi
  
  securityContext:
    runAsNonRoot: true
    runAsUser: 999

# Redis Configuration (for HA)
redis:
  enabled: true
  auth:
    enabled: true
    existingSecret: argocd-redis-auth
  
  master:
    persistence:
      enabled: true
      size: 8Gi
      storageClass: "fast-ssd"
  
  replica:
    enabled: true
    replicas: 2
    persistence:
      enabled: true
      size: 8Gi
      storageClass: "fast-ssd"

# HA Configuration
ha:
  enabled: true

# Security Configuration
rbac:
  create: true
  pspEnabled: true

# Network Policies
networkPolicy:
  enabled: true
  ingress:
    enabled: true
    namespaceSelector: {}
    podSelector: {}
  egress:
    enabled: true

# Monitoring Configuration
monitoring:
  enabled: true
  serviceMonitor:
    enabled: true
    interval: 30s
    scrapeTimeout: 10s

# Backup Configuration
backup:
  enabled: true
  schedule: "0 2 * * *"  # Daily at 2 AM
  retention: 30  # Keep backups for 30 days

# High Availability
autoscaling:
  enabled: true
  minReplicas: 2
  maxReplicas: 10
  targetCPUUtilizationPercentage: 80
  targetMemoryUtilizationPercentage: 80

# Security Context
securityContext:
  runAsNonRoot: true
  runAsUser: 999
  fsGroup: 999

# Pod Security Standards
podSecurityContext:
  runAsNonRoot: true
  runAsUser: 999
  fsGroup: 999

# Service Account
serviceAccount:
  create: true
  name: argocd-application-controller
  annotations: {}

# TLS Configuration
tls:
  enabled: true
  secretName: argocd-tls

# Metrics Configuration
metrics:
  enabled: true
  service:
    annotations:
      prometheus.io/scrape: "true"
      prometheus.io/port: "8082"

# Logging Configuration
logging:
  level: info
  format: json
```

### **Step 4: Create Secrets for Production**
```bash
# Create Redis authentication secret
kubectl create secret generic argocd-redis-auth \
  --from-literal=redis-password=$(openssl rand -base64 32) \
  --namespace argocd

# Create TLS secret (if using custom certificates)
kubectl create secret tls argocd-tls \
  --cert=path/to/cert.pem \
  --key=path/to/key.pem \
  --namespace argocd
```

### **Step 5: Install ArgoCD with Production Config**
```bash
# Install ArgoCD with production values
helm install argocd argo/argo-cd \
  --namespace argocd \
  --values argocd-production-values.yaml \
  --wait \
  --timeout 10m

# Verify the installation
kubectl get pods -n argocd
kubectl get services -n argocd
```

### **Step 6: Get Initial Admin Password**
```bash
# Get the initial admin password
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# Or use this command for better formatting
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
```

### **Step 7: Access ArgoCD UI**
```bash
# Port forward to access the UI locally
kubectl port-forward svc/argocd-server 8080:443 -n argocd

# Or get the external IP/domain
kubectl get svc argocd-server -n argocd
```

**Access the UI at**: `https://localhost:8080` (port-forward) or your domain

**Default credentials**:
- Username: `admin`
- Password: (from Step 6)

---

## 🔧 **Post-Installation Configuration**

### **Step 8: Install ArgoCD CLI**
```bash
# Install ArgoCD CLI
curl -sSL -o argocd-linux-amd64 https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-amd64
sudo install -m 555 argocd-linux-amd64 /usr/local/bin/argocd
rm argocd-linux-amd64

# For macOS
brew install argocd

# For Windows
choco install argocd
```

### **Step 9: Login to ArgoCD**
```bash
# Login via CLI
argocd login localhost:8080 --username admin --password <password-from-step-6>

# Or login to your domain
argocd login argocd.your-domain.com --username admin --password <password-from-step-6>
```

### **Step 10: Change Default Admin Password**
```bash
# Change the default admin password
argocd account update-password
```

### **Step 11: Configure External Authentication (Optional)**
```yaml
# Add to your values file or apply separately
dex:
  enabled: true
  config: |
    connectors:
      - type: github
        id: github
        name: GitHub
        config:
          clientID: your-github-client-id
          clientSecret: your-github-client-secret
          orgs:
            - name: your-org
```

---

## 🔒 **Security Hardening**

### **Step 12: Configure RBAC**
```yaml
# Create RBAC configuration
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-rbac-cm
  namespace: argocd
data:
  policy.default: role:readonly
  policy.csv: |
    p, role:org-admin, applications, *, */*, allow
    p, role:org-admin, clusters, get, *, allow
    p, role:org-admin, repositories, get, *, allow
    p, role:org-admin, repositories, create, *, allow
    p, role:org-admin, repositories, update, *, allow
    p, role:org-admin, repositories, delete, *, allow
    p, role:org-admin, certificates, get, *, allow
    p, role:org-admin, certificates, create, *, allow
    p, role:org-admin, certificates, update, *, allow
    p, role:org-admin, certificates, delete, *, allow
    p, role:org-admin, accounts, get, *, allow
    p, role:org-admin, accounts, update, *, allow
    p, role:org-admin, gpgkeys, get, *, allow
    p, role:org-admin, gpgkeys, create, *, allow
    p, role:org-admin, gpgkeys, delete, *, allow
    g, admin, role:org-admin
```

### **Step 13: Configure Network Policies**
```yaml
# Network policy for ArgoCD server
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: argocd-server-network-policy
  namespace: argocd
spec:
  podSelector:
    matchLabels:
      app.kubernetes.io/name: argocd-server
  policyTypes:
    - Ingress
    - Egress
  ingress:
    - from:
        - namespaceSelector:
            matchLabels:
              name: argocd-apps
      ports:
        - protocol: TCP
          port: 8080
  egress:
    - to:
        - namespaceSelector:
            matchLabels:
              name: argocd-apps
      ports:
        - protocol: TCP
          port: 8080
```

---

## 📊 **Monitoring Setup**

### **Step 14: Configure Prometheus Monitoring**
```yaml
# ServiceMonitor for ArgoCD
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: argocd-server
  namespace: argocd
  labels:
    release: prometheus
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: argocd-server
  endpoints:
    - port: http
      interval: 30s
      path: /metrics
```

### **Step 15: Create Grafana Dashboard**
```bash
# Create a basic monitoring dashboard
kubectl apply -f - <<EOF
apiVersion: v1
kind: ConfigMap
metadata:
  name: argocd-dashboard
  namespace: argocd
data:
  dashboard.json: |
    {
      "dashboard": {
        "title": "ArgoCD Dashboard",
        "panels": [
          {
            "title": "Application Sync Status",
            "type": "stat",
            "targets": [
              {
                "expr": "argocd_app_sync_total"
              }
            ]
          }
        ]
      }
    }
EOF
```

---

## 🔄 **Backup Configuration**

### **Step 16: Setup Automated Backups**
```bash
# Create backup script
cat > backup-argocd.sh << 'EOF'
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
NAMESPACE="argocd"
BACKUP_DIR="/backups/argocd"

# Create backup directory
mkdir -p $BACKUP_DIR

# Backup ArgoCD applications
kubectl get applications -n $NAMESPACE -o yaml > $BACKUP_DIR/applications_$DATE.yaml

# Backup ArgoCD configuration
kubectl get configmap argocd-cm -n $NAMESPACE -o yaml > $BACKUP_DIR/config_$DATE.yaml

# Backup RBAC configuration
kubectl get configmap argocd-rbac-cm -n $NAMESPACE -o yaml > $BACKUP_DIR/rbac_$DATE.yaml

# Backup secrets (be careful with this in production)
kubectl get secrets -n $NAMESPACE -o yaml > $BACKUP_DIR/secrets_$DATE.yaml

echo "Backup completed: $BACKUP_DIR"
EOF

chmod +x backup-argocd.sh
```

---

## ✅ **Verification Checklist**

### **Installation Verification**
- [ ] All ArgoCD pods are running
- [ ] Services are properly exposed
- [ ] UI is accessible
- [ ] CLI can connect
- [ ] Initial admin password is retrieved
- [ ] Admin password is changed

### **Security Verification**
- [ ] RBAC is configured
- [ ] Network policies are applied
- [ ] TLS is enabled
- [ ] Secrets are properly configured
- [ ] External authentication is set up (if applicable)

### **Monitoring Verification**
- [ ] Prometheus metrics are exposed
- [ ] ServiceMonitor is configured
- [ ] Grafana dashboard is created
- [ ] Alerts are configured

### **Backup Verification**
- [ ] Backup script is created
- [ ] Backup cron job is scheduled
- [ ] Backup retention policy is configured
- [ ] Backup restoration is tested

---

## 🚨 **Troubleshooting**

### **Common Issues**

**1. Pods not starting**
```bash
# Check pod status
kubectl get pods -n argocd

# Check pod logs
kubectl logs -n argocd deployment/argocd-server
kubectl logs -n argocd deployment/argocd-application-controller
kubectl logs -n argocd deployment/argocd-repo-server
```

**2. Cannot access UI**
```bash
# Check service status
kubectl get svc -n argocd

# Check ingress status
kubectl get ingress -n argocd

# Test port forwarding
kubectl port-forward svc/argocd-server 8080:443 -n argocd
```

**3. Authentication issues**
```bash
# Check RBAC configuration
kubectl get configmap argocd-rbac-cm -n argocd -o yaml

# Reset admin password
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData":{"admin.password":"$(openssl rand -base64 32)"}}'
```

---

## 📚 **Next Steps**

After completing this installation:

1. **Configure Git repositories** (Task 2)
2. **Deploy first application** (Task 3)
3. **Set up monitoring dashboards**
4. **Configure backup and restore procedures**
5. **Train team members on ArgoCD usage**

---

## 📝 **Documentation**

### **Created Files**
- `argocd-production-values.yaml` - Production Helm values
- `backup-argocd.sh` - Backup script
- RBAC configuration
- Network policies
- Monitoring configuration

### **Important Commands**
```bash
# Check ArgoCD status
argocd version

# List applications
argocd app list

# Get application details
argocd app get <app-name>

# Sync application
argocd app sync <app-name>
```

---

*This completes the production-ready ArgoCD installation using Helm with enterprise-grade configurations, security hardening, monitoring, and backup procedures.*

