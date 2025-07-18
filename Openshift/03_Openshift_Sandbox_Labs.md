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
| 🧠 **Boss Level**: Optimize & Monitor | Final Exam      | Monitoring + Alerts        | 🧠 Mastermind      | 1000 XP   |

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
Master networking and routing in OpenShift.

### 🧩 **Clue**:
> The network is the nervous system of your cluster. Master it.

### 🛠 **Your Toolkit**:
```bash
oc get routes
oc get services
oc describe route [route-name]
oc describe service [service-name]
```

### ✅ **Your Mission**:
1. **List all routes** in your project
2. **Examine service configurations**
3. **Test route connectivity**
4. **Understand the networking model**

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 7: Route Master

🌐 NETWORKING STATUS:
- Routes Found: [public URLs]
- Services Active: [service list]
- Connectivity: [working/broken]
- Network Model: [understanding level]

🎯 NEXT STEPS:
[What you want to explore next]
```

---

## 🧙 **Quest 8: Magic Mesh** 🧙 {#quest-8-magic-mesh}

### 🎯 **Mission Objective**: 
Explore advanced service mesh capabilities.

### 🧩 **Clue**:
> Service mesh is like magic - it makes complex networking simple.

### 🛠 **Your Toolkit**:
```bash
oc get virtualservices
oc get destinationrules
oc get serviceentries
oc get gateways
```

### ✅ **Your Mission**:
1. **Check if service mesh is installed**
2. **Explore VirtualServices**
3. **Examine DestinationRules**
4. **Understand the mesh architecture**

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 8: Magic Mesh

🧙 MESH STATUS:
- Service Mesh: [installed/not available]
- VirtualServices: [found configurations]
- DestinationRules: [traffic policies]
- Mesh Architecture: [understanding level]

🎯 NEXT STEPS:
[What you want to explore next]
```

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
- 🧠 **Mastermind**: Complete Boss Level

### 🎯 **XP Tracking**:
- **Quest 1**: 100 XP
- **Quest 2**: 150 XP
- **Quest 3**: 200 XP
- **Quest 4**: 250 XP
- **Quest 5**: 300 XP
- **Quest 6**: 400 XP
- **Quest 7**: 350 XP
- **Quest 8**: 500 XP
- **Boss Level**: 1000 XP

**Total Possible XP**: 3250 XP

---

## 🎮 **Ready to Start Your Adventure?** {#ready-to-start-your-adventure}

**Begin with Quest 1: Cluster Explorer** and post your mission report to unlock the next quest! 