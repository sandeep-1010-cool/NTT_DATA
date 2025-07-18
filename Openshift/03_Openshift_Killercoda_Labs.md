# OpenShift Hands-on Labs Guide

## Table of Contents
1. [Lab Setup and Prerequisites](#lab-setup-and-prerequisites)
2. [Lab 1: OpenShift Basics](#lab-1-openshift-basics)
3. [Lab 2: Application Deployment](#lab-2-application-deployment)
4. [Lab 3: Build and Image Management](#lab-3-build-and-image-management)
5. [Lab 4: Networking and Routing](#lab-4-networking-and-routing)
6. [Lab 5: Security Configuration](#lab-5-security-configuration)
7. [Lab 6: Storage Management](#lab-6-storage-management)
8. [Lab 7: CI/CD Pipelines](#lab-7-cicd-pipelines)
9. [Lab 8: Monitoring and Observability](#lab-8-monitoring-and-observability)
10. [Lab 9: Advanced Features](#lab-9-advanced-features)

---

## Lab Setup and Prerequisites

### Prerequisites
- Access to [OpenShift Developer Sandbox](https://developers.redhat.com/developer-sandbox)
- Basic understanding of containers and Kubernetes concepts
- Git repository for storing code examples

### Important Notes for Developer Sandbox
- **Project Creation**: The Developer Sandbox restricts creating new projects
- **Use Existing Project**: All labs will use your existing project `vuyyalasandeep91-dev`
- **Permission Limitations**: Some "Forbidden" errors are normal - you can still work with basic resources
- **Resource Limits**: Be mindful of resource usage within the free tier
- **Session Duration**: Sandbox sessions have time limits
- **Existing Resources**: Your project may have pre-existing resources (workspace, modelmesh-serving, etc.)

### Getting Started with OpenShift Developer Sandbox

#### Step 1: Access the Sandbox
1. Visit [OpenShift Developer Sandbox](https://developers.redhat.com/developer-sandbox)
2. Click "Start your free sandbox"
3. Sign in with your Red Hat account using these credentials:
   - **Email**: `vuyyalasandeep91@gmail.com`
   - **Password**: `ChangePwd@13081991`
4. Complete the required information form:
   - **First name**: `sandeep`
   - **Last name**: `vuyyala`
   - **Email address**: `vuyyalasandeep91@gmail.com`
   - **Terms and conditions**: Check the box to agree
5. Click "Submit" and wait for your sandbox to be provisioned (usually 2-3 minutes)

#### Step 1.5: Select OpenShift Platform
1. On the Red Hat Developer Sandbox dashboard, you'll see multiple product cards
2. **Select the "OpenShift" card** (first card with red and black "O" logo)
3. Click the **"Try it"** button on the OpenShift card
4. This will provision your OpenShift cluster for the labs
5. Wait for the OpenShift cluster to be ready (2-3 minutes)

#### Step 2: Access Your Cluster
1. Once provisioned, you'll see your cluster URL
2. Click on the cluster URL to access the web console
3. Log in with the provided credentials
4. You'll see the OpenShift web console dashboard

#### Step 3: Install OpenShift CLI (oc)
```bash
# For Windows:
# 1. Download from: https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/windows/oc.zip
# 2. Extract the zip file
# 3. Add the extracted folder to your system PATH
# 4. Open a new command prompt and test: oc version

# For Linux/macOS:
curl -L https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/linux/oc.tar.gz | tar xz
sudo mv oc /usr/local/bin/
oc version
```

#### Step 4: Get Your Login Token
1. In the OpenShift web console, click on your username `vuyyalasandeep91` in the top right corner
2. Select "Copy login command" from the dropdown menu
3. Copy the `oc login` command that appears (it will include your token and server URL)
4. Save this command for the next step

#### Step 5: Login via CLI
```bash
# Use the login command you copied from the web console
# It will look something like this:
oc login --token=sha256~YOUR_TOKEN_HERE --server=https://api.YOUR_CLUSTER.openshiftapps.com:6443

# Verify you're logged in
oc whoami

# List your projects
oc get projects
```

---

## Lab 1: OpenShift Basics

### Objective
Learn basic OpenShift operations including project creation, pod management, and service configuration.

### Quick Start Exercise - Your First Application
**Complete this exercise before proceeding to the detailed labs below.**

#### **Step 1: Use Your Existing Project**
```bash
# Note: Developer Sandbox has restrictions on project creation
# Use your existing project instead of creating a new one

# Switch to your existing project
oc project vuyyalasandeep91-dev

# Verify the project
oc project

# List current resources (ignore "Forbidden" errors - they're normal)
oc get all

# List only basic resources to avoid permission errors
oc get pods,services,deployments
```

**Important**: The OpenShift Developer Sandbox restricts project creation. Use your existing project `vuyyalasandeep91-dev` for all labs. Some "Forbidden" errors are normal and don't affect your ability to work with basic OpenShift resources.

#### **Step 2: Deploy Your First Application**
```bash
# Deploy a sample Node.js application
oc new-app nodejs~https://github.com/sclorg/nodejs-ex

# Monitor the deployment
oc get pods -w
```

#### **Step 3: Expose Your Application**
```bash
# Create a route to access your application
oc expose service nodejs-ex

# Get the route URL
oc get routes

# Access your application in the browser using the route URL
```

#### **Step 4: Explore Your Deployment**
```bash
# List basic resources (avoid permission errors)
oc get pods,services,deployments,routes

# View deployment details
oc describe deployment nodejs-ex

# Check application logs
oc logs deployment/nodejs-ex

# If using DeploymentConfig instead of Deployment:
# oc describe deploymentconfig nodejs-ex
# oc logs dc/nodejs-ex
```

#### **Step 5: Troubleshooting Common Issues**
```bash
# If you see "Forbidden" errors, they're normal in Developer Sandbox
# Focus on resources you can access: pods, services, deployments, routes

# Check if your application is running
oc get pods -l app=nodejs-ex

# View application logs
oc logs -l app=nodejs-ex

# Check service endpoints
oc get endpoints

# Verify routes are working
oc get routes
```

### Exercise 1.1: Create and Manage Projects

**Purpose**: Learn the fundamentals of OpenShift project management, which serves as the foundation for organizing and isolating applications in the cluster.

**Expected Outcomes**:
- Create and manage OpenShift projects using CLI commands
- Understand the relationship between projects and Kubernetes namespaces
- Configure resource quotas to control project resource consumption
- Explore project resources and understand their organization
- Apply best practices for project naming and structure

#### Step 1: Use Your Existing Project
```bash
# Note: Developer Sandbox restricts project creation
# Use your existing project for all exercises

# Switch to your existing project
oc project vuyyalasandeep91-dev

# Verify the project
oc project

# List current resources
oc get all
```

**Concept**: Projects in OpenShift are Kubernetes namespaces with additional features like security policies, quotas, and network isolation. In the Developer Sandbox, you'll use your existing project `vuyyalasandeep91-dev` for all exercises.

#### Step 2: Explore Project Resources
```bash
# View all resources in the project
oc get all

# View project details
oc describe project openshift-labs

# List all resource types available
oc api-resources
```

#### Step 3: Set Project Quotas
```bash
# Create a resource quota for the project
cat <<EOF | oc apply -f -
apiVersion: v1
kind: ResourceQuota
metadata:
  name: compute-quota
spec:
  hard:
    requests.cpu: "2"
    requests.memory: 4Gi
    limits.cpu: "4"
    limits.memory: 8Gi
    persistentvolumeclaims: "5"
EOF
```

### Exercise 1.2: Deploy a Simple Application

**Purpose**: Master the basics of pod deployment and service configuration in OpenShift, understanding the fundamental building blocks of containerized applications.

**Expected Outcomes**:
- Create and manage individual pods using YAML definitions
- Monitor pod lifecycle and troubleshoot common issues
- Create services for internal pod communication
- Test service connectivity and understand networking basics
- Apply basic OpenShift CLI commands for resource management

#### Step 1: Create a Simple Pod
```bash
# Create a simple nginx pod
cat <<EOF | oc apply -f -
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
EOF
```

#### Step 2: Monitor Pod Status
```bash
# Check pod status
oc get pods

# Get detailed pod information
oc describe pod nginx-pod

# View pod logs
oc logs nginx-pod
```

#### Step 3: Create a Service
```bash
# Create a service for the nginx pod
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
  type: ClusterIP
EOF
```

#### Step 4: Verify Service Creation
```bash
# List services
oc get services

# Get service details
oc describe service nginx-service

# Test service connectivity
oc exec nginx-pod -- curl -s http://nginx-service
```

### Exercise 1.3: Use OpenShift CLI Commands

**Purpose**: Develop proficiency with the OpenShift CLI (oc) to efficiently manage resources and troubleshoot issues in the cluster.

**Expected Outcomes**:
- Navigate and explore OpenShift resources using CLI commands
- Use resource management commands for pods, services, and other objects
- Access pods interactively for debugging and testing
- Apply CLI best practices for efficient cluster management
- Understand the relationship between CLI and web console operations

#### Step 1: Explore oc Commands
```bash
# Get help for oc commands
oc help

# List all available commands
oc --help

# Get help for specific command
oc get --help
```

#### Step 2: Resource Management
```bash
# List all pods
oc get pods

# List all services
oc get services

# List all resources with labels
oc get all --show-labels

# Get resources in YAML format
oc get pod nginx-pod -o yaml
```

#### Step 3: Interactive Pod Access
```bash
# Access the nginx pod
oc rsh nginx-pod

# Inside the pod, test nginx
curl -s http://localhost

# Exit the pod
exit
```

---

## Lab 2: Application Deployment

### Objective
Learn to deploy applications using DeploymentConfig and oc new-app commands.

### Exercise 2.1: Deploy Using oc new-app

**Purpose**: Learn to use OpenShift's powerful `oc new-app` command for rapid application deployment and prototyping with automatic resource creation.

**Expected Outcomes**:
- Deploy applications directly from Git repositories using S2I
- Understand the automatic creation of BuildConfig, ImageStream, DeploymentConfig, and Service
- Monitor the build and deployment process
- Expose applications externally using routes
- Apply the automation benefits of `oc new-app` for rapid development

#### Step 1: Deploy from Git Repository
```bash
# Deploy a sample Node.js application
oc new-app nodejs~https://github.com/sclorg/nodejs-ex

# Monitor the deployment
oc get pods -w
```

**Concept**: `oc new-app` automatically creates BuildConfig, ImageStream, DeploymentConfig, and Service resources.

#### Step 2: Explore Created Resources
```bash
# List all created resources
oc get all

# View the deployment configuration
oc get deploymentconfigs

# View the build configuration
oc get buildconfigs

# View image streams
oc get imagestreams
```

#### Step 3: Expose the Application
```bash
# Create a route to expose the application
oc expose service nodejs-ex

# Get the route URL
oc get routes

# Access the application
# Copy the route URL and open it in your browser
```

### Exercise 2.2: Deploy with Custom Parameters

**Purpose**: Master advanced deployment techniques using custom parameters, environment variables, and application scaling in OpenShift.

**Expected Outcomes**:
- Deploy applications with custom environment variables and configuration
- Monitor build processes and deployment status
- Scale applications horizontally using OpenShift commands
- Understand deployment strategies and resource management
- Apply production-ready deployment practices

#### Step 1: Deploy with Environment Variables
```bash
# Deploy with custom environment variables
oc new-app nodejs~https://github.com/sclorg/nodejs-ex \
  --env NODE_ENV=production \
  --env PORT=8080 \
  --name my-nodejs-app
```

#### Step 2: Monitor Build and Deployment
```bash
# Watch the build process
oc logs -f bc/my-nodejs-app

# Watch the deployment
oc rollout status dc/my-nodejs-app

# Check pod status
oc get pods -l app=my-nodejs-app
```

#### Step 3: Scale the Application
```bash
# Scale the application to 3 replicas
oc scale dc/my-nodejs-app --replicas=3

# Verify scaling
oc get pods -l app=my-nodejs-app

# Scale back to 1 replica
oc scale dc/my-nodejs-app --replicas=1
```

### Exercise 2.3: Deploy Using DeploymentConfig

**Purpose**: Learn to create and manage DeploymentConfigs manually for fine-grained control over application deployments and lifecycle management.

**Expected Outcomes**:
- Create DeploymentConfigs manually using YAML definitions
- Configure deployment strategies and resource limits
- Set up services for DeploymentConfig-based applications
- Test deployment functionality and service connectivity
- Understand the differences between DeploymentConfig and Kubernetes Deployments

#### Step 1: Create DeploymentConfig Manually
```bash
# Create a DeploymentConfig for a Python application
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: python-app
spec:
  replicas: 2
  selector:
    app: python-app
  template:
    metadata:
      labels:
        app: python-app
    spec:
      containers:
      - name: python-app
        image: python:3.9-slim
        command: ["python", "-m", "http.server", "8080"]
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
  triggers:
  - type: ConfigChange
EOF
```

#### Step 2: Create Service for DeploymentConfig
```bash
# Create a service for the Python app
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Service
metadata:
  name: python-app-service
spec:
  selector:
    app: python-app
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
  type: ClusterIP
EOF
```

#### Step 3: Test the Deployment
```bash
# Check deployment status
oc rollout status dc/python-app

# List pods
oc get pods -l app=python-app

# Test service connectivity
oc exec $(oc get pods -l app=python-app -o jsonpath='{.items[0].metadata.name}') -- curl -s http://python-app-service:8080
```

---

## Lab 3: Build and Image Management

### Objective
Learn to build container images using Source-to-Image (S2I) and manage ImageStreams.

### Exercise 3.1: Source-to-Image (S2I) Build

**Purpose**: Master OpenShift's Source-to-Image (S2I) build system for automatically creating container images from source code without Dockerfiles.

**Expected Outcomes**:
- Create BuildConfigs for S2I-based image building
- Monitor build processes and troubleshoot build issues
- Deploy applications from built images using DeploymentConfigs
- Understand the S2I build process and available builders
- Apply S2I best practices for different programming languages

#### Step 1: Create a BuildConfig
```bash
# Create a BuildConfig for a Node.js application
cat <<EOF | oc apply -f -
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: my-app-build
spec:
  output:
    to:
      kind: ImageStreamTag
      name: my-app:latest
  source:
    git:
      uri: https://github.com/sclorg/nodejs-ex
      ref: master
  strategy:
    type: Source
    sourceStrategy:
      from:
        kind: ImageStreamTag
        name: nodejs:16
  triggers:
  - type: ConfigChange
EOF
```

#### Step 2: Start the Build
```bash
# Start the build
oc start-build my-app-build

# Monitor the build
oc logs -f bc/my-app-build

# List builds
oc get builds
```

#### Step 3: Deploy from Built Image
```bash
# Create a DeploymentConfig using the built image
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: my-app
spec:
  replicas: 1
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
EOF
```

### Exercise 3.2: Docker Build Strategy

#### Step 1: Create Docker BuildConfig
```bash
# Create a BuildConfig using Docker strategy
cat <<EOF | oc apply -f -
apiVersion: build.openshift.io/v1
kind: BuildConfig
metadata:
  name: docker-app-build
spec:
  output:
    to:
      kind: ImageStreamTag
      name: docker-app:latest
  source:
    git:
      uri: https://github.com/sclorg/django-ex
      ref: master
    contextDir: .
  strategy:
    type: Docker
    dockerStrategy:
      dockerfilePath: Dockerfile
  triggers:
  - type: ConfigChange
EOF
```

#### Step 2: Monitor Docker Build
```bash
# Start the build
oc start-build docker-app-build

# Watch build logs
oc logs -f bc/docker-app-build

# Check build status
oc get builds
```

### Exercise 3.3: ImageStream Management

#### Step 1: Create ImageStream
```bash
# Create an ImageStream
cat <<EOF | oc apply -f -
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
EOF
```

#### Step 2: Import External Images
```bash
# Import nginx image from Docker Hub
oc import-image nginx:latest --from=docker.io/nginx:latest --confirm

# List image streams
oc get imagestreams

# View image stream details
oc describe imagestream nginx
```

#### Step 3: Tag Images
```bash
# Tag an image
oc tag my-app:latest my-app:v1.1

# List all tags
oc get imagestreamtag

# Remove a tag
oc tag my-app:v1.1 -d
```

---

## Lab 4: Networking and Routing

### Objective
Learn to configure networking, services, and routes for external access.

### Exercise 4.1: Service Configuration

**Purpose**: Learn to configure and manage Kubernetes services in OpenShift for internal networking, service discovery, and load balancing.

**Expected Outcomes**:
- Create and configure different types of services (ClusterIP, NodePort)
- Test service-to-service communication within the cluster
- Understand service selectors and endpoint management
- Apply networking best practices for microservices
- Troubleshoot service connectivity issues

#### Step 1: Create Multiple Services
```bash
# Deploy a multi-container application
oc new-app https://github.com/sclorg/nodejs-ex --name frontend
oc new-app https://github.com/sclorg/nodejs-ex --name backend

# Create a service for frontend
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  selector:
    app: frontend
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
  type: ClusterIP
EOF

# Create a service for backend
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Service
metadata:
  name: backend-service
spec:
  selector:
    app: backend
  ports:
  - port: 8080
    targetPort: 8080
    protocol: TCP
  type: ClusterIP
EOF
```

#### Step 2: Test Service Communication
```bash
# Get pod names
FRONTEND_POD=$(oc get pods -l app=frontend -o jsonpath='{.items[0].metadata.name}')
BACKEND_POD=$(oc get pods -l app=backend -o jsonpath='{.items[0].metadata.name}')

# Test frontend to backend communication
oc exec $FRONTEND_POD -- curl -s http://backend-service:8080

# Test backend to frontend communication
oc exec $BACKEND_POD -- curl -s http://frontend-service:8080
```

### Exercise 4.2: Route Configuration

#### Step 1: Create Basic Route
```bash
# Expose frontend service
oc expose service frontend-service --name=frontend-route

# Expose backend service
oc expose service backend-service --name=backend-route

# List routes
oc get routes
```

#### Step 2: Create Custom Route
```bash
# Create a custom route with specific hostname
cat <<EOF | oc apply -f -
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: custom-frontend-route
spec:
  host: frontend-openshift-labs.apps.sandbox.x8i5.p1.openshiftapps.com
  to:
    kind: Service
    name: frontend-service
  port:
    targetPort: 8080
EOF
```

#### Step 3: Test Route Access
```bash
# Get route URLs
oc get routes

# Test route access (replace with your actual route URL)
curl -s http://frontend-openshift-labs.apps.sandbox.x8i5.p1.openshiftapps.com
```

### Exercise 4.3: Network Policies

#### Step 1: Create Network Policy
```bash
# Create a network policy to allow traffic between frontend and backend
cat <<EOF | oc apply -f -
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-backend
spec:
  podSelector:
    matchLabels:
      app: backend
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
EOF
```

#### Step 2: Test Network Policy
```bash
# Test that frontend can reach backend
oc exec $FRONTEND_POD -- curl -s http://backend-service:8080

# Test that backend can reach frontend (should be blocked by default)
oc exec $BACKEND_POD -- curl -s http://frontend-service:8080
```

---

## Lab 5: Security Configuration

### Objective
Learn to configure security context constraints, RBAC, and service accounts.

### Exercise 5.1: Security Context Constraints

**Purpose**: Master OpenShift's Security Context Constraints (SCCs) to enforce pod-level security policies and protect the cluster from malicious containers.

**Expected Outcomes**:
- Understand different SCC types and their security implications
- Create custom SCCs for specific application requirements
- Apply SCCs to service accounts and pods
- Configure pod security contexts and capabilities
- Implement least-privilege security principles
- Troubleshoot SCC-related deployment issues

#### Step 1: View Available SCCs
```bash
# List all SCCs
oc get scc

# View details of restricted SCC
oc describe scc restricted

# View details of anyuid SCC
oc describe scc anyuid
```

#### Step 2: Create Custom SCC
```bash
# Create a custom SCC for your application
cat <<EOF | oc apply -f -
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
EOF
```

#### Step 3: Apply SCC to Service Account
```bash
# Create a service account
oc create serviceaccount my-app-sa

# Add SCC to service account
oc adm policy add-scc-to-user my-app-scc -z my-app-sa

# Verify SCC assignment
oc get scc my-app-scc -o yaml
```

### Exercise 5.2: RBAC Configuration

#### Step 1: Create Role and RoleBinding
```bash
# Create a custom role
cat <<EOF | oc apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-developer
rules:
- apiGroups: [""]
  resources: ["pods", "services", "routes"]
  verbs: ["get", "list", "create", "update", "delete"]
- apiGroups: ["apps.openshift.io"]
  resources: ["deploymentconfigs"]
  verbs: ["get", "list", "create", "update", "delete"]
EOF

# Create a role binding
cat <<EOF | oc apply -f -
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: developer-binding
subjects:
- kind: ServiceAccount
  name: my-app-sa
  namespace: openshift-labs
roleRef:
  kind: Role
  name: app-developer
  apiGroup: rbac.authorization.k8s.io
EOF
```

#### Step 2: Test RBAC
```bash
# Test service account permissions
oc auth can-i create pods --as=system:serviceaccount:openshift-labs:my-app-sa

oc auth can-i delete pods --as=system:serviceaccount:openshift-labs:my-app-sa

# List roles and role bindings
oc get roles
oc get rolebindings
```

### Exercise 5.3: Service Account Usage

#### Step 1: Deploy with Service Account
```bash
# Deploy an application using the service account
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: secure-app
spec:
  replicas: 1
  selector:
    app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      serviceAccountName: my-app-sa
      containers:
      - name: secure-app
        image: nginx:latest
        ports:
        - containerPort: 80
EOF
```

#### Step 2: Verify Service Account Usage
```bash
# Check that the pod is using the service account
oc get pod -l app=secure-app -o yaml | grep serviceAccountName

# Check pod security context
oc get pod -l app=secure-app -o yaml | grep -A 10 securityContext
```

---

## Lab 6: Storage Management

### Objective
Learn to configure persistent volumes, claims, and storage classes.

### Exercise 6.1: Persistent Volume Claims

**Purpose**: Learn to configure persistent storage in OpenShift using PersistentVolumeClaims (PVCs) to provide reliable, persistent data storage for applications.

**Expected Outcomes**:
- Create and manage PVCs for application data storage
- Deploy applications that use persistent volumes
- Test data persistence across pod restarts
- Understand different access modes and storage classes
- Apply storage best practices for stateful applications
- Troubleshoot storage-related issues

#### Step 1: Create PVC
```bash
# Create a persistent volume claim
cat <<EOF | oc apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-storage
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

#### Step 2: Deploy Application with PVC
```bash
# Deploy an application that uses the PVC
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: app-with-storage
spec:
  replicas: 1
  selector:
    app: app-with-storage
  template:
    metadata:
      labels:
        app: app-with-storage
    spec:
      containers:
      - name: app
        image: nginx:latest
        ports:
        - containerPort: 80
        volumeMounts:
        - name: app-storage
          mountPath: /usr/share/nginx/html
      volumes:
      - name: app-storage
        persistentVolumeClaim:
          claimName: app-storage
EOF
```

#### Step 3: Test Persistent Storage
```bash
# Get the pod name
POD_NAME=$(oc get pods -l app=app-with-storage -o jsonpath='{.items[0].metadata.name}')

# Create a file in the persistent volume
oc exec $POD_NAME -- sh -c "echo 'Hello from persistent storage!' > /usr/share/nginx/html/test.txt"

# Verify the file was created
oc exec $POD_NAME -- cat /usr/share/nginx/html/test.txt

# Delete the pod to test persistence
oc delete pod $POD_NAME

# Wait for new pod to be created
oc get pods -l app=app-with-storage

# Check if the file still exists
NEW_POD_NAME=$(oc get pods -l app=app-with-storage -o jsonpath='{.items[0].metadata.name}')
oc exec $NEW_POD_NAME -- cat /usr/share/nginx/html/test.txt
```

### Exercise 6.2: Storage Classes

#### Step 1: List Available Storage Classes
```bash
# List storage classes
oc get storageclass

# View storage class details
oc describe storageclass
```

#### Step 2: Create PVC with Specific Storage Class
```bash
# Create PVC with specific storage class (if available)
cat <<EOF | oc apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: fast-storage
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
  storageClassName: fast
EOF
```

### Exercise 6.3: ConfigMaps and Secrets

#### Step 1: Create ConfigMap
```bash
# Create a ConfigMap
cat <<EOF | oc apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database_url: "postgresql://user:pass@db:5432/mydb"
  api_key: "development-key"
  environment: "development"
EOF
```

#### Step 2: Create Secret
```bash
# Create a secret
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: app-secret
type: Opaque
data:
  username: dXNlcg==  # base64 encoded "user"
  password: cGFzcw==  # base64 encoded "pass"
EOF
```

#### Step 3: Use ConfigMap and Secret in Pod
```bash
# Deploy application using ConfigMap and Secret
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: config-app
spec:
  replicas: 1
  selector:
    app: config-app
  template:
    metadata:
      labels:
        app: config-app
    spec:
      containers:
      - name: config-app
        image: nginx:latest
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_URL
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database_url
        - name: API_KEY
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: api_key
        - name: DB_USERNAME
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: username
        - name: DB_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secret
              key: password
EOF
```

---

## Lab 7: CI/CD Pipelines

### Objective
Learn to create and manage CI/CD pipelines using OpenShift Pipelines (Tekton).

### Exercise 7.1: Install OpenShift Pipelines

**Purpose**: Learn to implement CI/CD pipelines in OpenShift using Tekton for automated build, test, and deployment processes.

**Expected Outcomes**:
- Install and configure OpenShift Pipelines (Tekton)
- Create and manage Tekton pipelines for application lifecycle
- Monitor pipeline execution and troubleshoot failures
- Implement automated build and deployment workflows
- Apply CI/CD best practices for OpenShift applications
- Integrate pipelines with external tools and services

#### Step 1: Check Pipeline Operator
```bash
# Check if OpenShift Pipelines is installed
oc get csv -n openshift-operators | grep pipelines

# If not installed, you can install it via the OperatorHub in the web console
# Go to Operators → OperatorHub → Search for "OpenShift Pipelines"
```

#### Step 2: Create Pipeline Resources
```bash
# Create a pipeline for building and deploying an application
cat <<EOF | oc apply -f -
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
  - name: image-name
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
      value: \$(params.git-url)
    - name: revision
      value: \$(params.git-revision)
  - name: build-image
    runAfter: ["fetch-repository"]
    taskRef:
      name: buildah
    workspaces:
    - name: source
      workspace: shared-workspace
    params:
    - name: IMAGE
      value: \$(params.image-name)
EOF
```

#### Step 2: Create PipelineRun
```bash
# Create a PipelineRun to execute the pipeline
cat <<EOF | oc apply -f -
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
    value: https://github.com/sclorg/nodejs-ex
  - name: git-revision
    value: master
  - name: image-name
    value: image-registry.openshift-image-registry.svc:5000/openshift-labs/my-app:latest
EOF
```

#### Step 3: Monitor Pipeline Execution
```bash
# List pipeline runs
oc get pipelineruns

# Get pipeline run details
oc describe pipelinerun build-and-deploy-run

# View pipeline run logs
oc logs pipelinerun/build-and-deploy-run
```

### Exercise 7.2: ArgoCD Integration

#### Step 1: Install ArgoCD Operator
```bash
# Install ArgoCD operator via OperatorHub
# Go to Operators → OperatorHub → Search for "ArgoCD"
# Click Install
```

#### Step 2: Create ArgoCD Application
```bash
# Create an ArgoCD application
cat <<EOF | oc apply -f -
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: my-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/sclorg/nodejs-ex
    targetRevision: HEAD
    path: k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: openshift-labs
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
    - CreateNamespace=true
  revisionHistoryLimit: 10
EOF
```

---

## Lab 8: Monitoring and Observability

### Objective
Learn to configure monitoring, logging, and observability in OpenShift.

### Exercise 8.1: Application Monitoring

**Purpose**: Implement comprehensive application monitoring in OpenShift using Prometheus for metrics collection, alerting, and observability.

**Expected Outcomes**:
- Deploy applications that expose metrics endpoints
- Create ServiceMonitors for application metrics collection
- Configure PrometheusRules for alerting and monitoring
- Set up basic monitoring and observability
- Apply monitoring best practices for OpenShift applications
- Understand the monitoring stack architecture

#### Step 1: Deploy Application with Metrics
```bash
# Deploy an application that exposes metrics
oc new-app https://github.com/sclorg/nodejs-ex --name monitored-app

# Expose the application
oc expose service monitored-app
```

#### Step 2: Create ServiceMonitor
```bash
# Create a ServiceMonitor for the application
cat <<EOF | oc apply -f -
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: monitored-app
  namespace: openshift-labs
spec:
  selector:
    matchLabels:
      app: monitored-app
  endpoints:
  - port: 8080
    interval: 30s
    path: /metrics
EOF
```

#### Step 3: Create PrometheusRule
```bash
# Create alerting rules
cat <<EOF | oc apply -f -
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: monitored-app-rules
  namespace: openshift-labs
spec:
  groups:
  - name: monitored-app
    rules:
    - alert: HighErrorRate
      expr: rate(http_requests_total{status=~"5.."}[5m]) > 0.1
      for: 5m
      labels:
        severity: warning
      annotations:
        summary: "High error rate detected"
        description: "Error rate is {{ \$value }} errors per second"
EOF
```

### Exercise 8.2: Centralized Logging

#### Step 1: Deploy EFK Stack
```bash
# Deploy Elasticsearch
oc new-app --template=elasticsearch-ephemeral --name=elasticsearch

# Deploy Kibana
oc new-app --template=kibana --name=kibana

# Deploy Fluentd
oc new-app --template=fluentd --name=fluentd
```

#### Step 2: Configure Log Collection
```bash
# Create a ConfigMap for Fluentd configuration
cat <<EOF | oc apply -f -
apiVersion: v1
kind: ConfigMap
metadata:
  name: fluentd-config
data:
  fluent.conf: |
    <source>
      @type tail
      path /var/log/containers/*.log
      pos_file /var/log/fluentd-containers.log.pos
      tag kubernetes.*
      read_from_head true
      <parse>
        @type json
        time_key time
        time_format %Y-%m-%dT%H:%M:%S.%NZ
      </parse>
    </source>
    
    <match kubernetes.**>
      @type elasticsearch
      host elasticsearch
      port 9200
      logstash_format true
      logstash_prefix k8s
    </match>
EOF
```

### Exercise 8.3: Distributed Tracing

#### Step 1: Deploy Jaeger
```bash
# Deploy Jaeger for distributed tracing
oc new-app --template=jaeger --name=jaeger
```

#### Step 2: Configure Application for Tracing
```bash
# Deploy application with Jaeger integration
oc new-app https://github.com/sclorg/nodejs-ex --name traced-app

# Add Jaeger environment variables
oc set env dc/traced-app JAEGER_AGENT_HOST=jaeger-agent JAEGER_AGENT_PORT=6831
```

---

## Lab 9: Advanced Features

### Objective
Learn advanced OpenShift features including service mesh, operators, and multi-cluster management.

### Exercise 9.1: Service Mesh (Istio)

**Purpose**: Implement service mesh capabilities in OpenShift using Istio for advanced traffic management, security, and observability.

**Expected Outcomes**:
- Install and configure Istio service mesh in OpenShift
- Create VirtualServices for traffic routing and load balancing
- Configure DestinationRules for traffic policies
- Implement basic service mesh patterns for microservices
- Understand service mesh architecture and benefits
- Apply service mesh best practices for OpenShift applications

#### Step 1: Install Service Mesh Operator
```bash
# Install Service Mesh operator via OperatorHub
# Go to Operators → OperatorHub → Search for "Service Mesh"
# Click Install
```

#### Step 2: Create ServiceMesh Control Plane
```bash
# Create a ServiceMesh control plane
cat <<EOF | oc apply -f -
apiVersion: maistra.io/v2
kind: ServiceMeshControlPlane
metadata:
  name: basic-install
  namespace: istio-system
spec:
  version: v2.4
  tracing:
    type: Jaeger
  policy:
    type: Istiod
  telemetry:
    type: Istiod
  addons:
    grafana:
      enabled: true
    kiali:
      enabled: true
    prometheus:
      enabled: true
EOF
```

#### Step 3: Create VirtualService
```bash
# Create a VirtualService for traffic routing
cat <<EOF | oc apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: my-app
spec:
  hosts:
  - my-app.example.com
  http:
  - route:
    - destination:
        host: my-app-service
        port:
          number: 8080
      weight: 100
EOF
```

### Exercise 9.2: Operators

#### Step 1: Install PostgreSQL Operator
```bash
# Install PostgreSQL operator via OperatorHub
# Go to Operators → OperatorHub → Search for "PostgreSQL"
# Click Install
```

#### Step 2: Create PostgreSQL Cluster
```bash
# Create a PostgreSQL cluster
cat <<EOF | oc apply -f -
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
EOF
```

### Exercise 9.3: Multi-cluster Management

#### Step 1: Access Multiple Clusters
```bash
# Configure access to multiple clusters
# You can create additional sandbox environments or use different projects

# List current context
oc config current-context

# List all contexts
oc config get-contexts

# Switch between contexts
oc config use-context <context-name>
```

#### Step 2: Deploy Across Clusters
```bash
# Deploy the same application to different clusters/projects
oc new-project cluster1-app
oc new-app https://github.com/sclorg/nodejs-ex --name app-cluster1

oc new-project cluster2-app
oc new-app https://github.com/sclorg/nodejs-ex --name app-cluster2
```

---

## Lab Summary and Best Practices

### Key Takeaways

1. **Project Management**: Use projects for resource isolation and organization
2. **Security**: Always apply appropriate SCCs and RBAC policies
3. **Monitoring**: Implement comprehensive monitoring from the start
4. **CI/CD**: Automate build and deployment processes
5. **Storage**: Plan storage requirements and use appropriate storage classes
6. **Networking**: Configure proper network policies and routes
7. **Operators**: Use operators for complex application lifecycle management

### Best Practices

#### Development
- Use `oc new-app` for quick prototyping
- Implement health checks in your applications
- Use resource limits and requests
- Follow the principle of least privilege

#### Production
- Use DeploymentConfigs for production deployments
- Implement proper monitoring and alerting
- Use persistent storage for stateful applications
- Configure network policies for security
- Use service mesh for advanced traffic management

#### Security
- Apply appropriate SCCs to pods
- Use service accounts for pod identity
- Implement RBAC for access control
- Use secrets for sensitive data
- Configure network policies

#### Monitoring
- Set up Prometheus and Grafana
- Configure centralized logging
- Implement distributed tracing
- Create meaningful alerts
- Monitor application and infrastructure metrics

### Next Steps

1. **Explore OpenShift Documentation**: [https://docs.openshift.com/](https://docs.openshift.com/)
2. **Join OpenShift Community**: [https://www.redhat.com/en/technologies/cloud-computing/openshift/community](https://www.redhat.com/en/technologies/cloud-computing/openshift/community)
3. **Practice with Real Applications**: Deploy your own applications to OpenShift
4. **Learn Advanced Topics**: Service mesh, operators, multi-cluster management
5. **Get Certified**: Consider OpenShift certification paths

### Troubleshooting Tips

1. **Check Pod Logs**: `oc logs <pod-name>`
2. **Describe Resources**: `oc describe <resource-type> <resource-name>`
3. **Check Events**: `oc get events`
4. **Verify Network**: `oc exec <pod> -- curl <service>`
5. **Check Resource Usage**: `oc top pods`

This hands-on lab guide provides a comprehensive introduction to OpenShift with practical exercises that build upon the theoretical concepts and examples covered in the previous guides. 