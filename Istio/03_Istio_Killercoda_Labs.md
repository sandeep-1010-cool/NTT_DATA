# Istio Killercoda Labs - Step-by-Step Practical Exercises

## 🎯 **Learning Objectives**

This guide provides **hands-on practice** in Killercoda environment with:
- ✅ **Step-by-step instructions** for each exercise
- ✅ **Purpose and expected outcomes** for every command
- ✅ **Underlying concepts** explained throughout
- ✅ **Progressive difficulty** from basic to advanced
- ✅ **Real-world scenarios** using Bookinfo application

---

## 🚀 **Lab 1: Istio Installation and Setup**

### **Objective**: Install Istio and verify the installation

### **Step 1: Environment Preparation**

**Purpose**: Ensure we have a clean Kubernetes environment ready for Istio

```bash
# Check Kubernetes cluster status
kubectl cluster-info

# Expected Output: Shows cluster info and control plane endpoint

# Check available nodes
kubectl get nodes

# Expected Output: Shows 2-3 worker nodes ready
```

### **Step 2: Download and Install Istio**

**Purpose**: Install Istio using the official installation method

```bash
# Download Istio
curl -L https://istio.io/downloadIstio | sh -

# Expected Output: Downloads Istio and extracts to istio-* directory

# Add Istio to PATH
cd istio-*
export PATH=$PWD/bin:$PATH

# Expected Output: istioctl command becomes available

# Verify istioctl installation
istioctl version

# Expected Output: Shows Istio version and Kubernetes version
```

### **Step 3: Install Istio with Demo Profile**

**Purpose**: Install Istio with all components for learning

```bash
# Install Istio with demo profile
istioctl install --set profile=demo -y

# Expected Output: 
# ✓ Istio core installed
# ✓ Istiod installed
# ✓ Ingress gateways installed
# ✓ Egress gateways installed
# ✓ Installation complete

# Verify installation
kubectl get pods -n istio-system

# Expected Output: Shows all Istio pods running (istiod, ingressgateway, etc.)
```

### **Step 4: Enable Sidecar Injection**

**Purpose**: Configure automatic sidecar injection for the default namespace

```bash
# Label default namespace for sidecar injection
kubectl label namespace default istio-injection=enabled

# Expected Output: namespace/default labeled

# Verify namespace labeling
kubectl get namespace default --show-labels

# Expected Output: Shows istio-injection=enabled label
```

---

## 🏗️ **Lab 2: Deploy Bookinfo Application**

### **Objective**: Deploy the sample Bookinfo application and understand Istio's impact

### **Step 1: Deploy Bookinfo Application**

**Purpose**: Deploy the sample application to see Istio in action

```bash
# Deploy Bookinfo application
kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml

# Expected Output: Creates all Bookinfo services and deployments

# Check application deployment
kubectl get pods

# Expected Output: Shows all Bookinfo pods running (productpage, details, reviews, ratings)

# Check services
kubectl get svc

# Expected Output: Shows all Bookinfo services
```

### **Step 2: Verify Sidecar Injection**

**Purpose**: Confirm that Istio sidecars are injected into application pods

```bash
# Check pod details to see sidecar containers
kubectl describe pod $(kubectl get pods -l app=productpage -o jsonpath='{.items[0].metadata.name}')

# Expected Output: Shows 2 containers per pod (app + istio-proxy)

# Alternative: Check all pods for sidecars
kubectl get pods -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.spec.containers[*].name}{"\n"}{end}'

# Expected Output: Each pod should show app container + istio-proxy
```

### **Step 3: Apply Gateway and VirtualService**

**Purpose**: Configure external access to the application

```bash
# Apply Gateway configuration
kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml

# Expected Output: Creates Gateway and VirtualService resources

# Check Gateway and VirtualService
kubectl get gateway,virtualservice

# Expected Output: Shows bookinfo-gateway and bookinfo virtualservice
```

### **Step 4: Access the Application**

**Purpose**: Test external access to the Bookinfo application

```bash
# Get external IP and port
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

# Expected Output: Sets INGRESS_HOST and INGRESS_PORT variables

# Test application access
curl -I http://$INGRESS_HOST:$INGRESS_PORT/productpage

# Expected Output: HTTP/1.1 200 OK

# Access application in browser (if available)
echo "Access the application at: http://$INGRESS_HOST:$INGRESS_PORT/productpage"
```

---

## 🚦 **Lab 3: Basic Traffic Management**

### **Objective**: Learn VirtualService and DestinationRule concepts

### **Step 1: Create Service Subsets**

**Purpose**: Define different versions of the reviews service

```bash
# Apply DestinationRule to create subsets
kubectl apply -f samples/bookinfo/networking/destination-rule-all.yaml

# Expected Output: Creates destination rules for all services

# Check DestinationRules
kubectl get destinationrule

# Expected Output: Shows destination rules for productpage, reviews, ratings, details
```

### **Step 2: Apply Traffic Splitting**

**Purpose**: Split traffic between different versions of reviews service

```bash
# Apply traffic splitting VirtualService
kubectl apply -f samples/bookinfo/networking/virtual-service-all-v1.yaml

# Expected Output: Creates VirtualService routing all traffic to v1

# Check VirtualService
kubectl get virtualservice

# Expected Output: Shows virtual services for all Bookinfo services
```

### **Step 3: Test Traffic Routing**

**Purpose**: Verify that traffic is routed correctly

```bash
# Test multiple requests to see traffic distribution
for i in {1..10}; do
  curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage | grep -o "stars: [0-9]*"
  sleep 1
done

# Expected Output: All requests should show "stars: 0" (v1 version)
```

### **Step 4: Implement Canary Deployment**

**Purpose**: Gradually shift traffic to a new version

```bash
# Apply canary deployment (25% to v2, 75% to v1)
kubectl apply -f samples/bookinfo/networking/virtual-service-reviews-75-25.yaml

# Expected Output: Updates VirtualService for reviews

# Test traffic distribution
for i in {1..20}; do
  curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage | grep -o "stars: [0-9]*"
  sleep 1
done

# Expected Output: Mix of "stars: 0" (v1) and "stars: 1" (v2)
```

---

## 🔐 **Lab 4: Security with mTLS**

### **Objective**: Implement mutual TLS for service-to-service communication

### **Step 1: Check Current mTLS Status**

**Purpose**: Understand the default mTLS configuration

```bash
# Check current mTLS mode
kubectl get peerauthentication -n istio-system

# Expected Output: May show default policy or none

# Check DestinationRule for mTLS
kubectl get destinationrule -n istio-system

# Expected Output: May show default policy or none
```

### **Step 2: Enable Strict mTLS**

**Purpose**: Enable strict mutual TLS for all services

```bash
# Create PeerAuthentication for strict mTLS
cat <<EOF | kubectl apply -f -
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
EOF

# Expected Output: peerauthentication.security.istio.io/default created

# Create DestinationRule for mTLS
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: default
  namespace: istio-system
spec:
  host: "*.local"
  trafficPolicy:
    tls:
      mode: ISTIO_MUTUAL
EOF

# Expected Output: destinationrule.networking.istio.io/default created
```

### **Step 3: Verify mTLS Configuration**

**Purpose**: Confirm that mTLS is working correctly

```bash
# Check mTLS status
istioctl analyze

# Expected Output: Should show no issues with mTLS configuration

# Test application still works
curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage | head -5

# Expected Output: Application should still be accessible
```

### **Step 4: Monitor mTLS Traffic**

**Purpose**: Observe encrypted traffic between services

```bash
# Check mTLS metrics
kubectl exec -it $(kubectl get pods -l app=productpage -o jsonpath='{.items[0].metadata.name}') -c istio-proxy -- curl -s http://localhost:15000/stats | grep tls

# Expected Output: Shows TLS-related metrics

# Check connection status
kubectl exec -it $(kubectl get pods -l app=productpage -o jsonpath='{.items[0].metadata.name}') -c istio-proxy -- curl -s http://localhost:15000/clusters | grep reviews

# Expected Output: Shows TLS connections to reviews service
```

---

## 📊 **Lab 5: Observability and Monitoring**

### **Objective**: Set up monitoring and observe application metrics

### **Step 1: Deploy Prometheus and Grafana**

**Purpose**: Set up monitoring stack for Istio

```bash
# Deploy Prometheus
kubectl apply -f samples/addons/prometheus.yaml

# Expected Output: Creates Prometheus deployment and service

# Deploy Grafana
kubectl apply -f samples/addons/grafana.yaml

# Expected Output: Creates Grafana deployment and service

# Check monitoring pods
kubectl get pods -n istio-system | grep -E "(prometheus|grafana)"

# Expected Output: Shows Prometheus and Grafana pods running
```

### **Step 2: Access Grafana Dashboard**

**Purpose**: View Istio metrics in Grafana

```bash
# Port forward Grafana
kubectl -n istio-system port-forward $(kubectl -n istio-system get pods -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000 &

# Expected Output: Starts port forwarding in background

# Access Grafana (if browser available)
echo "Grafana available at: http://localhost:3000"
echo "Default credentials: admin/admin"

# Generate some traffic for metrics
for i in {1..50}; do
  curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage > /dev/null
  sleep 0.1
done

# Expected Output: Generates traffic for monitoring
```

### **Step 3: Deploy Jaeger for Distributed Tracing**

**Purpose**: Set up distributed tracing

```bash
# Deploy Jaeger
kubectl apply -f samples/addons/jaeger.yaml

# Expected Output: Creates Jaeger deployment and service

# Check Jaeger pod
kubectl get pods -n istio-system | grep jaeger

# Expected Output: Shows Jaeger pod running

# Port forward Jaeger
kubectl -n istio-system port-forward $(kubectl -n istio-system get pods -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686 &

# Expected Output: Starts port forwarding in background

# Access Jaeger (if browser available)
echo "Jaeger available at: http://localhost:16686"
```

### **Step 4: Generate Traces**

**Purpose**: Create distributed traces to observe

```bash
# Generate traffic with tracing headers
for i in {1..10}; do
  curl -H "x-request-id: trace-$i" http://$INGRESS_HOST:$INGRESS_PORT/productpage > /dev/null
  sleep 1
done

# Expected Output: Generates traces visible in Jaeger

# Check trace sampling
kubectl exec -it $(kubectl get pods -l app=productpage -o jsonpath='{.items[0].metadata.name}') -c istio-proxy -- curl -s http://localhost:15000/stats | grep tracing

# Expected Output: Shows tracing-related metrics
```

---

## 🔄 **Lab 6: Circuit Breaker and Resilience**

### **Objective**: Implement circuit breaker and test resilience patterns

### **Step 1: Apply Circuit Breaker**

**Purpose**: Configure circuit breaker for the reviews service

```bash
# Apply circuit breaker DestinationRule
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: reviews-circuit-breaker
spec:
  host: reviews
  trafficPolicy:
    outlierDetection:
      consecutive5xxErrors: 5
      interval: 10s
      baseEjectionTime: 30s
      maxEjectionPercent: 10
    connectionPool:
      tcp:
        maxConnections: 100
      http:
        http1MaxPendingRequests: 1024
        maxRequestsPerConnection: 10
EOF

# Expected Output: destinationrule.networking.istio.io/reviews-circuit-breaker created

# Check circuit breaker configuration
kubectl get destinationrule reviews-circuit-breaker -o yaml

# Expected Output: Shows circuit breaker settings
```

### **Step 2: Apply Retry and Timeout Policies**

**Purpose**: Configure retry and timeout for resilience

```bash
# Apply retry and timeout VirtualService
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews-resilience
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
    retries:
      attempts: 3
      perTryTimeout: 2s
    timeout: 3s
EOF

# Expected Output: virtualservice.networking.istio.io/reviews-resilience created

# Check resilience configuration
kubectl get virtualservice reviews-resilience -o yaml

# Expected Output: Shows retry and timeout settings
```

### **Step 3: Test Circuit Breaker**

**Purpose**: Simulate failures and observe circuit breaker behavior

```bash
# Generate traffic to test circuit breaker
for i in {1..20}; do
  curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage > /dev/null
  sleep 0.5
done

# Expected Output: Normal traffic flow

# Check circuit breaker metrics
kubectl exec -it $(kubectl get pods -l app=productpage -o jsonpath='{.items[0].metadata.name}') -c istio-proxy -- curl -s http://localhost:15000/stats | grep outlier

# Expected Output: Shows circuit breaker metrics (may be empty if no failures)
```

---

## 🎯 **Lab 7: Fault Injection and Testing**

### **Objective**: Test application resilience with fault injection

### **Step 1: Apply Fault Injection**

**Purpose**: Inject delays and errors to test resilience

```bash
# Apply fault injection to ratings service
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ratings-fault
spec:
  hosts:
  - ratings
  http:
  - fault:
      delay:
        percentage:
          value: 10
        fixedDelay: 5s
    route:
    - destination:
        host: ratings
        subset: v1
EOF

# Expected Output: virtualservice.networking.istio.io/ratings-fault created

# Test fault injection
for i in {1..10}; do
  echo "Request $i:"
  time curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage > /dev/null
  echo "---"
  sleep 2
done

# Expected Output: Some requests will be delayed by 5 seconds
```

### **Step 2: Apply Error Injection**

**Purpose**: Inject HTTP errors to test error handling

```bash
# Apply error injection
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ratings-error
spec:
  hosts:
  - ratings
  http:
  - fault:
      abort:
        percentage:
          value: 10
        httpStatus: 500
    route:
    - destination:
        host: ratings
        subset: v1
EOF

# Expected Output: virtualservice.networking.istio.io/ratings-error created

# Test error injection
for i in {1..20}; do
  response=$(curl -s -w "%{http_code}" http://$INGRESS_HOST:$INGRESS_PORT/productpage)
  echo "Request $i: HTTP $(echo $response | tail -c 4)"
  sleep 1
done

# Expected Output: Mix of 200 and 500 responses
```

---

## 🔧 **Lab 8: Advanced Traffic Management**

### **Objective**: Implement advanced traffic management features

### **Step 1: Traffic Mirroring**

**Purpose**: Mirror traffic to multiple destinations

```bash
# Apply traffic mirroring
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews-mirror
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 100
    mirror:
      host: reviews
      subset: v2
    mirrorPercent: 10
EOF

# Expected Output: virtualservice.networking.istio.io/reviews-mirror created

# Generate traffic to test mirroring
for i in {1..50}; do
  curl -s http://$INGRESS_HOST:$INGRESS_PORT/productpage > /dev/null
  sleep 0.1
done

# Expected Output: 10% of traffic mirrored to v2
```

### **Step 2: Header-Based Routing**

**Purpose**: Route traffic based on request headers

```bash
# Apply header-based routing
cat <<EOF | kubectl apply -f -
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews-header
spec:
  hosts:
  - reviews
  http:
  - match:
    - headers:
        end-user:
          exact: jason
    route:
    - destination:
        host: reviews
        subset: v2
  - route:
    - destination:
        host: reviews
        subset: v1
EOF

# Expected Output: virtualservice.networking.istio.io/reviews-header created

# Test header-based routing
curl -H "end-user: jason" http://$INGRESS_HOST:$INGRESS_PORT/productpage | grep -o "stars: [0-9]*"

# Expected Output: "stars: 1" (v2 version)

curl http://$INGRESS_HOST:$INGRESS_PORT/productpage | grep -o "stars: [0-9]*"

# Expected Output: "stars: 0" (v1 version)
```

---

## 🧹 **Lab 9: Cleanup and Verification**

### **Objective**: Clean up resources and verify system state

### **Step 1: Remove Application Resources**

**Purpose**: Clean up Bookinfo application

```bash
# Remove Bookinfo application
kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml

# Expected Output: Removes all Bookinfo resources

# Remove networking resources
kubectl delete -f samples/bookinfo/networking/

# Expected Output: Removes Gateway, VirtualService, DestinationRule

# Verify cleanup
kubectl get pods
kubectl get svc
kubectl get virtualservice
kubectl get destinationrule

# Expected Output: Should show no Bookinfo resources
```

### **Step 2: Remove Monitoring Stack**

**Purpose**: Clean up monitoring components

```bash
# Remove monitoring addons
kubectl delete -f samples/addons/

# Expected Output: Removes Prometheus, Grafana, Jaeger

# Verify monitoring cleanup
kubectl get pods -n istio-system | grep -E "(prometheus|grafana|jaeger)"

# Expected Output: Should show no monitoring pods
```

### **Step 3: Uninstall Istio**

**Purpose**: Remove Istio from the cluster

```bash
# Uninstall Istio
istioctl uninstall --purge -y

# Expected Output: Removes all Istio components

# Verify Istio removal
kubectl get pods -n istio-system

# Expected Output: Should show no pods in istio-system namespace

# Remove istio-system namespace
kubectl delete namespace istio-system

# Expected Output: Removes istio-system namespace
```

### **Step 4: Final Verification**

**Purpose**: Ensure complete cleanup

```bash
# Check for any remaining Istio resources
kubectl get crd | grep istio

# Expected Output: May show some CRDs (they persist across uninstall)

# Check cluster status
kubectl cluster-info

# Expected Output: Shows healthy cluster status

# Clean up downloaded Istio
cd ..
rm -rf istio-*

# Expected Output: Removes downloaded Istio files
```

---

## 📚 **Summary and Next Steps**

### **What You've Learned**:

✅ **Istio Installation**: Complete setup and verification
✅ **Application Deployment**: Deploy and configure applications
✅ **Traffic Management**: VirtualService, DestinationRule, Gateway
✅ **Security**: mTLS implementation and verification
✅ **Observability**: Prometheus, Grafana, Jaeger setup
✅ **Resilience**: Circuit breaker, retry, timeout patterns
✅ **Testing**: Fault injection and error simulation
✅ **Advanced Features**: Traffic mirroring, header-based routing
✅ **Cleanup**: Proper resource management

### **Key Concepts Mastered**:

- **Service Mesh Architecture**: Data plane vs control plane
- **Traffic Routing**: VirtualService and DestinationRule patterns
- **Security**: mTLS and service-to-service encryption
- **Observability**: Metrics, tracing, and monitoring
- **Resilience**: Circuit breaker and retry mechanisms
- **Testing**: Fault injection for resilience testing

### **Next Steps**:

1. **Practice**: Repeat labs with different configurations
2. **Explore**: Try advanced Istio features
3. **Real-world**: Apply concepts to your own applications
4. **Certification**: Consider Istio certification paths
5. **Community**: Join Istio community and forums

---

## 🎯 **Troubleshooting Guide**

### **Common Issues and Solutions**:

**Issue**: Pods not starting with sidecar injection
```bash
# Check namespace labeling
kubectl get namespace default --show-labels

# Re-label if needed
kubectl label namespace default istio-injection=enabled --overwrite
```

**Issue**: Gateway not accessible
```bash
# Check Gateway and VirtualService
kubectl get gateway,virtualservice

# Check ingress gateway pod
kubectl get pods -n istio-system -l app=istio-ingressgateway
```

**Issue**: mTLS not working
```bash
# Verify PeerAuthentication
kubectl get peerauthentication -n istio-system

# Check DestinationRule
kubectl get destinationrule -n istio-system
```

**Issue**: Metrics not showing
```bash
# Check Prometheus deployment
kubectl get pods -n istio-system | grep prometheus

# Verify telemetry configuration
kubectl get telemetry -n istio-system
```

This comprehensive lab guide provides hands-on experience with all major Istio concepts, from basic installation to advanced traffic management and security features. 