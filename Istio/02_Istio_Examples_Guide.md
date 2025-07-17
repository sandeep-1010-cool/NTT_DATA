# Istio Examples Guide - Concept-Based Practical Examples

## 🎯 **Learning Approach**

This guide provides **ordered examples** for each Istio concept, aligned with the theory for easy mapping. Each example builds upon the previous one, creating a progressive learning path.

---

## 📚 **Phase 1: Basic Traffic Management Examples**

### 🔹 **Example 1: Simple VirtualService**

**Concept**: Basic traffic routing with VirtualService

**Purpose**: Learn fundamental VirtualService structure and routing

```yaml
# simple-virtualservice.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: productpage
spec:
  hosts:
  - productpage
  http:
  - route:
    - destination:
        host: productpage
        subset: v1
```

**Expected Outcome**: All traffic to productpage routes to v1 subset

**CLI Commands**:
```bash
# Apply the VirtualService
kubectl apply -f simple-virtualservice.yaml

# Check VirtualService status
kubectl get virtualservice productpage

# Test routing
curl -s http://productpage:9080/ | grep -o "stars: [0-9]*"
```

---

### 🔹 **Example 2: Traffic Splitting**

**Concept**: Split traffic between multiple service versions

**Purpose**: Learn traffic splitting for blue-green or canary deployments

```yaml
# traffic-splitting.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
spec:
  hosts:
  - reviews
  http:
  - route:
    - destination:
        host: reviews
        subset: v1
      weight: 75
    - destination:
        host: reviews
        subset: v2
      weight: 25
```

**Expected Outcome**: 75% traffic to v1, 25% to v2

**CLI Commands**:
```bash
# Apply traffic splitting
kubectl apply -f traffic-splitting.yaml

# Test multiple requests to see traffic distribution
for i in {1..10}; do
  curl -s http://productpage:9080/ | grep -o "stars: [0-9]*"
  sleep 1
done
```

---

### 🔹 **Example 3: DestinationRule with Subsets**

**Concept**: Define service subsets and policies

**Purpose**: Learn how to create service subsets and apply policies

```yaml
# destination-rule.yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: reviews
spec:
  host: reviews
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
  - name: v3
    labels:
      version: v3
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

**Expected Outcome**: Service subsets defined with connection pooling

---

## 🏗️ **Phase 2: Gateway and Ingress Examples**

### 🔹 **Example 4: Basic Gateway**

**Concept**: Configure ingress traffic with Gateway

**Purpose**: Learn how to expose services externally

```yaml
# basic-gateway.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: bookinfo-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 80
      name: http
      protocol: HTTP
    hosts:
    - "*"
```

```yaml
# gateway-virtualservice.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: bookinfo
spec:
  hosts:
  - "*"
  gateways:
  - bookinfo-gateway
  http:
  - match:
    - uri:
        prefix: /productpage
    - uri:
        prefix: /static
    - uri:
        exact: /login
    - uri:
        exact: /logout
    - uri:
        prefix: /api/v1/products
    route:
    - destination:
        host: productpage
        port:
          number: 9080
```

**Expected Outcome**: External access to Bookinfo application

**CLI Commands**:
```bash
# Apply Gateway and VirtualService
kubectl apply -f basic-gateway.yaml
kubectl apply -f gateway-virtualservice.yaml

# Get external IP
export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')

# Access application
curl -I http://$INGRESS_HOST:$INGRESS_PORT/productpage
```

---

### 🔹 **Example 5: TLS Gateway**

**Concept**: Secure ingress with TLS

**Purpose**: Learn how to configure HTTPS ingress

```yaml
# tls-gateway.yaml
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: bookinfo-tls-gateway
spec:
  selector:
    istio: ingressgateway
  servers:
  - port:
      number: 443
      name: https
      protocol: HTTPS
    tls:
      mode: SIMPLE
      serverCertificate: /etc/istio/ingressgateway-certs/tls.crt
      privateKey: /etc/istio/ingressgateway-certs/tls.key
    hosts:
    - bookinfo.com
```

**Expected Outcome**: HTTPS access to application

---

## 🔐 **Phase 3: Security Examples**

### 🔹 **Example 6: mTLS Configuration**

**Concept**: Enable mutual TLS for service-to-service communication

**Purpose**: Learn how to secure service communication

```yaml
# mtls-peer-authentication.yaml
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: istio-system
spec:
  mtls:
    mode: STRICT
```

```yaml
# mtls-destination-rule.yaml
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
```

**Expected Outcome**: All service-to-service communication encrypted

**CLI Commands**:
```bash
# Apply mTLS configuration
kubectl apply -f mtls-peer-authentication.yaml
kubectl apply -f mtls-destination-rule.yaml

# Verify mTLS is working
istioctl analyze
```

---

### 🔹 **Example 7: Authorization Policy**

**Concept**: Control access to services

**Purpose**: Learn how to implement service-level authorization

```yaml
# authorization-policy.yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: productpage-policy
  namespace: default
spec:
  selector:
    matchLabels:
      app: productpage
  rules:
  - from:
    - source:
        principals: ["cluster.local/ns/default/sa/bookinfo-productpage"]
    to:
    - operation:
        methods: ["GET"]
        paths: ["/api/v1/products*"]
```

**Expected Outcome**: Only authorized services can access productpage API

---

## 📊 **Phase 4: Observability Examples**

### 🔹 **Example 8: Telemetry Configuration**

**Concept**: Configure metrics and tracing

**Purpose**: Learn how to enable observability features

```yaml
# telemetry.yaml
apiVersion: telemetry.istio.io/v1alpha1
kind: Telemetry
metadata:
  name: mesh-default
  namespace: istio-system
spec:
  metrics:
  - providers:
    - name: prometheus
  tracing:
  - providers:
    - name: jaeger
    randomSamplingPercentage: 10.0
```

**Expected Outcome**: Metrics sent to Prometheus, traces to Jaeger

---

### 🔹 **Example 9: Custom Metrics**

**Concept**: Define custom metrics for monitoring

**Purpose**: Learn how to create application-specific metrics

```yaml
# custom-metrics.yaml
apiVersion: telemetry.istio.io/v1alpha1
kind: Telemetry
metadata:
  name: custom-metrics
  namespace: default
spec:
  metrics:
  - providers:
    - name: prometheus
    overrides:
    - match:
        metric: REQUEST_COUNT
        mode: CLIENT_AND_SERVER
      tagOverrides:
        custom_tag:
          operation: UPSERT
          value: "custom_value"
```

**Expected Outcome**: Custom metrics available in Prometheus

---

## 🔄 **Phase 5: Resilience Examples**

### 🔹 **Example 10: Circuit Breaker**

**Concept**: Implement circuit breaker pattern

**Purpose**: Learn how to prevent cascading failures

```yaml
# circuit-breaker.yaml
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: circuit-breaker
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
```

**Expected Outcome**: Circuit breaker prevents requests to failing services

---

### 🔹 **Example 11: Retry and Timeout**

**Concept**: Configure retry and timeout policies

**Purpose**: Learn how to handle transient failures

```yaml
# retry-timeout.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
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
```

**Expected Outcome**: Automatic retries with timeout limits

---

## 🎯 **Phase 6: Advanced Traffic Management**

### 🔹 **Example 12: Fault Injection**

**Concept**: Test service resilience with fault injection

**Purpose**: Learn how to simulate failures

```yaml
# fault-injection.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: ratings
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
```

**Expected Outcome**: 10% of requests to ratings service delayed by 5 seconds

---

### 🔹 **Example 13: Traffic Mirroring**

**Concept**: Mirror traffic to multiple destinations

**Purpose**: Learn how to send traffic to multiple services

```yaml
# traffic-mirroring.yaml
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: reviews
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
```

**Expected Outcome**: 10% of traffic mirrored to v2 subset

---

## 🔧 **Phase 7: Advanced Security**

### 🔹 **Example 14: JWT Authentication**

**Concept**: Implement JWT-based authentication

**Purpose**: Learn how to authenticate requests

```yaml
# jwt-authentication.yaml
apiVersion: security.istio.io/v1beta1
kind: RequestAuthentication
metadata:
  name: jwt-example
  namespace: default
spec:
  selector:
    matchLabels:
      app: productpage
  jwtRules:
  - issuer: "https://accounts.google.com"
    audiences:
    - "bookinfo.com"
    jwksUri: "https://www.googleapis.com/oauth2/v1/certs"
```

```yaml
# jwt-authorization.yaml
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: jwt-policy
  namespace: default
spec:
  selector:
    matchLabels:
      app: productpage
  rules:
  - from:
    - source:
        requestPrincipals: ["*"]
    to:
    - operation:
        methods: ["GET"]
        paths: ["/api/v1/products*"]
```

**Expected Outcome**: Only authenticated requests can access protected endpoints

---

## 📊 **Phase 8: Monitoring and Alerting**

### 🔹 **Example 15: Prometheus Configuration**

**Concept**: Configure Prometheus for Istio metrics

**Purpose**: Learn how to collect and visualize metrics

```yaml
# prometheus-config.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: prometheus-config
  namespace: istio-system
data:
  prometheus.yml: |
    global:
      scrape_interval: 15s
    scrape_configs:
    - job_name: 'istio-mesh'
      kubernetes_sd_configs:
      - role: endpoints
        namespaces:
          names:
          - istio-system
      relabel_configs:
      - source_labels: [__meta_kubernetes_service_name]
        regex: 'istio-mixer'
        action: keep
```

**Expected Outcome**: Prometheus collects Istio metrics

---

### 🔹 **Example 16: Grafana Dashboard**

**Concept**: Create custom Grafana dashboards

**Purpose**: Learn how to visualize Istio metrics

```yaml
# grafana-dashboard.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: istio-dashboard
  namespace: istio-system
data:
  istio-dashboard.json: |
    {
      "dashboard": {
        "title": "Istio Service Dashboard",
        "panels": [
          {
            "title": "Request Rate",
            "type": "graph",
            "targets": [
              {
                "expr": "rate(istio_requests_total[5m])",
                "legendFormat": "{{destination_service}}"
              }
            ]
          }
        ]
      }
    }
```

**Expected Outcome**: Custom Grafana dashboard for Istio monitoring

---

## 📚 **Summary**

This examples guide provides:
- ✅ **Progressive learning** from basic to advanced concepts
- ✅ **Real-world scenarios** for each Istio feature
- ✅ **Complete configurations** with explanations
- ✅ **Best practices** integrated throughout
- ✅ **Practical outcomes** for each example

**Next Steps**: Use these examples as reference for hands-on labs in Killercoda. 