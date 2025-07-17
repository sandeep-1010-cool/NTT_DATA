# 🚀 Istio Hands-On Practice in Browser (No Setup Required)

## ✅ HEADLINE: Practice Istio Hands-On in Browser (No Setup Required)

---

### 📘 Markdown File Name: `istio-hands-on-browser.md`

---

### ⚙️ Objective:

Learn & practice Istio **step-by-step** on a **free browser-based platform**, without any local setup.

---

### 🧪 Best Free Platforms (Katacoda Alternative):

#### Option 1: **Killercoda** (Recommended)
> ✅ **Free Istio scenarios**  
> ✅ **Pre-configured environments**  
> ✅ **Step-by-step tutorials**  
> ✅ **No setup required**  
> 🔗 [https://killercoda.com/istio](https://killercoda.com/istio)

#### Option 2: **Play with Kubernetes (PWK)**
> ✅ **Free browser-based Kubernetes**  
> ✅ **Pre-installed Istio**  
> ✅ **Terminal access**  
> 🔗 [https://labs.play-with-k8s.com/](https://labs.play-with-k8s.com/)

#### Option 3: **Google Cloud Shell**
> ✅ **Free tier available**  
> ✅ **Pre-installed kubectl & istioctl**  
> ✅ **Integrated terminal**  
> 🔗 [https://shell.cloud.google.com/](https://shell.cloud.google.com/)

#### Option 4: **GitHub Codespaces**
> ✅ **Free tier available**  
> ✅ **Full development environment**  
> ✅ **Pre-configured with Docker & kubectl**  
> 🔗 [https://github.com/features/codespaces](https://github.com/features/codespaces)

---

### 🚀 Quick Start Steps:

## Step 1: Launch Killercoda Istio Environment

### 🚀 Getting Started with Killercoda:

1. **Open Killercoda**: Go to https://killercoda.com/istio
2. **Choose Scenario**: Select "Istio Playground" or "Istio Fundamentals"
3. **Start Scenario**: Click "Start Scenario" button
4. **Wait for Ready**: Wait for the environment to initialize (usually 30-60 seconds)
5. **Access Terminal**: The terminal will automatically open in the right pane

### 📋 Verify Setup:
```bash
# Check Kubernetes is running
kubectl get nodes
# Purpose: Verifies that your Kubernetes cluster is accessible and shows all nodes
# Expected: Should show 1-2 nodes in "Ready" status

# Check Istio installation
istioctl version
# Purpose: Confirms Istio CLI tool is installed and shows version information
# Expected: Should display Istio version (e.g., 1.17.0)

# Verify Istio is properly installed
kubectl get pods -n istio-system
# Purpose: Checks that Istio control plane components are running
# Expected: Should show pods like istiod, istio-ingressgateway, etc. in "Running" status
```

### Alternative: Play with Kubernetes (PWK)
👉 Open: https://labs.play-with-k8s.com/  
✅ Click "Start" and add a new instance

### Option C: Google Cloud Shell
👉 Open: https://shell.cloud.google.com/  
✅ Run: `gcloud container clusters create istio-demo --zone=us-central1-a`

### Option D: GitHub Codespaces
👉 Create a new repository with Istio manifests  
✅ Use GitHub Codespaces for full development environment

---

## Step 2: Deploy Sample App (Killercoda Environment)

### 📦 Deploy BookInfo App:
```bash
# Deploy the BookInfo sample application
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml
# Purpose: Deploys the BookInfo microservices demo application
# Expected: Creates 6 services: productpage, details, reviews, ratings, and review versions

# Verify pods are running
kubectl get pods
# Purpose: Checks that all BookInfo pods are created and starting
# Expected: Should show pods like productpage-v1, details-v1, reviews-v1, etc.

# Wait for all pods to be in Running state
kubectl wait --for=condition=Ready pod --all --timeout=300s
# Purpose: Ensures all pods are fully ready before proceeding
# Expected: Command completes when all pods show "Running" status
```

✅ Deploys the `BookInfo` microservices demo app with 6 services:
- `productpage` - Frontend
- `details` - Product details
- `reviews` - Product reviews
- `ratings` - Product ratings
- `reviews-v1`, `reviews-v2`, `reviews-v3` - Different review versions

---

## Step 3: Enable Automatic Sidecar Injection

```bash
kubectl label namespace default istio-injection=enabled
# Purpose: Enables automatic Istio sidecar injection for the default namespace
# Expected: Any new pods created in default namespace will automatically get Istio sidecar
```

---

## Step 4: Apply Gateway and VirtualService

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/bookinfo-gateway.yaml
# Purpose: Creates Istio Gateway and VirtualService to enable external access to BookInfo app
# Expected: Creates Gateway and VirtualService resources for traffic routing
```

---

## Step 5: Verify Access

```bash
kubectl get gateway
# Purpose: Lists all Istio Gateway resources to verify they were created
# Expected: Should show "bookinfo-gateway" in the list

kubectl get virtualservice
# Purpose: Lists all VirtualService resources to verify traffic routing rules
# Expected: Should show "bookinfo" VirtualService

kubectl get svc istio-ingressgateway -n istio-system
# Purpose: Shows the Istio ingress gateway service details
# Expected: Shows service with external IP or port information
```

---

## Step 6: Test App via Ingress (Killercoda Environment)

### 🌐 Access the Application:

```bash
# In Killercoda, use port-forward for easy access
kubectl port-forward -n istio-system service/istio-ingressgateway 8080:80 &
# Purpose: Creates a tunnel to access Istio ingress gateway from localhost
# Expected: Port-forward runs in background, making app accessible at localhost:8080

# Test the application
curl http://localhost:8080/productpage
# Purpose: Tests if the BookInfo application is accessible through Istio gateway
# Expected: Should return HTML content of the BookInfo product page

# Alternative: Get the service URL (if available in Killercoda)
kubectl get svc istio-ingressgateway -n istio-system
# Purpose: Shows service details including ports and endpoints
# Expected: Displays service configuration for external access

# For external access, Killercoda provides a public URL
# Check the scenario instructions for the specific URL
```

---

## Step 7: Add Istio Features (Optional)

* Traffic Shifting
* Fault Injection
* Rate Limiting
* mTLS / AuthorizationPolicy

Explore more here 👉 [https://istio.io/latest/docs/examples/](https://istio.io/latest/docs/examples/)

---

### 💡 Pro Tip Prompt (for Cursor/ChatGPT):

```bash
Prompt: Generate an Istio manifest to apply traffic shifting between two versions of a microservice using VirtualService. Target: BookInfo app.
```

---

## 🎯 Advanced Scenarios

### Traffic Management
```bash
# Apply traffic splitting (50/50 between v1 and v2)
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/virtual-service-all-v1.yaml
# Purpose: Routes all traffic to v1 services (baseline configuration)
# Expected: All requests go to v1 versions of services
```

### Fault Injection
```bash
# Inject delays and errors
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/virtual-service-reviews-test-abort.yaml
# Purpose: Injects HTTP 500 errors for testing fault tolerance
# Expected: Some requests to reviews service will return errors
```

### Circuit Breaker
```bash
# Apply circuit breaker to ratings service
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/destination-rule-all.yaml
# Purpose: Applies circuit breaker policies to prevent cascading failures
# Expected: Limits concurrent connections and requests to services
```

### Security (mTLS)
```bash
# Enable mutual TLS
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/security/peer-authentication.yaml
# Purpose: Enables mutual TLS authentication between services
# Expected: Services communicate with encrypted mTLS connections
```

---

## 📊 Monitoring & Observability

### View Service Mesh
```bash
# Access Kiali dashboard
istioctl dashboard kiali
# Purpose: Opens Kiali dashboard for visualizing service mesh topology
# Expected: Opens browser with service graph showing traffic flow between services
```

### View Metrics
```bash
# Access Grafana
istioctl dashboard grafana
# Purpose: Opens Grafana dashboard for viewing Istio metrics and performance data
# Expected: Opens browser with pre-configured Istio dashboards
```

### View Traces
```bash
# Access Jaeger
istioctl dashboard jaeger
# Purpose: Opens Jaeger for distributed tracing and request flow analysis
# Expected: Opens browser with Jaeger UI for tracing requests across services
```

---

## 🔧 Troubleshooting Commands

```bash
# Check Istio installation
istioctl verify-install
# Purpose: Verifies that Istio is properly installed and configured
# Expected: Should show "✓ Istio is installed successfully" if everything is OK

# Check proxy status
istioctl proxy-status
# Purpose: Shows status of all Istio sidecar proxies in the cluster
# Expected: Should show "SYNCED" status for all proxies

# Check proxy config
istioctl proxy-config all -n default
# Purpose: Shows detailed configuration of Istio proxies in default namespace
# Expected: Displays routes, clusters, listeners, and other proxy configurations

# View service endpoints
kubectl get endpoints -n default
# Purpose: Shows which pods are backing each service
# Expected: Lists all services and their corresponding pod IPs

# Check Istio resources
kubectl get virtualservice,destinationrule,gateway -n default
# Purpose: Lists all Istio traffic management resources
# Expected: Shows VirtualServices, DestinationRules, and Gateways in the namespace
```

---

## 📚 Additional Resources

* [Istio Official Documentation](https://istio.io/latest/docs/)
* [Istio Examples](https://istio.io/latest/docs/examples/)
* [Istio GitHub Repository](https://github.com/istio/istio)
* [Killercoda Istio Scenarios](https://killercoda.com/istio)
* [Play with Kubernetes](https://labs.play-with-k8s.com/)
* [Google Cloud Shell](https://shell.cloud.google.com/)
* [GitHub Codespaces](https://github.com/features/codespaces)

---

## 🎉 Success Checklist

- [ ] Browser-based environment launched
- [ ] BookInfo app deployed successfully
- [ ] Gateway and VirtualService configured
- [ ] Application accessible via ingress
- [ ] Traffic management features tested
- [ ] Monitoring dashboards accessed
- [ ] Basic troubleshooting commands understood

---

**🎯 Ready to practice Istio without any local setup!** 