# 🚀 Istio Hands-On Practice in Browser (No Setup Required)

## ✅ HEADLINE: Practice Istio Hands-On in Browser (No Setup Required)

---

### 📘 Markdown File Name: `istio-hands-on-browser.md`

---

### ⚙️ Objective:

Learn & practice Istio **step-by-step** on a **free browser-based platform**, without any local setup.

---

### 🧪 Best Free Platforms (Katacoda Alternative):

#### Option 1: **Play with Kubernetes (PWK)**
> ✅ **Free browser-based Kubernetes**  
> ✅ **Pre-installed Istio**  
> ✅ **Terminal access**  
> 🔗 [https://labs.play-with-k8s.com/](https://labs.play-with-k8s.com/)

#### Option 2: **Killercoda**
> ✅ **Free Istio scenarios**  
> ✅ **Pre-configured environments**  
> ✅ **Step-by-step tutorials**  
> 🔗 [https://killercoda.com/istio](https://killercoda.com/istio)

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

## Step 1: Launch Play with Kubernetes (PWK)

### 🚀 Getting Started with PWK:

1. **Open PWK**: Go to https://labs.play-with-k8s.com/
2. **Start Session**: Click the "Start" button
3. **Add Instance**: Click "Add New Instance" to create a Kubernetes node
4. **Wait for Ready**: Wait for the instance to show "Ready" status (green dot)
5. **Access Terminal**: Click on your instance to open the terminal

### 📋 Verify Setup:
```bash
# Check Kubernetes is running
kubectl get nodes

# Check if Istio is pre-installed
istioctl version

# If Istio is not installed, install it:
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.17.0
export PATH=$PWD/bin:$PATH
istioctl install --set profile=demo -y
```

### Option B: Killercoda
👉 Open: https://killercoda.com/istio  
✅ Choose an Istio scenario and click "Start Scenario"

### Option C: Google Cloud Shell
👉 Open: https://shell.cloud.google.com/  
✅ Run: `gcloud container clusters create istio-demo --zone=us-central1-a`

### Option D: GitHub Codespaces
👉 Create a new repository with Istio manifests  
✅ Use GitHub Codespaces for full development environment

---

## Step 2: Deploy Sample App (PWK Specific)

### 📦 Deploy BookInfo App:
```bash
# Deploy the BookInfo sample application
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml

# Verify pods are running
kubectl get pods

# Wait for all pods to be in Running state
kubectl wait --for=condition=Ready pod --all --timeout=300s
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
```

---

## Step 4: Apply Gateway and VirtualService

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/bookinfo-gateway.yaml
```

---

## Step 5: Verify Access

```bash
kubectl get gateway
kubectl get virtualservice
```

💡 Use `kubectl get svc istio-ingressgateway -n istio-system` to find external IP.

---

## Step 6: Test App via Ingress (PWK Specific)

### 🌐 Access the Application:

```bash
# Get the external IP (in PWK, this will be the node IP)
export GATEWAY_URL=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="ExternalIP")].address}')

# If no external IP, use the node IP
if [ -z "$GATEWAY_URL" ]; then
  export GATEWAY_URL=$(kubectl get nodes -o jsonpath='{.items[0].status.addresses[?(@.type=="InternalIP")].address}')
fi

# Get the port
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')

# Test the application
curl http://$GATEWAY_URL:$INGRESS_PORT/productpage

# Or use port-forward for easier access
kubectl port-forward -n istio-system service/istio-ingressgateway 8080:80 &
curl http://localhost:8080/productpage
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
```

### Fault Injection
```bash
# Inject delays and errors
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/virtual-service-reviews-test-abort.yaml
```

### Circuit Breaker
```bash
# Apply circuit breaker to ratings service
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/networking/destination-rule-all.yaml
```

### Security (mTLS)
```bash
# Enable mutual TLS
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/security/peer-authentication.yaml
```

---

## 📊 Monitoring & Observability

### View Service Mesh
```bash
# Access Kiali dashboard
istioctl dashboard kiali
```

### View Metrics
```bash
# Access Grafana
istioctl dashboard grafana
```

### View Traces
```bash
# Access Jaeger
istioctl dashboard jaeger
```

---

## 🔧 Troubleshooting Commands

```bash
# Check Istio installation
istioctl verify-install

# Check proxy status
istioctl proxy-status

# Check proxy config
istioctl proxy-config all -n default

# View service endpoints
kubectl get endpoints -n default

# Check Istio resources
kubectl get virtualservice,destinationrule,gateway -n default
```

---

## 📚 Additional Resources

* [Istio Official Documentation](https://istio.io/latest/docs/)
* [Istio Examples](https://istio.io/latest/docs/examples/)
* [Istio GitHub Repository](https://github.com/istio/istio)
* [Play with Kubernetes](https://labs.play-with-k8s.com/)
* [Killercoda Istio Scenarios](https://killercoda.com/istio)
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