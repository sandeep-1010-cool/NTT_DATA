# Istio Documentation Images

This directory contains placeholder files for the images used in the Istio documentation. Each file contains a description of what the actual image should show.

## Image Files and Their Purposes

### 1. microservices-challenges.png
**Location:** Used in `01_Introduction_to_Service_Mesh.md`
**Purpose:** Illustrates the challenges of microservice architecture, showing how each microservice is burdened with non-business logic components.

### 2. service-mesh-solution.png
**Location:** Used in `02_Service_Mesh_Solution.md`
**Purpose:** Shows the service mesh solution using the sidecar pattern with Control Plane managing Pods containing Proxy and Service components.

### 3. traffic-splitting-canary.png
**Location:** Used in `02_Service_Mesh_Solution.md`
**Purpose:** Illustrates traffic splitting for canary deployment, showing how traffic is distributed between different service versions.

### 4. istio-architecture.png
**Location:** Used in `02_Service_Mesh_Solution.md`
**Purpose:** Shows the complete Istio architecture with Control Plane (Istiod) managing the Data Plane (Envoy proxies).

### 5. istio-configuration-approach.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Explains how Istio configuration leverages Kubernetes YAML files and Custom Resource Definitions (CRDs).

### 6. istio-configuration-flow.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Shows the Istio configuration flow from CRDs through Istiod to Envoy proxies.

### 7. istio-config-philosophy.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Emphasizes the key principle: "We don't configure Proxies, we configure Istiod."

### 8. istio-service-discovery.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Illustrates dynamic service discovery in Istio, showing automatic service registration.

### 9. istio-security-certificates.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Shows Istio's security capabilities through certificate management and mutual TLS.

### 10. istio-metrics-tracing.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Demonstrates how Istio gathers telemetry data from Envoy proxies.

### 11. istio-ingress-gateway.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Shows the Istio Ingress Gateway as the entrypoint to the cluster.

### 12. istio-traffic-flow.png
**Location:** Used in `03_Istio_Configuration.md`
**Purpose:** Illustrates the complete Istio traffic flow from user request through services and back.

## Note
These are placeholder files containing descriptions of what the actual images should show. To create the actual images, you would need to use a diagramming tool (like draw.io, Lucidchart, or similar) to create the visual representations based on the descriptions in each file. 