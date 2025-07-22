Great. Now let’s level up your pipeline skills by introducing **customization and reusability**.

---

# ✅ **Module 5: Azure Pipelines – Variables, Conditions & Templates (Intermediate Level)**

## 🎯 Goal:

Learn to use **variables**, **conditions**, and **templates** in Azure Pipelines to make your YAML pipelines **dynamic**, **reusable**, and **maintainable**.

---

## 🧠 5.1 Why Customize Pipelines?

In real projects:

* You need **different behaviors** based on environments (dev/test/prod)
* You want to **avoid duplication** of steps
* You need to **pass values** dynamically

Customization improves scalability and clarity.

---

## 📦 5.2 Pipeline Variables

### 🔹 Inline Variables

```yaml
variables:
  nodeVersion: '18.x'
  environment: 'dev'
```

Use it:

```yaml
- task: UseNode@1
  inputs:
    version: $(nodeVersion)
```

### 🔹 Runtime Variables from Azure DevOps

Define in:

* **UI** (Pipelines → Edit → Variables)
* Or pipeline YAML via `variables:` block

---

## 🧪 5.3 Hands-on: Add Variables to CI Pipeline

Update `.azure-pipelines/basic-ci.yml` to:

```yaml
trigger:
  - main

variables:
  nodeVersion: '18.x'

pool:
  vmImage: 'ubuntu-latest'

steps:
  - task: UseNode@1
    inputs:
      version: $(nodeVersion)

  - script: |
      echo "Environment: $(Build.SourceBranchName)"
      npm install
    displayName: 'Install Dependencies'
```

Commit and push changes.

---

## 🔄 5.4 Pipeline Conditions

Conditions control when a step runs.

Example: Run a deployment **only on main** branch:

```yaml
- script: echo "Deploying..."
  condition: eq(variables['Build.SourceBranch'], 'refs/heads/main')
```

---

## 📁 5.5 GitHub Documentation

Create:

```
/Module-5-PipelineCustomization/
  - VariablesUsage.md
  - ConditionsDemo.md
  - UpdatedPipeline.yml
  - Screenshot-VariableOutput.png
```

---

## 🔗 5.6 Pipeline Templates (Advanced Reuse)

Break large pipelines into modular pieces.

### 🔧 Example Structure:

```
/.azure-pipelines/
  - ci-template.yml
  - azure-pipeline.yml
```

### ci-template.yml

```yaml
parameters:
  - name: nodeVersion
    type: string
    default: '18.x'

steps:
  - task: UseNode@1
    inputs:
      version: ${{ parameters.nodeVersion }}

  - script: |
      npm install
      npm test
```

### azure-pipeline.yml

```yaml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

stages:
  - stage: Build
    jobs:
      - job: CI
        steps:
          - template: ci-template.yml
            parameters:
              nodeVersion: '20.x'
```

Commit and run — this makes your pipelines cleaner and reusable.

---

## 📌 Summary

You now know how to:

* Define and use pipeline **variables**
* Control execution with **conditions**
* Break pipelines into **reusable templates**

---

### 🔜 Next: **Module 6 – Azure Artifacts & Package Management**

We’ll explore how to manage private packages, dependencies, and versioning using Azure Artifacts.

Continuing…
