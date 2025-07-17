# 🚀 Istio Hands-On Practice in Browser (No Setup Required)

## ✅ HEADLINE: Practice Istio Hands-On in Browser (No Setup Required)

---

### 📘 Markdown File Name: `istio-hands-on-browser.md`

---

### ⚙️ Objective:

Learn & practice Istio **step-by-step** on a **free browser-based platform**, without any local setup.

---

### 🧪 Best Free Platform: [Katacoda (Istio Scenario)](https://www.katacoda.com/solo-io/scenarios/istio-workshop)

> ✅ **No install needed** — runs in browser  
> ✅ **Pre-configured cluster** with Istio + sample apps  
> ✅ **CLI & editor in browser**

---

### 🚀 Quick Start Steps:

## Step 1: Launch Istio Lab in Browser
👉 Open this link: https://www.katacoda.com/solo-io/scenarios/istio-workshop  
✅ It will launch a browser-based terminal with Kubernetes & Istio installed.

---

## Step 2: Deploy Sample App

```bash
kubectl apply -f https://raw.githubusercontent.com/istio/istio/release-1.17/samples/bookinfo/platform/kube/bookinfo.yaml
```

✅ Deploys the `BookInfo` microservices demo app.

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

## Step 6: Test App via Ingress

```bash
export GATEWAY_URL=$(kubectl get svc istio-ingressgateway -n istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
curl http://$GATEWAY_URL/productpage
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
* [Katacoda Istio Scenarios](https://www.katacoda.com/solo-io/scenarios)

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