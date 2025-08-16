# Istio on OpenShift — Hands‑On Lab (Step‑by‑Step with Commands)

> **Goal:** Stand up OpenShift Service Mesh (Istio), deploy sample services, enforce Zero Trust, control ingress/egress, and observe traffic.
>
> **Format:** Small steps → verify → next. Copy‑paste friendly. Works on OpenShift 4.12+ with OpenShift Service Mesh (OSSM) v2.4–v2.6.

---

## 0) Assumptions & Variables (edit these first)

```bash
# ---- EDIT THESE ----
export OCP_PROJECT="demo-mesh"
export MESH_NS="istio-system"           # control plane ns
export APPS_DOMAIN="apps.example.com"   # e.g., apps.<cluster-domain>
export GATEWAY_HOST="httpbin.${APPS_DOMAIN}"
```

---

## 1) Create Project & Confirm Operators

> Operators needed: **Kiali Operator**, **Jaeger Operator**, **OpenShift Service Mesh Operator**.

```bash
# Create your app namespace
oc new-project "$OCP_PROJECT" || true

# Check operators installed (namespaces may vary)
oc get csv -A | egrep -i "(servicemesh|kiali|jaeger)"
```

> If operators are missing, install them via **OperatorHub** (Web Console) or your cluster admin.

---

## 2) Install the Mesh Control Plane (SMCP) & Member Roll (SMMR)

Create control plane and add your project to the mesh.

```bash
cat > smcp.yaml <<'EOF'
apiVersion: maistra.io/v2
kind: ServiceMeshControlPlane
metadata:
  name: basic
  namespace: ${MESH_NS}
spec:
  version: v2.5              # adjust to your installed OSSM version
  policy:
    type: Istiod
  telemetry:
    type: Istiod
EOF

cat > smmr.yaml <<'EOF'
apiVersion: maistra.io/v1
kind: ServiceMeshMemberRoll
metadata:
  name: default
  namespace: ${MESH_NS}
spec:
  members:
    - ${OCP_PROJECT}
EOF

# Create the control plane namespace if needed
oc get ns "$MESH_NS" >/dev/null 2>&1 || oc create ns "$MESH_NS"

# Apply control plane & member roll
oc apply -f smcp.yaml
oc apply -f smmr.yaml

# Watch control plane come up
oc -n "$MESH_NS" get pods -w
```

Wait until **istiod**, **istio-ingressgateway**, and any **jaeger/kiali** pods are **Running**.

---

## 3) Deploy Sample Apps (httpbin & sleep) — Sidecar Injection

```bash
cat > httpbin.yaml <<'EOF'
apiVersion: v1
kind: ServiceAccount
metadata:
  name: httpbin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
spec:
  selector:
    matchLabels:
      app: httpbin
  template:
    metadata:
      labels:
        app: httpbin
    spec:
      serviceAccountName: httpbin
      containers:
        - name: httpbin
          image: docker.io/kennethreitz/httpbin
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
spec:
  selector:
    app: httpbin
  ports:
    - name: http
      port: 8000
      targetPort: 80
EOF

cat > sleep.yaml <<'EOF'
apiVersion: v1
kind: ServiceAccount
metadata:
  name: sleep
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sleep
spec:
  selector:
    matchLabels:
      app: sleep
  template:
    metadata:
      labels:
        app: sleep
    spec:
      serviceAccountName: sleep
      containers:
        - name: sleep
          image: curlimages/curl:8.9.0
          command: ["/bin/sh","-c","sleep 3650000"]
EOF

oc -n "$OCP_PROJECT" apply -f httpbin.yaml
oc -n "$OCP_PROJECT" apply -f sleep.yaml

# Verify sidecars (READY should be 2/2)
oc -n "$OCP_PROJECT" get pods -o wide
```

> If READY isn’t 2/2, ensure your project is listed in **SMMR** and pods restart (delete pods to trigger injection).

---

## 4) Enforce Zero Trust — mTLS + Deny‑All + Allow Rules

```bash
# Enable mTLS STRICT for the namespace
cat > peerauth.yaml <<'EOF'
apiVersion: security.istio.io/v1beta1
kind: PeerAuthentication
metadata:
  name: default
  namespace: ${OCP_PROJECT}
spec:
  mtls:
    mode: STRICT
EOF

# Deny all by default
cat > deny-all.yaml <<'EOF'
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: default-deny
  namespace: ${OCP_PROJECT}
spec: {}
EOF

# Allow sleep -> httpbin
cat > allow-sleep-httpbin.yaml <<'EOF'
apiVersion: security.istio.io/v1beta1
kind: AuthorizationPolicy
metadata:
  name: httpbin-allow-sleep
  namespace: ${OCP_PROJECT}
spec:
  selector:
    matchLabels:
      app: httpbin
  rules:
    - from:
        - source:
            principals: ["cluster.local/ns/${OCP_PROJECT}/sa/sleep"]
EOF

oc apply -f peerauth.yaml
oc apply -f deny-all.yaml
oc apply -f allow-sleep-httpbin.yaml

# Test (should succeed)
oc -n "$OCP_PROJECT" exec deploy/sleep -- curl -sS http://httpbin:8000/get | head -n 5

# Negative test (remove allow and watch it fail)
# oc -n "$OCP_PROJECT" delete authorizationpolicy httpbin-allow-sleep
# oc -n "$OCP_PROJECT" exec deploy/sleep -- curl -sS -m 3 http://httpbin:8000/get || echo "DENIED as expected"
```

---

## 5) Control Egress — Block Unknown, Allow Specific

```bash
# Egress scope: only known (registry) destinations
cat > sidecar-egress.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: Sidecar
metadata:
  name: default
  namespace: ${OCP_PROJECT}
spec:
  outboundTrafficPolicy:
    mode: REGISTRY_ONLY
  egress:
    - hosts:
        - "./*"            # same namespace
        - "${MESH_NS}/*"   # mesh system
EOF

oc apply -f sidecar-egress.yaml

# Try an external call (should FAIL)
oc -n "$OCP_PROJECT" exec deploy/sleep -- curl -I -m 3 https://api.github.com || echo "blocked"

# Allow a specific external API via ServiceEntry
cat > se-github.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: ServiceEntry
metadata:
  name: allow-github
  namespace: ${OCP_PROJECT}
spec:
  hosts:
    - api.github.com
  ports:
    - number: 443
      name: https
      protocol: TLS
  resolution: DNS
EOF

oc apply -f se-github.yaml

# Retry (should SUCCEED now)
oc -n "$OCP_PROJECT" exec deploy/sleep -- curl -I https://api.github.com
```

---

## 6) Expose Externally — Gateway + VirtualService (+ OpenShift Route)

```bash
# Gateway (edge listener)
cat > gw-httpbin.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: Gateway
metadata:
  name: httpbin-gw
  namespace: ${OCP_PROJECT}
spec:
  selector:
    istio: ingressgateway
  servers:
    - port:
        number: 80
        name: http
        protocol: HTTP
      hosts:
        - ${GATEWAY_HOST}
EOF

# VirtualService (routing rules)
cat > vs-httpbin.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: httpbin
  namespace: ${OCP_PROJECT}
spec:
  hosts:
    - ${GATEWAY_HOST}
  gateways:
    - httpbin-gw
  http:
    - match:
        - uri:
            prefix: /
      route:
        - destination:
            host: httpbin.${OCP_PROJECT}.svc.cluster.local
            port:
              number: 8000
EOF

oc apply -f gw-httpbin.yaml
oc apply -f vs-httpbin.yaml

# Create OpenShift Route to the istio-ingressgateway (HTTP passthrough or edge TLS as needed)
# Simple HTTP test route (for demo)
oc -n "$MESH_NS" create route edge httpbin --service=istio-ingressgateway \
  --hostname="${GATEWAY_HOST}" --port=http2 || true

# Test from outside (your terminal)
curl -I "http://${GATEWAY_HOST}"
```

> For HTTPS + real certs, integrate **cert-manager** or use OpenShift Route with passthrough to HTTPS on ingressgateway.

---

## 7) Traffic Management — Canary Split (90/10)

```bash
# Create a second deployment labeled version=v2
cat > httpbin-v2.yaml <<'EOF'
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v2
  namespace: ${OCP_PROJECT}
spec:
  selector:
    matchLabels:
      app: httpbin
      version: v2
  template:
    metadata:
      labels:
        app: httpbin
        version: v2
    spec:
      serviceAccountName: httpbin
      containers:
        - name: httpbin
          image: docker.io/kennethreitz/httpbin
          ports:
            - containerPort: 80
EOF

# DestinationRule with subsets v1/v2
cat > dr-httpbin.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: httpbin
  namespace: ${OCP_PROJECT}
spec:
  host: httpbin.${OCP_PROJECT}.svc.cluster.local
  subsets:
    - name: v1
      labels:
        app: httpbin
        version: v1
    - name: v2
      labels:
        app: httpbin
        version: v2
EOF

# Update VirtualService to split 90/10
cat > vs-httpbin-canary.yaml <<'EOF'
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: httpbin
  namespace: ${OCP_PROJECT}
spec:
  hosts: ["${GATEWAY_HOST}"]
  gateways: ["httpbin-gw"]
  http:
    - route:
        - destination:
            host: httpbin.${OCP_PROJECT}.svc.cluster.local
            subset: v1
          weight: 90
        - destination:
            host: httpbin.${OCP_PROJECT}.svc.cluster.local
            subset: v2
          weight: 10
EOF

oc -n "$OCP_PROJECT" apply -f httpbin-v2.yaml
oc -n "$OCP_PROJECT" apply -f dr-httpbin.yaml
oc -n "$OCP_PROJECT" apply -f vs-httpbin-canary.yaml

# Generate traffic & observe
for i in {1..20}; do curl -s "http://${GATEWAY_HOST}/headers" | grep -E 'Server|Via' || true; done
```

---

## 8) Observability — Kiali, Jaeger, Metrics

```bash
# Kiali route (namespace may vary)
oc -n "$MESH_NS" get routes | grep -i kiali
# Jaeger route
oc -n "$MESH_NS" get routes | grep -i jaeger
```

Open **Kiali** → Graph → select namespaces **\$MESH\_NS** and **\$OCP\_PROJECT** → verify flows, error rates, and **mTLS locks**. In **Jaeger**, search for service `httpbin` to view traces.

---

## 9) Troubleshooting Quick Commands

```bash
# Mesh control plane
oc -n "$MESH_NS" get pods

# App policies
oc -n "$OCP_PROJECT" get authorizationpolicy,peerauthentication,sidecar,virtualservice,destinationrule,serviceentry

# Sidecar logs (access logs)
oc -n "$OCP_PROJECT" logs deploy/httpbin -c istio-proxy --tail=100

# (If istioctl is available)
istioctl proxy-status
istioctl pc routes deploy/httpbin.${OCP_PROJECT} -n ${OCP_PROJECT}
istioctl pc clusters deploy/httpbin.${OCP_PROJECT} -n ${OCP_PROJECT}
istioctl pc listeners deploy/httpbin.${OCP_PROJECT} -n ${OCP_PROJECT}
```

---

## 10) Cleanup

```bash
oc delete ns "$OCP_PROJECT"
# Or delete selectively
# oc -n "$OCP_PROJECT" delete -f vs-httpbin-canary.yaml -f dr-httpbin.yaml -f httpbin-v2.yaml
# oc -n "$OCP_PROJECT" delete -f gw-httpbin.yaml -f vs-httpbin.yaml -f sidecar-egress.yaml -f se-github.yaml
# oc -n "$OCP_PROJECT" delete -f peerauth.yaml -f deny-all.yaml -f allow-sleep-httpbin.yaml -f httpbin.yaml -f sleep.yaml
# oc -n "$MESH_NS" delete -f smmr.yaml -f smcp.yaml
```

---

### ✅ You’re Done

You have:

* Mesh control plane running
* Sidecar‑injected sample apps
* **Zero Trust** enforced with mTLS + AuthZ
* **Egress** locked down with explicit allow
* **Ingress** via Gateway + Route
* **Canary** split 90/10
* Observability via **Kiali/Jaeger**

> Need a **Confluence‑ready** version (with collapsible sections)? Say the word and I’ll reformat with headings and callouts.
