Let’s move forward into package and dependency management—an essential part of modern DevOps.

---

# ✅ **Module 6: Azure Artifacts – Package Management (Intermediate Level)**

## 🎯 Goal:

Understand how to **create**, **publish**, and **consume** packages (like NuGet, npm, Maven, Python) using **Azure Artifacts** for secure and centralized dependency management.

---

## 🧠 6.1 What is Azure Artifacts?

**Azure Artifacts** is a service inside Azure DevOps that provides:

| Feature                   | Description                                        |
| ------------------------- | -------------------------------------------------- |
| **Private package feeds** | Host npm, NuGet, Python, Maven packages securely   |
| **Scoped access**         | Control who can access what                        |
| **Versioning**            | Manage and consume different versions of libraries |
| **Upstream sources**      | Integrate with public registries like npmjs.com    |

---

## 📦 6.2 Use Cases

* Host **internal npm libraries**
* Share **.NET NuGet packages** across teams
* Create **versioned build artifacts**
* Use a **single source of truth** for dependencies

---

## 🛠️ 6.3 Hands-on: Set Up an Artifact Feed

### Step 1: Create a Feed

1. Go to Azure DevOps → **Artifacts**
2. Click **"New Feed"**
3. Name: `my-devops-feed`
4. Scope: This project
5. Visibility: Private
6. Enable upstream sources (optional)

✅ Your feed is ready.

---

## ⚙️ 6.4 Publish and Consume npm Package

Let’s simulate an internal package workflow:

### Step 2: Init a Sample npm Package (in Cursor IDE)

```bash
mkdir my-package
cd my-package
npm init -y
echo "module.exports = () => console.log('Hello from Azure Artifacts!');" > index.js
```

Update `package.json`:

```json
"scripts": {
  "publish": "npm publish"
}
```

### Step 3: Configure `.npmrc`

Create a `.npmrc` file:

```ini
registry=https://pkgs.dev.azure.com/YOUR_ORG/_packaging/my-devops-feed/npm/registry/
always-auth=true
```

Then, login:

```bash
npm login --registry=https://pkgs.dev.azure.com/YOUR_ORG/_packaging/my-devops-feed/npm/registry/ \
  --scope=@your-scope
```

> Use a **Personal Access Token (PAT)** as the password (with packaging scopes enabled).

### Step 4: Publish

```bash
npm publish --access=restricted
```

Your package is now hosted in Azure Artifacts 🎉

---

## 📁 6.5 GitHub Documentation

Create:

```
/Module-6-AzureArtifacts/
  - ArtifactsOverview.md
  - CreatingFeedSteps.md
  - PublishingPackageSteps.md
  - Screenshot-Feed-Publish.png
```

Include:

* `.npmrc` sample
* Feed link
* Screenshot from Azure DevOps → Artifacts → Feed → Package

---

## 🧪 Optional Practice

* Try consuming your package from another repo or project
* Try publishing a `.nupkg` or `.whl` file if you work with .NET or Python

---

## 📌 Summary

You’ve learned:

* What Azure Artifacts is
* How to create a feed
* How to publish a package
* How to document and manage internal dependencies securely

---

### 🔜 Next: **Module 7 – Azure Environments & Deployment Strategy (Intermediate Level)**

We’ll now move to **deployment pipelines**, using **environments** like dev/stage/prod and safe strategies like **manual approvals, blue-green, and canary**.

Continuing…
