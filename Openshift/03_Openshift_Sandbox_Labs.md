# OpenShift Quest Mode: Interactive Learning Adventure

## 🗺️ **Game Board: OpenShift Quest Mode**

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

## 🎯 **How to Play**

1. **Start with Quest 1** - Complete each quest in order
2. **Use the provided toolkit** - Each quest gives you specific commands to use
3. **Share your findings** - Post your mission reports to unlock the next quest
4. **Earn badges** - Collect badges as you complete each quest
5. **Track your XP** - Build up your OpenShift mastery points

## 🏆 **Leaderboard System**
- **Rookie**: 0-500 XP
- **Explorer**: 501-1000 XP  
- **Veteran**: 1001-2000 XP
- **Master**: 2001+ XP

## 🔧 **Setup Requirements**

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

## 🔥 **Quest 1: Cluster Explorer** 🧭

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

## 🚀 **Quest 2: Launch Your App** 🛸

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

## 🐞 **Quest 3: Debug That Pod!** 🔍

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

## 🔐 **Quest 4: Hacker Shield** 🛡️

### 🎯 **Mission Objective**: 
Create a secure DeploymentConfig with a custom **SCC** and **Service Account**.

### 🧩 **Clue**:
> Only those who wear the right armor (SCC) can survive the harsh world of OpenShift.

### 🛠 **Your Toolkit**:
```bash
# Create SCC
oc apply -f my-scc.yaml

# Create Service Account
oc create serviceaccount secure-sa

# Assign SCC to SA
oc adm policy add-scc-to-user my-scc -z secure-sa

# Deploy app using that SA
oc apply -f secure-app.yaml

# Verify
oc get pods -l app=secure-app
```

### ✅ **Your Mission**:
1. **Create an SCC** that allows **MustRunAsNonRoot**
2. **Create a service account**: `secure-sa`
3. **Deploy any app** (`nginx`, `python`, etc.) using that SA
4. **Confirm the pod is Running**

### 📋 **Required Files**:

#### `my-scc.yaml`:
```yaml
apiVersion: security.openshift.io/v1
kind: SecurityContextConstraints
metadata:
  name: my-scc
allowPrivilegedContainer: false
allowHostNetwork: false
allowHostPorts: false
allowHostPID: false
allowHostIPC: false
readOnlyRootFilesystem: false
runAsUser:
  type: MustRunAsNonRoot
seLinuxContext:
  type: MustRunAs
supplementalGroups:
  type: RunAsAny
users: []
groups: []
volumes:
  - configMap
  - downwardAPI
  - emptyDir
  - persistentVolumeClaim
  - projected
  - secret
priority: 10
```

#### `secure-app.yaml`:
```yaml
apiVersion: apps.openshift.io/v1
kind: DeploymentConfig
metadata:
  name: secure-app
spec:
  replicas: 1
  selector:
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
```

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 4: Hacker Shield

🛡️ SECURITY AUDIT:
- SCC Created: [my-scc]
- Service Account: [secure-sa]
- Pod Status: [running/error]
- Security Context: [non-root user]
- Deployment Success: [yes/no]

🎯 NEXT STEPS:
[What you want to explore next]
```

### 🏅 **Success Criteria**:
- ✅ Successfully created custom SCC
- ✅ Created and configured service account
- ✅ Deployed app with secure context
- ✅ Pod is running with non-root user
- ✅ Posted mission report with findings

### 🎁 **Bonus Challenge**:
- **+50 XP** if you can explain why the SCC is needed
- **+25 XP** if you test the app and verify it's accessible

---

## 📦 **Quest 5: Treasure Storage** 📦

### 🎯 **Mission Objective**: 
Master persistent storage and configuration management.

### 🧩 **Clue**:
> Data is the new gold. Learn to store it safely and access it quickly.

### 🛠 **Your Toolkit**:
```bash
oc get pvc
oc get configmaps
oc get secrets
oc describe pvc [name]
oc describe configmap [name]
```

### ✅ **Your Mission**:
1. **Explore Persistent Volume Claims** (PVCs)
2. **Check ConfigMaps** for configuration data
3. **Review Secrets** (safely!)
4. **Understand storage classes**

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 5: Treasure Storage

📦 STORAGE INVENTORY:
- PVCs Found: [persistent volumes]
- ConfigMaps: [configuration data]
- Secrets: [sensitive data found]
- Storage Classes: [available types]

🎯 NEXT STEPS:
[What you want to explore next]
```

---

## ⚙️ **Quest 6: Assembly Line** 🤖

### 🎯 **Mission Objective**: 
Set up automated CI/CD pipelines for your applications.

### 🧩 **Clue**:
> Automation is the key to DevOps. Build once, deploy everywhere.

### 🛠 **Your Toolkit**:
```bash
oc get pipelineruns
oc get tasks
oc get buildconfigs
oc start-build [build-name]
```

### ✅ **Your Mission**:
1. **Check if pipelines are available**
2. **Explore build configurations**
3. **Start a manual build**
4. **Monitor build progress**

### 🎮 **Mission Report Template**:
```
✅ MISSION COMPLETE - Quest 6: Assembly Line

🤖 AUTOMATION STATUS:
- Pipelines Available: [yes/no]
- Build Configs: [found configurations]
- Build Status: [success/failure]
- Automation Level: [manual/automated]

🎯 NEXT STEPS:
[What you want to explore next]
```

---

## 🌐 **Quest 7: Route Master** 🌍

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

## 🧙 **Quest 8: Magic Mesh** 🧙

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

## 🧠 **Boss Level: Optimize & Monitor** 🧠

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

## 🏆 **Achievement System**

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

## 🎮 **Ready to Start Your Adventure?**

**Begin with Quest 1: Cluster Explorer** and post your mission report to unlock the next quest! 