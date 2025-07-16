# Istio & Service Mesh - Complete Notes

## 1. Introduction to Service Mesh

### What is Service Mesh?
- **Service Mesh** is a popular solution for managing communication between individual microservices in a microservice application
- It's a dedicated tool designed to solve the challenges of microservices communication

### Why Do We Need Service Mesh?

When moving from **monolith to microservices**, we introduce new challenges that didn't exist with monolithic applications.

#### Example: Online Shop Application
**Microservices Components:**
- Web Server (handles UI requests)
- Payment Microservice (handles payment logic)
- Shopping Cart
- Product Inventory
- Database
- Additional services

**Deployment:** Kubernetes cluster

---

## 2. Microservices Challenges

### Challenge 1: Service Communication Configuration
**Problem:** How do services know how to talk to each other?

**Requirements:**
- Each service needs to know the endpoints of other services
- When adding a new microservice, endpoints must be configured in all services that need to communicate with it
- This configuration becomes part of the application deployment code

**Example Flow:**
1. User puts items in shopping cart
2. Request received by web server
3. Web server hands over to shopping cart microservice
4. Shopping cart talks to database to persist data

### Challenge 2: Security
**Current Security Model:**
- Firewall rules around Kubernetes cluster
- Proxy as entry point (cluster can't be accessed directly)
- **Problem:** Once inside cluster, communication is insecure
- Services communicate over HTTP or other insecure protocols
- No restrictions on inter-service communication
- If attacker gets inside cluster, they can access everything

**Security Requirements:**
- Higher security needed for important applications (online banks, apps with personal data)
- Additional configuration needed to secure communication between services
- Each service needs security implementation

### Challenge 3: Retry Logic
**Requirement:** Each microservice needs retry logic for robustness
- Handle unreachable services
- Handle temporary connection losses
- Developers must add retry logic to each service

### Challenge 4: Monitoring and Metrics
**Required Metrics:**
- Service performance monitoring
- HTTP error tracking
- Request volume (receiving/sending)
- Request duration for bottleneck identification

**Implementation:**
- Prometheus client library for monitoring
- Zipkin for tracing data
- Each service needs monitoring logic

### Challenge 5: Developer Burden
**Problems:**
- Developers spend time on network logic instead of business logic
- Complexity added to services instead of keeping them lightweight
- Each microservice needs individual configuration for all these features 