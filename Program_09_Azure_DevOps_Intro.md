# BCSL657D — Program 9

## Introduction to Azure DevOps: Creating an Azure DevOps Account, Project, Repository, and Basic Pipeline

---

## 📋 Prerequisites

| Requirement          | Details                                                  |
| -------------------- | -------------------------------------------------------- |
| Internet Connection  | Required throughout                                      |
| Web Browser          | Chrome / Edge / Firefox                                  |
| Microsoft Account    | Gmail or Outlook account (for signing in to Azure DevOps)|

> ⚠️ If you don't have a Microsoft account, create one for free at [https://signup.live.com](https://signup.live.com)

---

## 🗂️ Overview

In this program, you will:

1. Understand Azure DevOps and its services
2. Create a free Azure DevOps account
3. Create an organization and a new project
4. Explore the Azure DevOps dashboard
5. Create a Git repository with a README file
6. Create and run a basic starter pipeline

---

## 📖 Theory: What is Azure DevOps?

Azure DevOps is a **cloud-based DevOps platform** provided by Microsoft for managing the entire software development lifecycle.

### Azure DevOps Services

| Service              | Purpose                                          |
| -------------------- | ------------------------------------------------ |
| **Azure Repos**      | Git repositories for source code management      |
| **Azure Pipelines**  | Build and deployment automation (CI/CD)          |
| **Azure Boards**     | Work tracking, task management, Kanban boards    |
| **Azure Test Plans** | Manual and automated software testing            |
| **Azure Artifacts**  | Package management (NuGet, npm, Maven, etc.)     |

### Architecture Flow

```
Developer → Azure Repos → Azure Pipelines → Build/Test → Deployment
```

---

## 🚀 Step-by-Step Execution

### Step 1: Open Azure DevOps Website

Open your browser and navigate to:

```
https://dev.azure.com
```

Or go to:

```
https://azure.microsoft.com/en-us/products/devops
```

---

### Step 2: Sign In / Start Free

1. Click **Start free** (or **Sign in** if you already have an account)
2. Sign in using your **Microsoft account** (Gmail or Outlook)
3. If prompted, accept the terms of service

> ⚠️ If this is your first time, Microsoft may ask you to verify your email or phone number.

---

### Step 3: Create an Azure DevOps Organization

After signing in, you'll be prompted to create an organization:

1. **Enter organization name**: Choose a unique name (e.g., `your-name-devops`)
   ```
   Example: ramesh-devops
   ```
2. **Select region**: Choose the closest region (e.g., `South India`, `Central US`)
3. Click **Continue**

> Your organization URL will be: `https://dev.azure.com/your-organization-name`

---

### Step 4: Create a New Project

After the organization is created, you'll see the **"Create a project"** page:

1. **Project name**:
   ```
   DevOpsLabProject
   ```
2. **Description** (optional):
   ```
   BCSL657D Lab Program 9 - Introduction to Azure DevOps
   ```
3. **Visibility**: Select **Private** (recommended for lab work)
   > `Private` = Only you and invited members can access  
   > `Public` = Anyone on the internet can view
4. Click **Create**

---

### Step 5: Explore the Azure DevOps Dashboard

After the project is created, you'll land on the **Project Dashboard**. Explore these sections from the **left sidebar**:

| Section        | What You'll See                                          |
| -------------- | -------------------------------------------------------- |
| **Overview**   | Project summary, wiki, dashboards                        |
| **Boards**     | Work items, Kanban board, sprints, backlogs              |
| **Repos**      | Git repositories, files, commits, branches, pull requests|
| **Pipelines**  | Build and release pipelines, environments                |
| **Test Plans** | Manual test plans and test suites                        |
| **Artifacts**  | Package feeds (NuGet, npm, Maven)                        |

> Take a moment to click on each section and familiarize yourself with the layout.

---

### Step 6: Create a Repository with README

1. Go to **Repos** → **Files** (from the left sidebar)
2. You'll see a page that says *"DevOpsLabProject is empty. Add some code!"*
3. Under **Initialize main branch with a README or .gitignore**, check:
   ```
   ✔ Add a README
   ```
4. Click **Initialize**

> Azure DevOps creates a Git repository with a `README.md` file on the `main` branch. You should now see the file browser with your `README.md`.

---

### Step 7: Verify the Repository

After initialization, you should see:

- **Branch**: `main`
- **Files**: `README.md`
- **Commit history**: 1 initial commit

> You can click on `README.md` to view its contents, or click **Edit** to modify it directly in the browser.

---

### Step 8: Create a Basic Pipeline

1. Go to **Pipelines** (from the left sidebar)
2. Click **Create Pipeline**
3. **Where is your code?** → Select **Azure Repos Git**
4. **Select a repository** → Select **DevOpsLabProject**
5. **Configure your pipeline** → Select **Starter pipeline**

> Azure DevOps generates a basic YAML pipeline file.

---

### Step 9: Review the Starter Pipeline YAML

You'll see the auto-generated `azure-pipelines.yml` with content similar to:

```yaml
# Starter pipeline
# Start with a minimal pipeline that you can customize to build and deploy your code.
# Add steps that build, run tests, deploy, and more:
# https://aka.ms/yaml

trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- script: echo Hello, world!
  displayName: 'Run a one-line script'

- script: |
    echo Add other tasks to build, test, and deploy your project.
    echo See https://aka.ms/yaml
  displayName: 'Run a multi-line script'
```

**Key elements explained:**

| Element                    | Purpose                                                    |
| -------------------------- | ---------------------------------------------------------- |
| `trigger: - main`         | Pipeline triggers automatically when code is pushed to `main` branch |
| `pool: vmImage: ubuntu-latest` | Uses a Microsoft-hosted Ubuntu virtual machine to run the build |
| `steps:`                   | List of tasks/scripts to execute                           |
| `- script: echo Hello`    | A simple shell command that prints text                    |
| `displayName:`            | Human-readable name shown in the pipeline UI               |

> ⚠️ **Do NOT modify** the YAML for now — use the starter pipeline as-is.

---

### Step 10: Save and Run the Pipeline

1. Click **Save and run** (top-right button)
2. A dialog appears:
   - **Commit message**: Leave default or enter:
     ```
     Add azure-pipelines.yml
     ```
   - **Branch**: `main`
3. Click **Save and run**

> Azure DevOps commits the `azure-pipelines.yml` file to your repository and immediately starts the pipeline.

---

### Step 11: Monitor the Pipeline Execution

After clicking "Save and run":

1. You'll be taken to the **pipeline run** page
2. You'll see the pipeline **stages and jobs** executing
3. Click on **Job** to see the detailed logs

**Observe the following during execution:**

| Phase               | What Happens                                            |
| -------------------- | ------------------------------------------------------- |
| **Initialize job**   | Sets up the virtual machine agent                       |
| **Checkout**         | Clones your repository from Azure Repos                 |
| **Run a one-line script** | Executes `echo Hello, world!`                     |
| **Run a multi-line script** | Executes the multi-line echo commands            |
| **Post-job cleanup** | Cleans up the agent                                     |

---

### Step 12: Verify the Pipeline Results

After the pipeline finishes:

1. The status should show: ✅ **Success** (green checkmark)
2. In the logs, you should see:
   ```
   Hello, world!
   Add other tasks to build, test, and deploy your project.
   See https://aka.ms/yaml
   ```
3. Go back to **Pipelines** → You'll see your pipeline listed with status **Succeeded**

---

## ✅ Summary of All Steps (Quick Reference)

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 1    | Open `https://dev.azure.com`                                           |
| 2    | Click **Start free** → Sign in with Microsoft account                 |
| 3    | Create Organization → Enter name → Select region → Continue          |
| 4    | Create Project → Name: `DevOpsLabProject` → Visibility: Private → Create |
| 5    | Explore Dashboard: Boards, Repos, Pipelines, Test Plans, Artifacts    |
| 6    | Repos → Files → Initialize with README → Click Initialize            |
| 7    | Verify: `README.md` visible on `main` branch                          |
| 8    | Pipelines → Create Pipeline → Azure Repos Git → Select repo → Starter pipeline |
| 9    | Review the auto-generated YAML                                         |
| 10   | Click **Save and run** → Commit to `main` → Save and run             |
| 11   | Monitor: Click on Job to see live logs                                |
| 12   | Verify: Pipeline status = **Succeeded** ✅                             |

---

## 📝 Theory Notes

### What is CI/CD?

| Term                         | Meaning                                                          |
| ---------------------------- | ---------------------------------------------------------------- |
| **Continuous Integration**   | Automatically build and test code when changes are committed     |
| **Continuous Delivery**      | Automatically prepare code for release to production             |
| **Continuous Deployment**    | Automatically deploy code to production after all tests pass     |

### Azure DevOps vs Other CI/CD Tools

| Feature           | Azure DevOps       | Jenkins            | GitHub Actions      |
| ----------------- | ------------------ | ------------------ | ------------------- |
| Hosting           | Cloud (Microsoft)  | Self-hosted        | Cloud (GitHub)      |
| Configuration     | YAML               | UI + Groovy        | YAML                |
| Free Tier         | 5 users free       | Free (open-source) | 2000 min/month free |
| Integration       | Azure ecosystem    | Plugin-based       | GitHub ecosystem    |
| Boards/Tracking   | Built-in           | Not included       | Issues/Projects     |

### YAML Pipeline Structure

```yaml
trigger:          # When to run (e.g., on push to main)
  - main

pool:             # What machine to run on
  vmImage: ubuntu-latest

steps:            # What to do
  - script: ...   # Shell commands
  - task: ...     # Pre-built tasks (Maven, Docker, etc.)
```

### Azure DevOps Key Terminology

| Term              | Definition                                                     |
| ----------------- | -------------------------------------------------------------- |
| **Organization**  | Top-level container that holds projects                        |
| **Project**       | Contains repos, pipelines, boards, and test plans              |
| **Repository**    | Git repo storing source code                                   |
| **Pipeline**      | Automated workflow for build/test/deploy                       |
| **Agent**         | Machine (VM) that executes pipeline tasks                      |
| **Artifact**      | Build output (JAR, WAR, Docker image, etc.)                    |
| **Trigger**       | Event that starts a pipeline (push, PR, schedule)              |

---

## ⚠️ Troubleshooting

| Problem                                     | Solution                                                                    |
| ------------------------------------------- | --------------------------------------------------------------------------- |
| "Start free" button not visible             | Try direct URL: `https://dev.azure.com` and sign in                        |
| Cannot create organization                  | Verify your Microsoft account email is confirmed                           |
| Project creation fails                      | Try a different project name (must be unique within the organization)       |
| Repos page shows empty                      | Click "Initialize" with README checked                                     |
| Pipeline creation — no "Azure Repos Git"    | Ensure you initialized the repository in Step 6 first                      |
| Pipeline fails — agent not available        | Free tier has limited parallel jobs; wait and retry                         |
| Pipeline stuck in "Queued" state            | Go to Project Settings → Pipelines → Parallel jobs → Request free grant    |
| YAML syntax error                           | Use the starter pipeline without modifications; check indentation          |
| "No hosted parallelism has been purchased"  | Fill the form at `https://aka.ms/azpipelines-parallelism-request` (takes ~2 days to approve) |
| Cannot see pipeline logs                    | Click on the **Job** name (not just the pipeline run) to see detailed logs |

> ⚠️ **Common Issue:** Microsoft may require you to request free parallelism for new organizations. If your pipeline stays "Queued" indefinitely, visit `https://aka.ms/azpipelines-parallelism-request` and submit a request. Approval takes 1-3 business days.

---

**Program 9 Complete ✔**
