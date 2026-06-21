# BCSL657D — Program 11

## Creating Release Pipelines: Deploying Applications to Azure App Services, Managing Secrets with Azure Key Vault, and Continuous Deployment with Azure Pipelines

---

## 📋 Prerequisites

| Requirement                  | Details                                                      |
| ---------------------------- | ------------------------------------------------------------ |
| Azure DevOps Account         | Created in Program 9 (with organization & project)           |
| Microsoft Azure Account      | Free account at [https://azure.microsoft.com/free](https://azure.microsoft.com/free) |
| GitHub Account               | With a Maven project pushed (from Program 10)                |
| Working Build Pipeline       | Created in Program 10 (builds successfully)                  |
| Internet Connection          | Required throughout                                          |
| Web Browser                  | Chrome / Edge / Firefox                                      |

> ⚠️ **You must have completed Programs 9 and 10 first.** This program builds on the build pipeline created in Program 10.

> ⚠️ **Azure Free Account** provides $200 credit for 30 days. Sign up at [https://azure.microsoft.com/free](https://azure.microsoft.com/free) using your Microsoft account.

---

## 🗂️ Overview

In this program, you will:

1. Create an **Azure App Service** (web app hosting) in the Azure Portal
2. Modify the build pipeline to **publish build artifacts**
3. Create a **Release Pipeline** that deploys artifacts to Azure App Service
4. Enable **Continuous Deployment** (auto-deploy on every successful build)
5. Create an **Azure Key Vault** to manage secrets securely
6. Link Key Vault secrets to the release pipeline
7. Verify the deployed application in the browser

---

## 📖 Theory: What is a Release Pipeline?

A **Release Pipeline** automates deployment of applications **after a successful build**. It is the second half of the CI/CD workflow.

### CI/CD Workflow

```
Developer → GitHub → Azure Pipeline (Build) → Release Pipeline → Azure App Service
    │                      │                         │                    │
    │                  Compile &               Deploy artifact      Live application
    │                  Run tests               to cloud             accessible via URL
    └──────────────────────────────────────────────────────────────────────┘
                              Continuous Integration / Continuous Deployment
```

### Key Concepts

| Concept               | Definition                                                        |
| --------------------- | ----------------------------------------------------------------- |
| **Release Pipeline**  | Automates deployment after a successful build                     |
| **Artifact**          | Build output (JAR, WAR, ZIP) passed from build to release        |
| **Stage**             | Deployment environment (e.g., Dev, Staging, Production)          |
| **Azure App Service** | Microsoft's PaaS for hosting web apps                             |
| **Azure Key Vault**   | Secure storage for passwords, API keys, certificates              |
| **Continuous Deployment** | Automatic deployment triggered by every successful build     |

---

## 🚀 Step-by-Step Execution

# Part A — Create Azure App Service

---

### Step 1: Open Azure Portal

Open your browser and navigate to:

```
https://portal.azure.com
```

Sign in with your **Microsoft account** (the same one used for Azure DevOps).

---

### Step 2: Create a Web App (App Service)

1. In the Azure Portal, click **+ Create a resource** (top-left)
2. Search for **Web App** in the search bar
3. Click **Web App** → Click **Create**

---

### Step 3: Configure the Web App

Fill in the form:

| Field                  | Value                                                        |
| ---------------------- | ------------------------------------------------------------ |
| **Subscription**       | Your subscription (e.g., `Azure for Students` or `Free Trial`) |
| **Resource Group**     | Click **Create new** → Enter: `devops-lab-rg` → Click OK   |
| **Name**               | Choose a unique name (e.g., `devops-lab-app-yourname`)       |
| **Publish**            | Code                                                          |
| **Runtime stack**       | Java 8 / Java SE                                             |
| **Operating System**   | Linux                                                         |
| **Region**             | Choose nearest (e.g., `Central India` or `East US`)          |

> ⚠️ The **Name** must be globally unique. It becomes your app URL: `https://your-app-name.azurewebsites.net`

4. For **App Service Plan**:
   - Click **Create new** → Name: `devops-lab-plan`
   - **Pricing Plan**: Select **Free F1** (for lab purposes)

5. Click **Review + create** → Click **Create**

6. Wait for deployment to complete (1-2 minutes), then click **Go to resource**

---

### Step 4: Verify the App Service

1. On the App Service **Overview** page, find the **URL**:
   ```
   https://devops-lab-app-yourname.azurewebsites.net
   ```
2. Click the URL or open it in a new tab
3. You should see a **default placeholder page** (this will be replaced after deployment)

> Note down your App Service name — you'll need it later.

---

# Part B — Modify Build Pipeline to Publish Artifacts

---

### Step 5: Open Azure DevOps

Navigate to:
```
https://dev.azure.com
```

Sign in and open your **project** (from Program 10).

---

### Step 6: Edit the Build Pipeline

1. Go to **Pipelines** (left sidebar)
2. Click on your existing pipeline (created in Program 10)
3. Click **Edit** (top-right)

---

### Step 7: Add Artifact Publishing Step

In the YAML editor, **replace ALL content** with the following:

```yaml
trigger:
- devops

pool:
  vmImage: ubuntu-latest

steps:
- task: Maven@3
  displayName: 'Maven Build and Test'
  inputs:
    mavenPomFile: 'pom.xml'
    goals: 'clean package'
    publishJUnitResults: true
    testResultsFiles: '**/surefire-reports/TEST-*.xml'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '1.8'

- task: CopyFiles@2
  displayName: 'Copy JAR to Staging Directory'
  inputs:
    sourceFolder: '$(Build.SourcesDirectory)/target'
    contents: '*.jar'
    targetFolder: '$(Build.ArtifactStagingDirectory)'

- task: PublishBuildArtifacts@1
  displayName: 'Publish Build Artifact'
  inputs:
    pathToPublish: '$(Build.ArtifactStagingDirectory)'
    artifactName: 'drop'
    publishLocation: 'Container'
```

**New tasks explained:**

| Task                        | Purpose                                                          |
| --------------------------- | ---------------------------------------------------------------- |
| `CopyFiles@2`              | Copies the built JAR from `target/` to a staging directory       |
| `PublishBuildArtifacts@1`   | Publishes the staged files as a downloadable artifact named `drop` |

> The `drop` artifact is what the Release Pipeline picks up for deployment.

---

### Step 8: Save and Run the Updated Pipeline

1. Click **Save and run**
2. Commit message: `Add artifact publishing step`
3. Click **Save and run**
4. Wait for the pipeline to complete successfully

**Verify:** After the build succeeds, click on the build run → you should see a **published artifact** section showing `drop` with the JAR file inside.

---

# Part C — Create the Release Pipeline

---

### Step 9: Navigate to Releases

1. Go to **Pipelines** → **Releases** (left sidebar)
2. Click **New pipeline** (or **New release pipeline**)

---

### Step 10: Select Deployment Template

1. A panel appears on the right: **Select a template**
2. Search for or select: **Azure App Service deployment**
3. Click **Apply**

> A stage named **Stage 1** is created automatically.

---

### Step 11: Add the Build Artifact

1. In the pipeline diagram, click **+ Add an artifact** (left box)
2. Configure:

| Field                 | Value                                               |
| --------------------- | --------------------------------------------------- |
| **Source type**        | Build                                                |
| **Project**           | Your Azure DevOps project name                       |
| **Source (build pipeline)** | Select your build pipeline (from Program 10)  |
| **Default version**   | Latest                                               |
| **Source alias**       | `_drop` (auto-filled)                               |

3. Click **Add**

---

### Step 12: Configure Stage 1 — Azure App Service Deployment

1. Click on **Stage 1** (the box in the pipeline diagram)
   - Or click **1 job, 1 task** link under Stage 1
2. You'll see the deployment task configuration

3. Fill in:

| Field                    | Value                                                     |
| ------------------------ | --------------------------------------------------------- |
| **Stage name**           | `Deploy to App Service`                                    |
| **Azure subscription**   | Select your Azure subscription → Click **Authorize**      |
| **App type**             | Web App on Linux                                           |
| **App service name**     | Select your App Service (e.g., `devops-lab-app-yourname`) |
| **Package or folder**    | `$(System.DefaultWorkingDirectory)/_drop/drop`            |

> ⚠️ When you click **Authorize**, Azure DevOps will create a service connection to your Azure subscription. You may need to sign in and grant permissions.

4. Click **Save** (top toolbar) → Click **OK** when prompted

---

### Step 13: Enable Continuous Deployment Trigger

1. Go back to the **pipeline diagram** view
2. Click the **lightning bolt icon** ⚡ on the artifact box (top-right corner of the artifact)
3. Toggle **Continuous deployment trigger** to **Enabled**
4. Under **Build branch filters**, add:
   - Branch: `devops`

5. Click **Save** → Click **OK**

> Now, every time a build succeeds on the `devops` branch, a release is **automatically created and deployed**.

---

### Step 14: Create a Release Manually (First Time)

1. Click **+ Create release** (top-right)
2. Select the stage: `Deploy to App Service` ✅
3. Click **Create**
4. Click on the **release name** (e.g., `Release-1`) to view it
5. Click on **Stage 1** → **Deploy** → **Confirm**

---

### Step 15: Monitor the Release

1. Click on the **stage** to see the deployment logs
2. You'll see:

| Phase                        | What Happens                                          |
| ---------------------------- | ----------------------------------------------------- |
| **Initialize job**           | Sets up the deployment agent                          |
| **Download artifact**        | Downloads the JAR from the build artifact (`drop`)    |
| **Deploy Azure App Service** | Uploads and deploys the JAR to your App Service       |
| **Post-deployment**          | Cleanup and finalization                              |

**Expected log (key lines):**
```
Downloading artifact _drop/drop
Successfully downloaded artifacts
Deploying to Azure App Service: devops-lab-app-yourname
Successfully deployed web package to App Service
```

---

### Step 16: Verify the Deployment

Open your browser and navigate to:

```
https://devops-lab-app-yourname.azurewebsites.net
```

> ⚠️ Replace `devops-lab-app-yourname` with your actual App Service name.

You should see your application running (or the App Service default page if the JAR is a console app rather than a web app).

---

# Part D — Azure Key Vault (Managing Secrets)

---

### Step 17: Create an Azure Key Vault

1. Go to **Azure Portal**: [https://portal.azure.com](https://portal.azure.com)
2. Click **+ Create a resource**
3. Search for **Key Vault** → Click **Key Vault** → Click **Create**

---

### Step 18: Configure the Key Vault

Fill in the form:

| Field                | Value                                                     |
| -------------------- | --------------------------------------------------------- |
| **Subscription**     | Your subscription                                          |
| **Resource group**   | Select existing: `devops-lab-rg`                          |
| **Key vault name**   | Choose unique name (e.g., `devops-lab-kv-yourname`)       |
| **Region**           | Same region as your App Service                            |
| **Pricing tier**     | Standard                                                   |

Click **Review + create** → Click **Create**

Wait for deployment, then click **Go to resource**.

---

### Step 19: Add a Secret to the Key Vault

1. In the Key Vault resource, go to **Secrets** (left sidebar under "Objects")
2. Click **+ Generate/Import**
3. Fill in:

| Field              | Value                           |
| ------------------ | ------------------------------- |
| **Upload options** | Manual                          |
| **Name**           | `DB-PASSWORD`                   |
| **Secret value**   | `MySecurePassword123!`          |
| **Enabled**        | Yes                             |

4. Click **Create**

> You can add multiple secrets (e.g., `API-KEY`, `DB-CONNECTION-STRING`, etc.)

---

### Step 20: Link Key Vault with Release Pipeline

1. Go back to **Azure DevOps**: [https://dev.azure.com](https://dev.azure.com)
2. Go to **Pipelines** → **Library** (left sidebar)
3. Click **+ Variable group**
4. Configure:

| Field                        | Value                                            |
| ---------------------------- | ------------------------------------------------ |
| **Variable group name**      | `KeyVault-Secrets`                                |
| **Description**              | Secrets from Azure Key Vault                      |

5. Toggle **Link secrets from an Azure key vault as variables** to **ON**
6. Select:
   - **Azure subscription**: Your subscription (authorize if needed)
   - **Key vault name**: Select `devops-lab-kv-yourname`

7. Click **+ Add** under Variables
8. Select the secrets you want to use (e.g., `DB-PASSWORD`)
9. Click **OK**
10. Click **Save** (top toolbar)

---

### Step 21: Use Key Vault Secrets in the Release Pipeline

1. Go to **Pipelines** → **Releases**
2. Click on your release pipeline → Click **Edit**
3. Go to **Variables** tab
4. Click **Variable groups** → Click **Link variable group**
5. Select: `KeyVault-Secrets`
6. **Scope**: Select `Deploy to App Service` (or Release)
7. Click **Link** → Click **Save**

> Now the `DB-PASSWORD` secret is available in your release pipeline as `$(DB-PASSWORD)`. It can be used in deployment tasks, scripts, or app settings without exposing the actual value.

---

# Part E — Verify Continuous Deployment

---

### Step 22: Trigger Automatic Deployment

To test the full CI/CD cycle, make a small change and push it:

1. Open Command Prompt and navigate to your project folder:
   ```cmd
   cd program10\azure-maven-app
   ```

2. Edit `App.java`:
   ```cmd
   notepad src\main\java\com\example\App.java
   ```

3. Change the first `println` line to:
   ```java
   System.out.println("Hello from Azure DevOps CI/CD Pipeline!");
   ```

4. Commit and push:
   ```cmd
   git add .
   git commit -m "Updated message for CI/CD test"
   git push origin devops
   ```

**What happens automatically:**
```
Git Push → Azure Pipeline (Build) triggered
         → Build succeeds → Artifact published
         → Release Pipeline triggered (Continuous Deployment)
         → Application deployed to Azure App Service
```

---

### Step 23: Monitor the Automatic Cycle

1. Go to **Azure DevOps** → **Pipelines**
   - You should see a new build running automatically
2. After the build succeeds, go to **Pipelines** → **Releases**
   - A new release should be created automatically
3. Monitor the release until it succeeds

---

## ✅ Summary of All Steps (Quick Reference)

### Part A — Azure App Service (Azure Portal)

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 1    | Open `https://portal.azure.com` → Sign in                            |
| 2    | Create a resource → Web App → Create                                  |
| 3    | Resource Group: `devops-lab-rg`, Name: unique, Runtime: Java 8, Plan: Free F1 |
| 4    | Verify URL: `https://your-app.azurewebsites.net`                      |

### Part B — Publish Artifacts (Azure DevOps)

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 5-6  | Edit existing build pipeline                                           |
| 7    | Add `CopyFiles@2` + `PublishBuildArtifacts@1` tasks to YAML           |
| 8    | Save and run → Verify `drop` artifact is published                    |

### Part C — Release Pipeline (Azure DevOps)

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 9    | Pipelines → Releases → New pipeline                                   |
| 10   | Template: Azure App Service deployment                                 |
| 11   | Add artifact: Source = Build pipeline                                  |
| 12   | Configure stage: Azure subscription + App Service name                |
| 13   | Enable Continuous Deployment trigger ⚡                                |
| 14   | Create release manually → Deploy                                      |
| 15   | Monitor deployment logs                                                |
| 16   | Verify at `https://your-app.azurewebsites.net`                        |

### Part D — Key Vault (Azure Portal + DevOps)

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 17-18| Azure Portal → Create Key Vault                                       |
| 19   | Add Secret: Name = `DB-PASSWORD`                                       |
| 20   | Azure DevOps → Library → Variable group → Link Key Vault             |
| 21   | Release Pipeline → Variables → Link variable group                    |

### Part E — Verify CI/CD

```cmd
cd program10\azure-maven-app
notepad src\main\java\com\example\App.java
git add .
git commit -m "Updated message for CI/CD test"
git push origin devops
```

Then monitor: Pipelines → build triggers → release triggers → deployed automatically.

---

## 📝 Theory Notes

### Release Pipeline vs Build Pipeline

| Aspect            | Build Pipeline                    | Release Pipeline                     |
| ----------------- | --------------------------------- | ------------------------------------ |
| **Purpose**       | Compile, test, create artifacts   | Deploy artifacts to environments     |
| **Trigger**       | Code push (CI)                    | Successful build (CD)                |
| **Output**        | JAR/WAR/ZIP artifact              | Running application in cloud         |
| **Configuration** | YAML file                         | Visual designer (UI-based)           |

### Azure App Service

Azure App Service is a **Platform-as-a-Service (PaaS)** that lets you build and host web apps without managing infrastructure.

| Feature              | Details                                              |
| -------------------- | ---------------------------------------------------- |
| **Supported Languages** | Java, .NET, Node.js, Python, PHP, Ruby           |
| **Auto-scaling**     | Scale up/out based on traffic                        |
| **Custom Domains**   | Map your own domain name                             |
| **SSL/TLS**          | Free SSL certificates available                      |
| **Pricing**          | Free tier available (F1 plan)                        |

### Azure Key Vault

Azure Key Vault is a **secret management service** that provides:

| Feature              | Purpose                                              |
| -------------------- | ---------------------------------------------------- |
| **Secrets**          | Store passwords, API keys, connection strings        |
| **Keys**             | Cryptographic keys for encryption                    |
| **Certificates**     | SSL/TLS certificate management                       |
| **Access Policies**  | Control who/what can access the vault                |
| **Audit Logging**    | Track all access to secrets                          |

### Why Use Key Vault?

| Without Key Vault ❌                    | With Key Vault ✅                         |
| --------------------------------------- | ----------------------------------------- |
| Passwords in config files               | Passwords stored securely in vault        |
| Secrets visible in source code          | Secrets injected at deployment time       |
| Hard to rotate credentials              | Easy secret rotation                      |
| No audit trail                          | Full access logging                       |

### Continuous Deployment Flow

```
┌──────────────┐     ┌─────────────────┐     ┌──────────────────┐     ┌─────────────┐
│  Developer   │────▶│  Build Pipeline  │────▶│ Release Pipeline  │────▶│ Azure App   │
│  pushes code │     │  (CI: compile,   │     │ (CD: deploy to   │     │ Service     │
│  to GitHub   │     │   test, package) │     │  App Service)    │     │ (Live App)  │
└──────────────┘     └─────────────────┘     └──────────────────┘     └─────────────┘
     trigger ──────▶      artifact ──────▶     continuous ──────▶       verified
     (git push)           (drop/JAR)          deployment               (browser)
```

---

## ⚠️ Troubleshooting

| Problem                                           | Solution                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------- |
| Azure Portal says "No subscriptions found"        | Sign up for free at `https://azure.microsoft.com/free` with the same account   |
| App Service creation fails — "name already taken"  | App names are globally unique; try a different name like `devops-yourname-123` |
| Free F1 plan not available                         | Select a different region where F1 is available                                |
| "Authorize" button fails for Azure subscription    | Ensure your Azure and DevOps accounts use the same Microsoft account           |
| Release fails — "Resource not found"               | Verify App Service name matches exactly in the release stage config            |
| Artifact not found by release pipeline             | Ensure build pipeline has `PublishBuildArtifacts@1` task and succeeded          |
| Key Vault not visible in variable group            | Ensure Key Vault is in the same Azure subscription connected to DevOps         |
| "Access denied" when linking Key Vault             | In Key Vault → Access policies → Add your Azure DevOps service principal       |
| Continuous Deployment not triggering               | Check the ⚡ trigger is enabled and branch filter matches `devops`             |
| Pipeline stays "Queued"                            | Request free parallelism at `https://aka.ms/azpipelines-parallelism-request`   |
| App shows default page after deployment            | Console apps (non-web) won't show in browser; check deployment logs for success|

---

**Program 11 Complete ✔**
