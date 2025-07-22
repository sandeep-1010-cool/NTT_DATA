Excellent. Let’s proceed.

---

# ✅ **Module 3: Azure Boards – Agile Project & Task Tracking (Basic Level)**

## 🎯 Goal:

Understand how to manage work using **Azure Boards** with Epics, Features, User Stories, Tasks, and Bugs. You’ll create and manage work items for a real development workflow.

---

## 🧠 3.1 What is Azure Boards?

**Azure Boards** is a tool for **project management** within Azure DevOps. It helps DevOps teams plan, track, and discuss work across the lifecycle.

It supports:

* Agile, Scrum, and Kanban workflows
* Work items (epics → features → stories/tasks → bugs)
* Dashboards and reporting

---

## 🔗 3.2 Azure Boards Hierarchy

| Level          | Description              | Example                              |
| -------------- | ------------------------ | ------------------------------------ |
| **Epic**       | Large body of work       | "Build E-commerce Platform"          |
| **Feature**    | Component of Epic        | "Implement Checkout Module"          |
| **User Story** | Specific requirement     | "As a user, I want to apply coupons" |
| **Task**       | Action to complete story | "Code the coupon logic"              |
| **Bug**        | Defect in the system     | "Coupon not applying on mobile"      |

---

## ⚙️ 3.3 Hands-on: Create Work Items

### Step 1: Go to Azure Boards

1. In Azure DevOps → Select your project
2. Click **Boards** → **Work Items**

### Step 2: Create Work Items

#### 🔹 Create an Epic

* Click **"New Work Item"** → **Epic**
* Title: `Develop My DevOps Demo App`

#### 🔹 Create a Feature

* Click **New Work Item** → **Feature**
* Title: `CI/CD Pipeline Integration`
* Link it to the Epic:

  * Open the feature → Add link → **Parent** → Select Epic

#### 🔹 Create User Stories

* Title: `As a dev, I want to auto-build my app on push`
* Link it under the Feature

#### 🔹 Add Tasks

* Title: `Write build pipeline YAML`
* Parent: User Story above

#### 🔹 Create a Bug (Optional)

* Title: `Pipeline fails when repo is empty`
* Severity: Medium

---

## 🧪 3.4 Practice Board Setup

1. Create this full hierarchy manually in Boards:

   ```
   Epic: My DevOps Learning Project
     └── Feature: Learn CI/CD Pipelines
           └── User Story: Set up a basic CI pipeline
                 ├── Task: Write pipeline YAML
                 └── Task: Test pipeline on push
   ```

2. Move work items across the **Boards → Backlogs → Kanban board** (drag-drop to change state: New → Active → Resolved)

---

## 📁 3.5 GitHub Documentation

Create a folder:

```
/Module-3-AzureBoards/
  - AzureBoardsOverview.md
  - WorkItemHierarchy.md
  - MyBoardSetupSteps.md
  - AzureBoardsScreenshots.png
```

---

## 📌 Summary:

You now understand and practiced:

* Agile structure inside Azure Boards
* Creating Epics → Features → User Stories → Tasks
* Using Boards for visual task management

---

### 🔜 Next: **Module 4 – Azure Pipelines: Basics of CI/CD**

We’ll now dive into automation—configuring your **first CI pipeline** using YAML, tied to the repo you created earlier.

Let’s continue.
