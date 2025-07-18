### **Demo Overview: Setting Up a Fully Automated CD Pipeline with ArgoCD**  

#### **1. Demo Setup**  
We will create a fully automated Continuous Deployment (CD) pipeline using ArgoCD for Kubernetes configuration changes. Here's the setup:  

##### **Prerequisites**  
- **Git Repository**:  
  - Contains deployment and service YAML files.  
  - Deployment YAML references the application image version `1.0`.  
  - Three application image versions (`1.0`, `2.0`, `3.0`) are available in your **Docker public repository**.  
- **Kubernetes Cluster**:  
  - An empty **Minikube cluster** (or any Kubernetes cluster).  

---

#### **2. Demo Workflow**  

##### **Step 1: Install ArgoCD in Kubernetes Cluster**  
1. **Install ArgoCD**:  
   - Use the official ArgoCD Helm Chart or manifests to install ArgoCD in the Minikube cluster.  
   - Example commands:  
     ````bash
     kubectl create namespace argocd
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
     ````  
2. **Access ArgoCD UI**:  
   - Forward the ArgoCD server service to your local machine:  
     ````bash
     kubectl port-forward svc/argocd-server -n argocd 8080:443
     ````  
   - Log in using the default admin credentials (admin/password).  

---

##### **Step 2: Configure ArgoCD Application**  
1. **Create ArgoCD Application CRD**:  
   - Define an ArgoCD Application resource to tell ArgoCD:  
     - The **Git repository** to track.  
     - The **target cluster** (Minikube).  
   - Example YAML:  
     ````yaml
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
         path: .
       destination:
         server: https://kubernetes.default.svc
         namespace: default
       syncPolicy:
         automated:
           prune: true
           selfHeal: true
     ````  
2. **Apply the Application CRD**:  
   - Deploy the Application configuration to the cluster:  
     ````bash
     kubectl apply -f application.yaml
     ````  

---

##### **Step 3: Verify ArgoCD Sync**  
1. **Initial Sync**:  
   - ArgoCD will automatically sync the Kubernetes resources from the Git repository to the Minikube cluster.  
   - Verify the synced resources:  
     ````bash
     kubectl get all -n default
     ````  

2. **Access Application**:  
   - If the application includes a service, forward the service port to access it locally:  
     ````bash
     kubectl port-forward svc/my-app-service 8080:80
     ````  

---

##### **Step 4: Update Configurations in Git Repository**  
1. **Update Deployment YAML**:  
   - Change the image version from `1.0` to `2.0` in the deployment YAML file.  
   - Example snippet:  
     ````yaml
     spec:
       containers:
       - name: my-app
         image: your-docker-repo/my-app:2.0
     ````  
2. **Commit Changes to Git**:  
   - Push the updated YAML file to the Git repository:  
     ````bash
     git add deployment.yaml
     git commit -m "Update image to version 2.0"
     git push origin main
     ````  

3. **Observe Automatic Deployment**:  
   - ArgoCD will detect the changes in the Git repository and automatically sync them to the Minikube cluster.  
   - Verify the new deployment:  
     ````bash
     kubectl get pods -n default
     ````  

---

#### **5. Key Features to Highlight in the Demo**  
| **Feature**                  | **Description**                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| **GitOps Workflow**          | ArgoCD automatically pulls changes from Git and applies them to the cluster. |
| **Self-Healing**             | Detects and fixes drift between the desired state (Git) and the actual state (cluster). |
| **Automated Sync**           | Changes in Git are automatically deployed to the cluster without manual intervention. |
| **Environment Isolation**    | Each environment (e.g., dev, staging, prod) can have its own Application CRD. |

---

#### **6. Demo Commands Summary**  

| **Step**                     | **Command**                                                                 |
|------------------------------|-----------------------------------------------------------------------------|
| Install ArgoCD               | `kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml` |
| Port-forward ArgoCD UI       | `kubectl port-forward svc/argocd-server -n argocd 8080:443`                |
| Create Application CRD       | `kubectl apply -f application.yaml`                                        |
| Verify Resources             | `kubectl get all -n default`                                              |
| Update Deployment YAML       | Modify `image` in deployment YAML file.                                   |
| Commit Changes to Git        | `git add deployment.yaml && git commit -m "Update image to version 2.0" && git push origin main` |
| Verify Deployment            | `kubectl get pods -n default`                                             |

---

### **Conclusion**  
This demo showcases how ArgoCD automates the deployment of Kubernetes configuration changes using GitOps principles. By installing ArgoCD, configuring the Application CRD, and syncing changes from Git, you achieve a seamless CD pipeline where updates are automatically applied to the cluster. This workflow eliminates manual intervention, ensuring consistency and scalability for Kubernetes deployments.