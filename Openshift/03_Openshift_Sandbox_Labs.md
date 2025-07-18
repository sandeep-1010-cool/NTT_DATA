# OpenShift Quest Mode: Interactive Learning Adventure

## 📋 **Quick Navigation**

### 🎮 **Quest System**
- [🗺️ Game Board](#-game-board-openshift-quest-mode)
- [🎯 How to Play](#-how-to-play)
- [🏆 Leaderboard System](#-leaderboard-system)
- [🔧 Setup Requirements](#-setup-requirements)

### 🚀 **Quests**
- [🔥 Quest 1: Cluster Explorer](#-quest-1-cluster-explorer-)
- [🚀 Quest 2: Launch Your App](#-quest-2-launch-your-app-)
- [🐞 Quest 3: Debug That Pod!](#-quest-3-debug-that-pod-)
- [🔐 Quest 4: Hacker Shield](#-quest-4-hacker-shield-)
- [📦 Quest 5: Treasure Storage](#-quest-5-treasure-storage-)
- [⚙️ Quest 6: Assembly Line](#-quest-6-assembly-line-)
- [🌐 Quest 7: Route Master](#-quest-7-route-master-)
- [🧙 Quest 8: Magic Mesh](#-quest-8-magic-mesh-)
- [🧠 Quest 9: Mastermind](#-quest-9-mastermind-)
- [🧠 Boss Level: Optimize & Monitor](#-boss-level-optimize--monitor-)

### 🏆 **Achievements**
- [🏆 Achievement System](#-achievement-system)
- [🎮 Ready to Start Your Adventure?](#-ready-to-start-your-adventure)

---

## 🗺️ **Game Board: OpenShift Quest Mode** {#game-board-openshift-quest-mode}

| Quest                                 | Mission Type    | Skill Focus                | Completion Badge   | XP Points |
| ------------------------------------- | --------------- | -------------------------- | ------------------ | --------- |
| 🔰 **Quest 1: Cluster Explorer**      | Scavenger Hunt  | CLI Navigation + Projects  | 🧭 Pathfinder      | 100 XP    |
| 🚀 **Quest 2: Launch Your App**       | Speedrun        | `oc new-app` + Routes      | 🛸 Deployer        | 150 XP    |
| 🐞 **Quest 3: Debug That Pod!**       | Rescue Mission  | Logs + Describe + Events   | 🔍 Debugger        | 200 XP    |
| 🔐 **Quest 4: Hacker Shield**         | Defense         | SCC + RBAC                 | 🛡️ SecOps Hero    | 250 XP    |
| 📦 **Quest 5: Treasure Storage**      | Inventory Build | PVC + Secrets + ConfigMaps | 📦 Vault Keeper    | 300 XP    |
| ⚙️ **Quest 6: Assembly Line**         | Automation      | CI/CD with Pipelines       | 🤖 DevOps Engineer | 400 XP    |
| 🌐 **Quest 7: Route Master**          | Connectivity    | Services + Routes          | 🌍 Network Guru    | 350 XP    |
| 🧙 **Quest 8: Magic Mesh**            | Advanced        | Istio + VirtualService     | 🧙 Mesh Mage       | 500 XP    |
| 🧠 **Quest 9: Mastermind**            | Monitoring      | Prometheus + Alerting      | 🧠 Mastermind      | 600 XP    |
| 🏆 **Boss Level**: Optimize & Monitor | Final Exam      | Monitoring + Alerts        | 🏆 Grand Master    | 1000 XP   |

## 🎯 **How to Play** {#how-to-play}

1. **Start with Quest 1** - Complete each quest in order
2. **Use the provided toolkit** - Each quest gives you specific commands to use
3. **Share your findings** - Post your mission reports to unlock the next quest
4. **Earn badges** - Collect badges as you complete each quest
5. **Track your XP** - Build up your OpenShift mastery points

## 🏆 **Leaderboard System** {#leaderboard-system}
- **Rookie**: 0-500 XP
- **Explorer**: 501-1000 XP  
- **Veteran**: 1001-2000 XP
- **Master**: 2001+ XP

## 🔧 **Setup Requirements** {#setup-requirements}

### Prerequisites
- Access to [OpenShift Developer Sandbox](https://developers.redhat.com/developer-sandbox)
- OpenShift CLI (oc) installed
- Basic understanding of containers and Kubernetes concepts

### Quick Setup
1. Visit [OpenShift Developer Sandbox](https://developers.redhat.com/developer-sandbox)
2. Sign in with: `vuyyalasandeep91@gmail.com` / `ChangePwd@13081991`
3. Select the **OpenShift** card and click "Try it"
4. Install OpenShift CLI: `oc version`
5. Get login command from web console and login via CLI

### Important Notes
- **Use Existing Project**: `vuyyalasandeep91-dev` (no new project creation)
- **Permission Limitations**: Some "Forbidden" errors are normal
- **Resource Limits**: Be mindful of free tier usage

---

## 🔥 **Quest 1: Cluster Explorer** 🧭 {#quest-1-cluster-explorer}

### 🎯 **Mission Objective**: 
Verify your OpenShift access and list all resources **you have permissions for**.

### 🧩 **Clue**:
> The cluster won't allow you to create a new world, but an old one remembers your name. Find it.

### 🛠 **Your Toolkit**:
```bash
oc whoami
oc project
oc get projects
oc get all
oc get pods,svc,deploy
```

### ✅ **Your Mission**:
1. **Log into the sandbox** using the credentials above
2. **Identify your current project** - it's already created for you
3. **List all resources** you're allowed to see
4. **Share your findings** like a mission report

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 1: Cluster Explorer

🔍 FINDINGS:
- Current User: [your username]
- Current Project: [project name]
- Resources Found: [list what you discovered]
- Permission Status: [any errors encountered]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Successfully logged into OpenShift
- ✅ Identified your working project
- ✅ Listed at least 3 resource types
- ✅ Posted mission report

---

## 🚀 **Quest 2: Launch Your App** 🛸 {#quest-2-launch-your-app}

### 🎯 **Mission Objective**: 
Deploy your first application and make it accessible to the world.

### 🧩 **Clue**:
> The portal to the world outside lies behind a service. Only a route can unlock it.

### 🛠 **Your Toolkit**:
```bash
# Deploy a sample app (Node.js or Python)
oc new-app nodejs~https://github.com/sclorg/nodejs-ex --name=quest2-app

# Monitor the pod
oc get pods -w

# Expose the service
oc expose service quest2-app

# Get the public route
oc get routes

# Alternative: Modern deployment with YAML
# oc apply -f quest2-app.yaml
```

### ✅ **Your Mission**:
1. **Deploy a new app** called `quest2-app`
2. **Watch the deployment** in real-time with `oc get pods -w`
3. **Expose your app** to the internet using a route
4. **Get your public URL** and test it in a browser
5. **Share your findings** in the mission report format

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 2: Launch Your App

🚀 DEPLOYMENT STATUS:
- App Name: quest2-app
- Pod Status: [running/error/etc]
- Route URL: [your app's public URL]
- Browser Test: [success/failure - what you saw]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Successfully deployed `quest2-app`
- ✅ Created a public route
- ✅ Retrieved the route URL
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you access the app in your browser and describe what you see
- **+25 XP** if you can explain what `oc new-app` actually did behind the scenes

### 🛠 **Pro Tips**:
- Use `Ctrl+C` to stop the `-w` (watch) command when you're done monitoring
- If you get any errors, try `oc get events` to see what's happening
- The route URL will look like: `quest2-app-vuyyalasandeep91-dev.apps.sandbox.x8i5.p1.openshiftapps.com`

---

## 🐞 **Quest 3: Debug That Pod!** 🔍 {#quest-3-debug-that-pod}

### 🎯 **Mission Objective**: 
Master the art of troubleshooting and debugging in OpenShift.

### 🧩 **Clue**:
> A pod without access to write shall crash again and again. Look where it tries to write.

### 🛠 **Your Toolkit**:
```bash
# View logs of the crashing pod
oc logs nginx-pod

# Describe pod events and errors
oc describe pod nginx-pod

# Access the pod (if it stays alive briefly)
oc rsh nginx-pod

# Clean and recreate the pod (if needed)
oc delete pod nginx-pod
```

### ✅ **Your Mission**:
1. **Find the reason** for the `CrashLoopBackOff` (hint: already seen it in your earlier logs)
2. **Suggest or apply a fix** (ConfigMap, SCC, init container, or change temp path)
3. **Share your findings** and your fix plan

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 3: Debug That Pod!

🔍 DEBUGGING FINDINGS:
- Pods Found: [number and names]
- Pod Status: [healthy/unhealthy]
- Logs Analysis: [any errors/issues]
- Events Found: [relevant events]
- Commands Executed: [what you tried]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Identified the crash reason
- ✅ Suggested a fix strategy
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you successfully fix the pod and get it running
- **+25 XP** if you can explain why the crash happened

---

## 🔐 **Quest 4: Hacker Shield** 🛡️ {#quest-4-hacker-shield}

### 🎯 **Mission Objective**: 
Implement app-level security best practices in the Developer Sandbox environment.

### 🧩 **Clue**:
> In the sandbox realm, you must work with what you have. Focus on app-level security, not cluster-level permissions.

### ⚠️ **Sandbox Limitations**:
- **Cannot create custom SCCs** (cluster-level permission required)
- **Cannot use `oc adm` commands** (admin privileges needed)
- **Cannot modify cluster RBAC** (restricted access)

### 🛠 **Your Toolkit** (Sandbox-Compatible):
```bash
# Check available SCCs (read-only)
oc get scc

# Create service account (project-level)
oc create serviceaccount secure-sa

# Deploy app with security context (Modern Deployment)
oc apply -f secure-app.yaml

# OR use legacy DeploymentConfig (if needed)
# oc apply -f secure-app-dc.yaml

# Verify pod security (use pod name, not label)
oc get pods -o yaml | grep -A 10 securityContext

# Check pod logs (use pod name)
oc logs secure-app-1-deploy

# Alternative: Check all pods and find the right one
oc get pods --show-labels

# Get specific pod details
oc describe pod secure-app-1-deploy

# Alternative: Use oc new-app for quick deployment
# oc new-app nginx:latest --name=secure-app --serviceaccount=secure-sa
```

### ✅ **Your Mission**:
1. **Explore available SCCs** (read-only)
2. **Create a service account**: `secure-sa`
3. **Deploy app with security context** (no custom SCC)
4. **Verify non-root execution** and security compliance

### 📋 **Required Files**:

#### `secure-app.yaml` (Modern Deployment):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: secure-app
spec:
  replicas: 1
  selector:
    matchLabels:
    app: secure-app
  template:
    metadata:
      labels:
        app: secure-app
    spec:
      serviceAccountName: secure-sa
      containers:
      - name: secure-app
        image: nginx:latest
        ports:
        - containerPort: 80
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: false
  resources:
    requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

#### `secure-app-dc.yaml` (Legacy DeploymentConfig - if needed):
```yaml
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: secure-app-dc
spec:
  replicas: 1
  selector:
    app: secure-app-dc
  template:
    metadata:
      labels:
        app: secure-app-dc
    spec:
      serviceAccountName: secure-sa
      containers:
      - name: secure-app
        image: nginx:latest
        ports:
        - containerPort: 80
        securityContext:
          runAsNonRoot: true
          runAsUser: 1000
          allowPrivilegeEscalation: false
          readOnlyRootFilesystem: false
  resources:
    requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128Mi"
            cpu: "500m"
```

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 4: Hacker Shield

🛡️ SECURITY AUDIT:
- Available SCCs: [list found]
- Service Account: [secure-sa]
- Pod Status: [running/error]
- Security Context: [non-root user verified]
- Permission Errors: [any encountered]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Explored available SCCs (read-only)
- ✅ Created service account successfully
- ✅ Deployed app with security context
- ✅ Verified non-root user execution
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain why `runAsNonRoot: true` is important
- **+25 XP** if you test the app and verify it's accessible
- **+25 XP** if you identify which SCC your pod is using

### 🛠 **Pro Tips**:
- Use `oc describe pod secure-app` to see which SCC is applied
- Check `oc get scc` to see available security policies
- If you get permission errors, they're expected in sandbox
- Focus on app-level security practices you can control
- **Note**: `DeploymentConfig` is deprecated - use `Deployment` for new apps
- Use `oc new-app` for quick prototyping, `oc apply` for production deployments

---

## 📦 **Quest 5: Treasure Storage** 📦 {#quest-5-treasure-storage}

### 🎯 **Mission Objective**: 
Use **ConfigMaps**, **Secrets**, and a **PersistentVolumeClaim** to securely configure and persist data for an application.

### 🧩 **Clue**:
> Not all treasures are gold – some are mounted under `/etc/config` and `/data`…

### 🛠 **Your Toolkit**:

#### **1️⃣ Create ConfigMap**:
```bash
oc create configmap app-config \
  --from-literal=APP_MODE=debug \
  --from-literal=WELCOME_MSG="Hello, Vault Keeper!"
```

#### **2️⃣ Create Secret**:
```bash
oc create secret generic app-secret \
  --from-literal=username=admin \
  --from-literal=password=OpenShiftRocks123
```

#### **3️⃣ Create PVC**:
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: quest-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 1Gi
EOF
```

#### **4️⃣ Deploy App Using All 3**:
```bash
cat <<EOF | oc apply -f -
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: vault-app
spec:
  replicas: 1
  selector:
    app: vault-app
  template:
    metadata:
      labels:
        app: vault-app
    spec:
      containers:
      - name: vault-app
        image: nginx
        ports:
        - containerPort: 80
        envFrom:
        - configMapRef:
            name: app-config
        - secretRef:
            name: app-secret
        volumeMounts:
        - name: data-volume
          mountPath: /usr/share/nginx/html
      volumes:
      - name: data-volume
        persistentVolumeClaim:
          claimName: quest-pvc
EOF
```

#### **5️⃣ Test It**:
```bash
# Get pod name
POD=$(oc get pods -l app=vault-app -o jsonpath='{.items[0].metadata.name}')

# Write file to PVC
oc exec $POD -- sh -c 'echo "Welcome Vault Keeper!" > /usr/share/nginx/html/index.html'

# Expose and access the app
oc expose dc vault-app
oc get routes
```

### ✅ **Your Mission**:
1. **Complete steps 1–5** above
2. **Visit the exposed URL** – you should see **"Welcome Vault Keeper!"**
3. **Delete the pod**, confirm PVC persists the file
4. **Share your findings** with route URL or screenshot

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 5: Treasure Storage

📦 STORAGE INVENTORY:
- ConfigMap Created: [app-config]
- Secret Created: [app-secret]
- PVC Created: [quest-pvc]
- App Deployed: [vault-app]
- Route URL: [your app's public URL]
- Data Persistence Test: [success/failure]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Created ConfigMap with environment variables
- ✅ Created Secret with sensitive data
- ✅ Created PVC for persistent storage
- ✅ Deployed app using all three resources
- ✅ Verified data persistence across pod restarts
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain the difference between ConfigMaps and Secrets
- **+25 XP** if you test the app and verify it's accessible
- **+25 XP** if you can show the data persists after pod deletion

### 🛠 **Pro Tips**:
- Use `oc get configmaps` to list all ConfigMaps
- Use `oc get secrets` to list all Secrets (safely!)
- Use `oc describe pvc quest-pvc` to check PVC status
- Use `oc get pods -l app=vault-app` to check your app status
- **Note**: Secrets are base64 encoded, ConfigMaps are plain text

---

## ⚙️ **Quest 6: Assembly Line** 🤖 {#quest-6-assembly-line}

### 🎯 **Mission Objective**: 
Create and run a **CI/CD pipeline** that clones a Git repo, builds the image with **Buildah**, and deploys it in your namespace.

### 🧩 **Clue**:
> A builder without a workspace is just a dreamer. A pipeline with a PVC becomes a factory.

### 🛠 **Your Toolkit** (Tekton YAMLs):

You'll need 3 objects:
- 📦 `Pipeline`
- 🚀 `PipelineRun`
- 📂 `VolumeClaimTemplate`

#### **1️⃣ Create Pipeline**:
```bash
cat <<EOF | oc apply -f -
apiVersion: tekton.dev/v1beta1
kind: Pipeline
metadata:
  name: build-and-deploy
spec:
  workspaces:
    - name: shared-workspace
  params:
    - name: git-url
      type: string
    - name: image-name
      type: string
  tasks:
    - name: fetch-repo
      taskRef:
        name: git-clone
      workspaces:
        - name: output
          workspace: shared-workspace
      params:
        - name: url
          value: \$(params.git-url)
    - name: build-image
      runAfter: ["fetch-repo"]
      taskRef:
        name: buildah
      workspaces:
        - name: source
          workspace: shared-workspace
      params:
        - name: IMAGE
          value: \$(params.image-name)
EOF
```

#### **2️⃣ Create PipelineRun**:
```bash
cat <<EOF | oc apply -f -
apiVersion: tekton.dev/v1beta1
kind: PipelineRun
metadata:
  name: build-and-deploy-run
spec:
  pipelineRef:
    name: build-and-deploy
  params:
    - name: git-url
      value: https://github.com/sclorg/nodejs-ex
    - name: image-name
      value: image-registry.openshift-image-registry.svc:5000/\$(oc project -q)/tekton-app:latest
  workspaces:
    - name: shared-workspace
      volumeClaimTemplate:
        spec:
          accessModes: ["ReadWriteOnce"]
          resources:
            requests:
              storage: 1Gi
EOF
```

#### **3️⃣ Monitor Progress**:
```bash
oc get pipelineruns
oc describe pipelinerun build-and-deploy-run
oc logs -f pipelinerun/build-and-deploy-run --all
```

#### **4️⃣ Deploy the Built Image**:
```bash
oc new-app \$(oc project -q)/tekton-app:latest
oc expose svc tekton-app
oc get routes
```

### ✅ **Your Mission**:
1. **Apply the Pipeline + PipelineRun**
2. **Ensure `git-clone` and `buildah` tasks are pre-installed** (if not, I'll provide YAMLs for them)
3. **Wait for the pipeline to finish**
4. **Deploy the image** using the commands above
5. **Share your findings** with route URL or issues

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 6: Assembly Line

🤖 AUTOMATION STATUS:
- Pipeline Created: [build-and-deploy]
- PipelineRun Status: [success/failure]
- Tasks Available: [git-clone, buildah]
- Build Duration: [time taken]
- Image Built: [tekton-app:latest]
- App Deployed: [yes/no]
- Route URL: [your app's public URL]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Created Pipeline with git-clone and buildah tasks
- ✅ Created PipelineRun with proper parameters
- ✅ Pipeline executed successfully
- ✅ Image built and pushed to registry
- ✅ App deployed from built image
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain the difference between Pipeline and PipelineRun
- **+25 XP** if you test the app and verify it's accessible
- **+25 XP** if you can show the pipeline logs and explain each step

### 🛠 **Pro Tips**:
- Use `oc get tasks` to see available Tekton tasks
- Use `oc get pipelineruns` to monitor pipeline execution
- Use `oc logs pipelinerun/build-and-deploy-run --all` for detailed logs
- **Note**: If tasks are missing, you may need to install them first
- Use `oc get routes` to get your app's public URL

---

## 🌐 **Quest 7: Route Master** 🌍 {#quest-7-route-master}

### 🎯 **Mission Objective**: 
Deploy two services — **frontend** and **backend** — that talk to each other inside the cluster and are exposed separately to the outside world.

### 🧩 **Clue**:
> Only those who name their services well can summon them from within the mesh.

### 🛠 **Your Toolkit**:

#### **1️⃣ Deploy Both Apps**:
```bash
# Deploy backend app
oc new-app https://github.com/sclorg/nodejs-ex --name=backend

# Deploy frontend app
oc new-app https://github.com/sclorg/nodejs-ex --name=frontend
```

#### **2️⃣ Create Services** (if needed):
If `oc new-app` didn't create services:
```bash
oc expose dc backend --port=8080 --name=backend-svc
oc expose dc frontend --port=8080 --name=frontend-svc
```

#### **3️⃣ Test Pod-to-Pod Access**:
```bash
# Get pod names
BACKEND_POD=$(oc get pods -l app=backend -o jsonpath='{.items[0].metadata.name}')
FRONTEND_POD=$(oc get pods -l app=frontend -o jsonpath='{.items[0].metadata.name}')

# Test communication from frontend to backend
oc exec $FRONTEND_POD -- curl -s http://backend-svc:8080
```

#### **4️⃣ Expose Both with Custom Routes**:
```bash
oc expose svc backend-svc --name=backend-route
oc expose svc frontend-svc --name=frontend-route
```

To create with custom host:
```bash
cat <<EOF | oc apply -f -
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  name: custom-frontend
spec:
  host: frontend-quest.apps.YOUR-SANDBOX-DOMAIN
  to:
    kind: Service
    name: frontend-svc
  port:
    targetPort: 8080
EOF
```

> Replace `YOUR-SANDBOX-DOMAIN` with what you see in `oc get routes`.

#### **5️⃣ Verify Network Connectivity**:
```bash
# List all services
oc get services

# List all routes
oc get routes

# Test service connectivity
oc exec $FRONTEND_POD -- curl -s http://backend-svc:8080

# Test external access
curl -s http://backend-route-URL
curl -s http://frontend-route-URL
```

### ✅ **Your Mission**:
1. **Deploy both apps** and expose services
2. **`curl` from frontend to backend** (inside cluster)
3. **Create 2 public routes** and access via browser
4. **Share any 1 route URL** or screenshot
5. **Type `done`** to unlock Quest 8: Mesh Mage

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 7: Route Master

🌐 NETWORKING STATUS:
- Backend App: [deployed/running]
- Frontend App: [deployed/running]
- Services Created: [backend-svc, frontend-svc]
- Internal Communication: [frontend → backend curl test]
- Routes Created: [backend-route, frontend-route]
- External Access: [URLs accessible]
- Network Model: [understanding level]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Deployed backend and frontend apps
- ✅ Created services for both apps
- ✅ Verified pod-to-pod communication
- ✅ Created public routes for both apps
- ✅ Tested external access to both apps
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain the difference between ClusterIP and Route
- **+25 XP** if you test both apps and verify they're accessible
- **+25 XP** if you can show the service-to-service communication logs

### 🛠 **Pro Tips**:
- Use `oc get services` to list all services
- Use `oc get routes` to see public URLs
- Use `oc describe service backend-svc` for service details
- Use `oc describe route backend-route` for route details
- **Note**: Services enable internal communication, Routes enable external access
- Use `oc exec $POD -- curl $SERVICE` to test internal connectivity

---

## 🧙 **Quest 8: Magic Mesh** 🧙 {#quest-8-magic-mesh}

### 🎯 **Mission Objective**: 
Use OpenShift Service Mesh (Istio) to deploy 2 versions of the same app and route traffic between them using a **VirtualService**.

### 🧩 **Clue**:
> One app. Two paths. The mesh decides who gets how much.

### 🧠 **Assumption**:
You've already installed **OpenShift Service Mesh Operator** and created:
- `ServiceMeshControlPlane`
- `ServiceMeshMemberRoll`

> Let me know if not — I'll help you bootstrap it first.

### 🛠 **Your Toolkit**:

#### **1️⃣ Label Namespace for Mesh Injection**:
```bash
oc label namespace $(oc project -q) istio-injection=enabled --overwrite
```

#### **2️⃣ Deploy Two Versions of App**:
```bash
# Version v1
oc new-app https://github.com/sclorg/nodejs-ex.git --name app-v1

# Version v2 (simulate new version)
oc new-app https://github.com/sclorg/nodejs-ex.git --name app-v2
```

#### **3️⃣ Create Kubernetes Service**:
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Service
metadata:
  name: mesh-app
  labels:
    app: mesh-app
spec:
  ports:
    - port: 8080
      targetPort: 8080
  selector:
    app: mesh-app
EOF
```

#### **4️⃣ Patch Deployments with Label for Routing**:
```bash
oc label deployment app-v1 app=mesh-app version=v1 --overwrite
oc label deployment app-v2 app=mesh-app version=v2 --overwrite
```

#### **5️⃣ Create Istio VirtualService & DestinationRule**:
```bash
cat <<EOF | oc apply -f -
apiVersion: networking.istio.io/v1beta1
kind: DestinationRule
metadata:
  name: mesh-app
spec:
  host: mesh-app
  subsets:
  - name: v1
    labels:
      version: v1
  - name: v2
    labels:
      version: v2
---
apiVersion: networking.istio.io/v1beta1
kind: VirtualService
metadata:
  name: mesh-app
spec:
  hosts:
  - mesh-app
  http:
  - route:
    - destination:
        host: mesh-app
        subset: v1
      weight: 80
    - destination:
        host: mesh-app
        subset: v2
      weight: 20
EOF
```

#### **6️⃣ Test Traffic Split**:
```bash
# Get pod
POD=$(oc get pod -l app=mesh-app -o jsonpath='{.items[0].metadata.name}')

# Run curl 10 times
for i in {1..10}; do oc exec $POD -- curl -s http://mesh-app:8080; echo; done
```

🔍 You should see mixed responses indicating 80/20 split between v1 and v2.

#### **7️⃣ Verify Mesh Components**:
```bash
# Check VirtualServices
oc get virtualservices

# Check DestinationRules
oc get destinationrules

# Check service mesh pods
oc get pods -n istio-system

# Check mesh injection
oc get pods --show-labels | grep istio-injection
```

### ✅ **Your Mission**:
1. **Deploy v1 and v2** under Service Mesh
2. **Apply `VirtualService` and `DestinationRule`**
3. **Run `curl` loop** and confirm split traffic
4. **Share your findings** with curl output or observation

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 8: Magic Mesh

🧙 MESH STATUS:
- Service Mesh: [installed/working]
- VirtualServices: [mesh-app created]
- DestinationRules: [mesh-app created]
- Traffic Split: [80/20 v1/v2]
- Mesh Injection: [enabled/working]
- Traffic Test Results: [curl output summary]
- Mesh Architecture: [understanding level]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Labeled namespace for mesh injection
- ✅ Deployed v1 and v2 versions of app
- ✅ Created Kubernetes service for mesh routing
- ✅ Applied VirtualService and DestinationRule
- ✅ Verified traffic split (80/20)
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain the difference between VirtualService and DestinationRule
- **+25 XP** if you modify the traffic split to 50/50 and test
- **+25 XP** if you can show the mesh telemetry and metrics

### 🛠 **Pro Tips**:
- Use `oc get virtualservices` to list VirtualServices
- Use `oc get destinationrules` to list DestinationRules
- Use `oc get pods -n istio-system` to check mesh components
- Use `oc describe virtualservice mesh-app` for detailed configuration
- **Note**: Service mesh requires proper namespace labeling for injection
- Use `oc logs -l app=mesh-app` to see traffic routing logs

---

## 🧠 **Quest 9: Mastermind – Monitoring & Alerting** 🧠 {#quest-9-mastermind}

### 🎯 **Mission Objective**: 
Enable deep visibility, track metrics, and set alerts that awaken when danger (or downtime) is near — using **Prometheus**, **ServiceMonitor**, and **PrometheusRule**.

### 🧩 **Clue**:
> You cannot monitor what you don't label. Metrics only flow where annotations go.

### 🧠 **Assumption**:
You are using **OpenShift Monitoring Stack** (comes pre-installed in sandbox).

### 🛠 **Your Toolkit**:

#### **1️⃣ Deploy a Prometheus-Compatible App**:
```bash
oc new-app prom/node-exporter --name=monitor-me
```

#### **2️⃣ Add Scrape Annotations to Pod**:
```bash
oc patch deployment monitor-me --patch '
spec:
  template:
    metadata:
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "9100"
' --type=merge
```

> Change `port` to match container's metrics port

#### **3️⃣ Create a ServiceMonitor**:
```bash
cat <<EOF | oc apply -f -
apiVersion: monitoring.coreos.com/v1
kind: ServiceMonitor
metadata:
  name: monitor-me
  labels:
    team: devops
spec:
  selector:
    matchLabels:
      app: monitor-me
  endpoints:
    - port: web
      interval: 30s
EOF
```

#### **4️⃣ Create an Alert Rule (PrometheusRule)**:
```bash
cat <<EOF | oc apply -f -
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: monitor-me-rules
spec:
  groups:
  - name: high-cpu
    rules:
    - alert: HighCPUDetected
      expr: process_cpu_seconds_total > 0.5
      for: 1m
      labels:
        severity: warning
      annotations:
        summary: "High CPU usage on monitor-me"
        description: "CPU usage is above threshold"
EOF
```

#### **5️⃣ Test It**:
```bash
# Simulate CPU load
oc exec $(oc get pods -l app=monitor-me -o jsonpath='{.items[0].metadata.name}') -- sh -c "yes > /dev/null &"

# Wait 1–2 mins and check alerts
oc get prometheusrules
oc get servicemonitors
```

You can also view metrics via web console:
**Monitoring → Metrics → Query `process_cpu_seconds_total`**

### ✅ **Your Mission**:
1. **Deploy a metrics-emitting app** (node-exporter)
2. **Set up `ServiceMonitor` + `PrometheusRule`**
3. **Trigger the alert** and validate it in the console
4. **Share your findings** in the mission report format

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 9: Mastermind

🧠 MONITORING STATUS:
- App Deployed: [monitor-me]
- ServiceMonitor: [created/working]
- PrometheusRule: [created/working]
- Metrics Collection: [active/verified]
- Alert Triggered: [yes/no]
- Console Access: [metrics visible]
- Monitoring Architecture: [understanding level]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Deployed node-exporter app with metrics
- ✅ Created ServiceMonitor for metrics collection
- ✅ Created PrometheusRule for alerting
- ✅ Triggered and verified alert
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain the difference between ServiceMonitor and PrometheusRule
- **+25 XP** if you create a custom metric and alert on it
- **+25 XP** if you can show the alert firing in the web console

### 🛠 **Pro Tips**:
- Use `oc get servicemonitors` to list ServiceMonitors
- Use `oc get prometheusrules` to list PrometheusRules
- Use `oc describe servicemonitor monitor-me` for detailed configuration
- Use `oc describe prometheusrule monitor-me-rules` for alert details
- **Note**: Metrics port must match the container's actual metrics endpoint
- Use web console Monitoring → Metrics for visual verification

---

## 🧠 **Boss Level: Optimize & Monitor** 🧠 {#boss-level-optimize--monitor}

### 🎯 **Mission Objective**: 
Implement comprehensive monitoring and optimization.

### 🧩 **Clue**:
> Monitoring is the key to production success. Watch everything.

### 🛠 **Your Toolkit**:
```bash
oc get prometheusrules
oc get servicemonitors
oc get alerts
oc top pods
oc top nodes
```

### ✅ **Your Mission**:
1. **Check monitoring setup**
2. **Review alerting rules**
3. **Monitor resource usage**
4. **Optimize performance**

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Boss Level: Optimize & Monitor

🧠 MONITORING STATUS:
- Prometheus Rules: [alerting setup]
- Service Monitors: [monitoring config]
- Resource Usage: [CPU/memory stats]
- Optimization Opportunities: [findings]

🏆 FINAL ASSESSMENT:
- Total XP Earned: [sum of all quests]
- Badges Collected: [list of badges]
- Mastery Level: [Rookie/Explorer/Veteran/Master]
- Next Learning Goals: [future plans]
```

---

## 🏆 **Achievement System** {#achievement-system}

### 🎖️ **Badges to Collect**:
- 🧭 **Pathfinder**: Complete Quest 1
- 🛸 **Deployer**: Complete Quest 2  
- 🔍 **Debugger**: Complete Quest 3
- 🛡️ **SecOps Hero**: Complete Quest 4
- 📦 **Vault Keeper**: Complete Quest 5
- 🤖 **DevOps Engineer**: Complete Quest 6
- 🌍 **Network Guru**: Complete Quest 7
- 🧙 **Mesh Mage**: Complete Quest 8
- 🧠 **Mastermind**: Complete Quest 9
- 🏆 **Grand Master**: Complete Boss Level

### 🎯 **XP Tracking**:
- **Quest 1**: 100 XP
- **Quest 2**: 150 XP
- **Quest 3**: 200 XP
- **Quest 4**: 250 XP
- **Quest 5**: 300 XP
- **Quest 6**: 400 XP
- **Quest 7**: 350 XP
- **Quest 8**: 500 XP
- **Quest 9**: 600 XP
- **Boss Level**: 1000 XP

**Total Possible XP**: 3850 XP

---

## 🎮 **Ready to Start Your Adventure?** {#ready-to-start-your-adventure}

**Begin with Quest 1: Cluster Explorer** and post your mission report to unlock the next quest! 