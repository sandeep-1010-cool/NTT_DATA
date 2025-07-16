# Istio Documentation Images

This directory contains the image files referenced in the Istio documentation. The images illustrate key concepts of service mesh architecture, Istio configuration, and traffic flow.

## Image Files

### 1. microservices-challenges.png
**Description:** Challenges of a microservice architecture, illustrating the complexities arising from non-business logic within each microservice.

**Content:**
- Four identical blue rectangular boxes representing Pods
- Each Pod contains a microservice with Business Logic (BL), Security (SEC), Communication (COMM), Retry (R), and other functionalities
- Three human-like icons connected by lines, symbolizing interactions
- Challenges listed: "These NON business logic must be added to each application", "Developers don't work on actual service/application logic", "adds complexity to the services"
- A sad face emoji representing the negative sentiment
- Detailed breakdown showing business logic vs non-business logic components

**Used in:** `01_Introduction_to_Service_Mesh.md` - Section 2: Microservices Challenges

### 2. service-mesh-solution.png
**Description:** Solution: Service Mesh with Sidecar Pattern, illustrating the architecture of a service mesh.

**Content:**
- Large teal-colored "Control Plane" rectangle at the top
- Three identical blue-bordered rectangles representing Pods
- Each Pod contains two stacked white rectangles: "Proxy" and "Service"
- Horizontal double-headed arrows connecting the Proxy components
- Light gray oval labeled "network layer" in the background

**Used in:** `02_Service_Mesh_Solution.md` - Section 3: How Service Mesh Solves These Challenges

### 3. traffic-splitting-canary.png
**Description:** Traffic Splitting core feature, specifically demonstrating a Canary Deployment strategy.

**Content:**
- Title: "Core feature: Traffic Splitting" with "Canary Deployment" highlighted
- Webserver Service on the right
- Payment Service on the top left with payment icon
- Three service instances: two labeled "Version 2.0" and one "Version 3.0"
- Traffic flow arrows: 90% to Version 2.0, 10% to Version 3.0
- Benefit statement: "release new versions without worrying about breaking the application"

**Used in:** `02_Service_Mesh_Solution.md` - Section 4: Traffic Split Feature

### 4. istio-architecture.png
**Description:** Istio Architecture, divided into Control Plane and Data Plane components.

**Content:**
- Top section: Control Plane with Istiod (Pilot, Citadel, Galley)
- Bottom section: Data Plane with three Pods, each containing Envoy proxy and Service
- Black lines connecting Control Plane to Data Plane
- Green "Mesh traffic" arrows connecting Envoy proxies
- Descriptive text: "configuration, discovery, certificates"

**Used in:** `02_Service_Mesh_Solution.md` - Section 5: Istio Implementation

### 5. istio-configuration-approach.png
**Description:** How to configure Istio? - Explains Istio configuration using Kubernetes YAML files and CRDs.

**Content:**
- Title: "How to configure Istio?"
- Two key points with thumbs-up icons:
  - "Istio is configured with Kubernetes YAML files"
  - "no need to learn Istio specific language"
- Prominent "CRD" box with three bullet points explaining Custom Resource Definitions
- Kubernetes logo and YAML document icons

**Used in:** `03_Istio_Configuration.md` - Section 6: Istio Configuration

### 6. istio-configuration-flow.png
**Description:** Istio configuration flow showing how CRDs are processed by Istiod and applied to Envoy proxies.

**Content:**
- Left side: CRD concepts (traffic routing, service communication, traffic split, retry rules)
- Middle top: Three YAML document icons pointing to Istiod
- Middle: Istiod box with Pilot, Citadel, Galley components
- Bottom: Three Pods with Envoy proxies and Services
- Black arrows showing configuration flow from Istiod to proxies
- Green "Mesh traffic" connections between proxies

**Used in:** `03_Istio_Configuration.md` - Section 6: Istio Configuration

### 7. istio-config-philosophy.png
**Description:** Istio configuration philosophy emphasizing that we configure Istiod, not proxies directly.

**Content:**
- Text box: "We don't configure Proxies, we configure Istiod"
- Three configuration inputs: Virtual Service YAML, Destination Rule YAML, other YAML files
- Black arrows pointing to Istiod (Pilot, Citadel, Galley)
- Three Pods with Envoy proxies and Services
- Green "Mesh traffic" connections
- Note: "Proxies can communicate without connecting to Istiod"

**Used in:** `03_Istio_Configuration.md` - Section 6: Istio Configuration

### 8. istio-service-discovery.png
**Description:** Dynamic Service Discovery within Istio service mesh.

**Content:**
- Title: "Dynamic Service discovery"
- Left side: Istio capabilities (configuration, service discovery)
- Text: "Internal Registry for Services & their endpoints"
- Benefit: "new microservice gets registered automatically"
- Istiod control plane with Pilot, Citadel, Galley
- Three Pods with Envoy proxies and Services
- Black lines connecting control plane to data plane

**Used in:** `03_Istio_Configuration.md` - Section 7: Istio Control Plane Features

### 9. istio-security-certificates.png
**Description:** Security - Certificate Management in Istio.

**Content:**
- Title: "Security - Certificate Management"
- Istio features: configuration, service discovery, certificate management
- Benefit: "secure TLS communication between microservices"
- Istiod control plane with Pilot, Citadel, Galley
- Three Pods with Envoy proxies and Services
- Light blue "mTLS" arrows between proxies
- Footer: "features: Service Discovery, Security, Metrics & Tracing"

**Used in:** `03_Istio_Configuration.md` - Section 7: Istio Control Plane Features

### 10. istio-metrics-tracing.png
**Description:** Metrics and Tracing in Istio service mesh.

**Content:**
- Title: "Metrics and Tracing"
- Istio features: configuration, service discovery, certificate management, gather telemetry data
- Istiod control plane with Pilot, Citadel, Galley
- Three Pods with Envoy proxies and Services
- Teal arrows labeled "Metrics" connecting control plane to proxies
- Pink hexagonal icons on connection points

**Used in:** `03_Istio_Configuration.md` - Section 7: Istio Control Plane Features

### 11. istio-ingress-gateway.png
**Description:** Istio Ingress Gateway as entrypoint to the cluster.

**Content:**
- Title: "Istio Ingress Gateway"
- Text: "entrypoint to your cluster"
- External "Cluster Request" entering "Ingress Gateway"
- Kubernetes cluster boundary with Kubernetes logo
- Istiod control plane with Pilot, Citadel, Galley
- Three Pods with Envoy proxies and Services
- Green "Mesh traffic" connections
- Virtual Service configuration connected to Gateway

**Used in:** `03_Istio_Configuration.md` - Section 8: Istio Ingress Gateway

### 12. istio-traffic-flow.png
**Description:** Istio Traffic Flow showing complete request flow through the service mesh.

**Content:**
- Title: "Istio: Traffic Flow"
- User icon initiating "Request"
- Istio Gateway as entry point
- Istiod Control Plane with Istio and Kubernetes logos
- Three Pods: Webserver, Payment, Database
- White arrows showing request flow
- Black oval: "Gather metrics data and send to Control Plane"
- Black lines connecting proxies to control plane

**Used in:** `03_Istio_Configuration.md` - Section 9: Complete Traffic Flow

## Usage

These images are referenced in the markdown files using relative paths like:
```markdown
![Image Description](images/filename.png)
```

The images help visualize complex Istio concepts and make the documentation more accessible and easier to understand. 