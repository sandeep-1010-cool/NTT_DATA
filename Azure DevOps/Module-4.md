Excellent. Let’s now step into automation—the backbone of DevOps.

---

# ✅ **Module 4: Azure Pipelines – CI/CD Fundamentals (Basic Level)**

## 🎯 Goal:

Understand Continuous Integration (CI), write your first **YAML pipeline**, and automate the build process for your project in Azure DevOps.

---

## 🧠 4.1 What is CI/CD?

| Concept                                 | Definition                                                          |
| --------------------------------------- | ------------------------------------------------------------------- |
| **CI (Continuous Integration)**         | Automatically build & test code on every change                     |
| **CD (Continuous Delivery/Deployment)** | Automatically deploy code to environments after successful build    |
| **Pipeline**                            | Workflow defined in YAML that runs steps (like build, test, deploy) |
| **Agent**                               | VM provided by Azure that runs your pipeline jobs                   |

---

## 📦 4.2 Azure Pipelines Components

| Component    | Description                                                      |
| ------------ | ---------------------------------------------------------------- |
| **Pipeline** | Main CI/CD workflow                                              |
| **Job**      | Group of steps running on the same agent                         |
| **Step**     | Single task (e.g., install, build, test)                         |
| **Task**     | Prebuilt action like `DotNetCoreCLI@2`, `CopyFiles@2`, etc.      |
| **Agent**    | VM that executes pipeline jobs (Microsoft-hosted or self-hosted) |

---

## ⚙️ 4.3 Hands-on: Create a Basic CI Pipeline (YAML)

### 🔧 Step 1: Create a New File in Repo

In Cursor IDE:

```bash
cd your-cloned-repo
mkdir .azure-pipelines
touch .azure-pipelines/basic-ci.yml
```

Paste this into `basic-ci.yml`:

```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: UseNode@1
    inputs:
      version: '18.x'
  
  - script: |
      echo "Installing dependencies"
      npm install
    displayName: 'Install Dependencies'

  - script: |
      echo "Running tests"
      npm test
    displayName: 'Run Tests'
```

Push the file:

```bash
git add .
git commit -m "Added basic CI pipeline YAML"
git push origin main
```

---

### 🧪 Step 2: Create Pipeline in Azure DevOps

1. Go to Azure DevOps → Pipelines
2. Click **"New Pipeline"**
3. Choose **Azure Repos Git**
4. Select your repo
5. Choose **YAML**
6. Point to `.azure-pipelines/basic-ci.yml`
7. Save and run

✅ It will spin up a Linux agent and run your CI steps.

---

## 🔍 4.4 Understand What Happened

* `trigger` = Run this pipeline on changes to `main` branch
* `pool` = Use Microsoft-hosted Ubuntu agent
* `steps` = What the agent runs (Node setup → npm install → tests)

---

## 📁 4.5 GitHub Documentation

Create this folder:

```
/Module-4-AzurePipelines/
  - CI-CD-Overview.md
  - BasicCIPipeline.yml
  - StepByStepPipelineCreation.md
  - PipelineRunResultScreenshot.png
```

Document:

* What CI is
* Your YAML file
* Screenshot of the successful run

---

## 📌 Summary

You’ve now:

* Understood CI/CD basics
* Created a working Azure Pipeline with YAML
* Run your first automated workflow on code push

---

### 🔜 Next: **Module 5 – Customize Pipelines with Variables, Conditions, and Templates**

We’ll enhance pipelines with reusable logic, conditional execution, and cleaner structure.

Let’s move on.
