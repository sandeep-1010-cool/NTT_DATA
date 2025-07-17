✅ Here's a **curated list of important ArgoCD topics** you must know as a **new joiner** expected to work on ArgoCD in a company. These are ordered for **hands-on learning + theory**, and can be copied to Cursor IDE for note-taking and parallel practice.

---

## 🚀 **Phase 1: Basics You Must Understand**

> 💡 Focus: Concepts + Architecture

### 🔹 1. What is GitOps?

* Git as the single source of truth
* GitOps vs traditional deployment
* Benefits: declarative, version-controlled, auditable

### 🔹 2. ArgoCD Overview

* What is ArgoCD?
* Where it fits in GitOps flow
* Features: declarative apps, auto sync, UI/CLI/API, diff detection

### 🔹 3. ArgoCD Architecture

* Key components:

  * `argocd-server` (UI/API)
  * `application-controller` (sync logic)
  * `repo-server` (pulls Git state)
  * `dex-server` (auth)
* Interactions between components

---

## ⚙️ **Phase 2: Installation & Setup**

> 💡 Focus: Install locally and explore UI/CLI

### 🔹 4. Install ArgoCD on Kubernetes

* Namespace creation
* CRD installation
* Using `kubectl apply` or `helm install`

### 🔹 5. Access ArgoCD UI

* `kubectl port-forward`
* Get initial admin password
* Login at `https://localhost:8080`

### 🔹 6. Install ArgoCD CLI

* `brew install argocd` (Mac)
* `argocd login localhost:8080`

---

## 🛠️ **Phase 3: Hands-on App Deployment**

> 💡 Focus: Practice GitOps workflow

### 🔹 7. Create First App (Guestbook example)

* CLI: `argocd app create ...`
* Sync app: manual vs auto sync
* Observe app health/status

### 🔹 8. Modify app in Git → observe changes

* Change YAML in Git
* ArgoCD detects drift
* Sync manually or auto

---

## 🔐 **Phase 4: Important Day-to-Day Skills**

> 💡 Focus: You’ll be expected to do this in real work

### 🔹 9. ArgoCD Sync Policies

* Manual sync
* Auto sync
* `prune`, `selfHeal`, `CreateNamespace`

### 🔹 10. ArgoCD RBAC (Basic)

* Roles: admin, developer
* Access control with `argocd-cm` and `rbac-cm`

### 🔹 11. CLI Commands Reference

* `argocd app list`
* `argocd app sync`
* `argocd app diff`
* `argocd app delete`

---

## 🧠 **Phase 5: Advanced (Optional Later)**

> 💡 Start learning after you're comfortable with basics

### 🔹 12. ApplicationSet (multi-env / multi-cluster)

* List generator / Matrix generator
* Dynamic app creation per env

### 🔹 13. Monitoring & Alerts

* Slack integration (notifications controller)
* Prometheus metrics setup

### 🔹 14. Security (Simple First)

* External secrets integration
* Network policies
* Login via SSO (Dex + GitHub)

---

## 🌐 **Free Practice Platforms (Browser-based)**

| Platform                                                                          | Use                                 |
| --------------------------------------------------------------------------------- | ----------------------------------- |
| [Play with Kubernetes](https://labs.play-with-k8s.com)                            | Free Kubernetes cluster             |
| [Katacoda (via GitHub)](https://katacoda.com) *(archived but still usable)*       | Prebuilt ArgoCD scenarios           |
| [KodeKloud GitOps Playground](https://kodekloud.com/courses/gitops/)              | Real browser labs                   |
| [minikube + ArgoCD in Gitpod](https://github.com/technosophos/gitops-argo-gitpod) | Online DevEnv                       |
| [Okteto](https://okteto.com)                                                      | Cloud dev environment to run ArgoCD |
| [Civo Academy](https://www.civo.com/academy)                                      | GitOps & ArgoCD courses             |

---

Let me know if you'd like a **Cursor prompt**, **markdown study plan**, or a **Confluence-ready table** to paste directly.
