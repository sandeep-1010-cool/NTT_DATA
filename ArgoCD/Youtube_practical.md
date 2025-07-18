### **Step-by-Step Demo: Setting Up ArgoCD for Kubernetes Configuration Management**

#### **1. Prerequisites**
- **Minikube Cluster**: An empty Kubernetes cluster.
- **Configuration Repository**: Contains:
  - A `dev` folder with Kubernetes manifest files (`deployment.yaml` and `service.yaml`).
  - Deployment YAML using image `1.0` hosted publicly on Docker Hub.
- **Application Source Code Repository**: Used to build the Docker images (not relevant for deployment steps).

---

### **Step 1: Install ArgoCD in Kubernetes**

#### **Commands to Execute**
1. **Create ArgoCD Namespace**:
   ```bash
   kubectl create namespace argocd
   ```

2. **Install ArgoCD**:
   Apply the official installation YAML directly:
   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. **Verify Components**:
   Check if ArgoCD pods are running:
   ```bash
   kubectl get pods -n argocd
   ```

#### **Expected Output**:
You should see several pods like `argocd-server`, `argocd-repo-server`, and others in a **Running** state.

---

### **Step 2: Access ArgoCD UI**

#### **Commands to Execute**
1. **Port Forward ArgoCD Server**:
   Forward the ArgoCD server service to localhost:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

2. **Access UI**:
   Open your browser and navigate to:
   ```
   https://localhost:8080
   ```

3. **Log In**:
   - Username: `admin`
   - Password: Retrieve using:
     ```bash
     kubectl get secret argocd-initial-admin-secret -n argocd -o yaml
     ```
   - Decode the password:
     ```bash
     echo <base64-password> | base64 -d
     ```

#### **Expected Output**:
You should see the ArgoCD dashboard, which will initially be empty (no applications).

---

### **Step 3: Configure ArgoCD Application**

#### **Create `application.yaml`**
1. **Navigate to the Repository**:
   Clone the configuration repository and navigate to the `dev` folder:
   ```bash
   git clone <repository-url>
   cd dev
   ```

2. **Create `application.yaml`**:
   Open the folder in Visual Studio Code and create a file named `application.yaml`.

3. **Define Application YAML**:
   Add the following configuration:
   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: my-app
     namespace: argocd
   spec:
     project: default
     source:
       repoURL: https://github.com/your-repo/k8s-configs
       targetRevision: HEAD
       path: dev
     destination:
       server: https://kubernetes.default.svc
       namespace: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
   ```

#### **Explanation of Fields**:
- **Source**:
  - `repoURL`: URL of the Git repository hosting Kubernetes manifests.
  - `targetRevision`: Points to the latest commit (`HEAD`).
  - `path`: Specifies the folder to track (`dev` folder).
- **Destination**:
  - `server`: DNS name of the Kubernetes API server (`kubernetes.default.svc`).
  - `namespace`: Target namespace (`default`).
- **SyncPolicy**:
  - `automated`: Enables automatic syncing and self-healing.

---

### **Step 4: Apply Application YAML**

#### **Commands to Execute**
1. **Apply Configuration**:
   ```bash
   kubectl apply -f application.yaml
   ```

2. **Verify Application**:
   Check ArgoCD UI to confirm the application is created and synced:
   ```bash
   kubectl get applications -n argocd
   ```

#### **Expected Output**:
The application should appear in the ArgoCD dashboard and start syncing resources from the Git repository.

---

### **Step 5: Update Deployment in Git Repository**

#### **Steps to Update**
1. **Modify Deployment YAML**:
   Update the image version from `1.0` to `2.0`:
   ```yaml
   spec:
     containers:
     - name: my-app
       image: your-docker-repo/my-app:2.0
   ```

2. **Commit Changes**:
   Push the updated YAML file to the Git repository:
   ```bash
   git add deployment.yaml
   git commit -m "Update image to version 2.0"
   git push origin main
   ```

#### **Expected Behavior**:
ArgoCD will detect the changes in the Git repository and automatically sync them to the Kubernetes cluster. Verify the updated deployment:
```bash
kubectl get pods -n default
```

---

### **Step 6: Verify Deployment**

#### **Commands to Execute**
1. **Check Resources**:
   List all resources in the default namespace:
   ```bash
   kubectl get all -n default
   ```

2. **Access Application**:
   If the application includes a service, forward the service port to access it locally:
   ```bash
   kubectl port-forward svc/my-app-service 8080:80
   ```

---

### **Summary of Commands**

| **Step**                     | **Command**                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| Create ArgoCD Namespace       | `kubectl create namespace argocd`                                          |
| Install ArgoCD                | `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml` |
| Port Forward ArgoCD UI        | `kubectl port-forward svc/argocd-server -n argocd 8080:443`                |
| Retrieve Password             | `kubectl get secret argocd-initial-admin-secret -n argocd -o yaml`         |
| Create Application YAML       | Define `application.yaml` as shown above                                  |
| Apply Application YAML        | `kubectl apply -f application.yaml`                                        |
| Update Deployment YAML        | Modify `image` and commit changes to Git repository                       |
| Verify Deployment             | `kubectl get pods -n default`                                             |

---

### **Conclusion**
This demo showcases how ArgoCD automates Kubernetes deployments using GitOps principles. By tracking configuration files in Git and syncing them to the cluster, you eliminate manual intervention, ensuring consistency and scalability for Kubernetes environments.