# BCSL657D — Program 10

## Creating Build Pipelines Using Azure DevOps: Integrating GitHub/Azure Repos, Building Maven/Gradle Projects, and Running Automated Unit Tests

---

## 📋 Prerequisites

| Requirement                | Details                                                    |
| -------------------------- | ---------------------------------------------------------- |
| Azure DevOps Account       | Created in Program 9 (with organization & project)         |
| GitHub Account             | [https://github.com](https://github.com) — free account    |
| Java JDK (8+)              | Verify: `java -version`                                    |
| Maven                      | Verify: `mvn -v`                                           |
| Git                        | Verify: `git --version`                                    |
| Internet Connection        | Required throughout                                        |
| Web Browser                | Chrome / Edge / Firefox                                    |

> ⚠️ **You must have completed Program 9** (Azure DevOps account and organization ready). If not, complete Program 9 first.

---

## 🗂️ Overview

In this program, you will:

1. Create a Maven project with a Java application and JUnit tests
2. Initialize a Git repository and push the project to **GitHub**
3. Create a **Build Pipeline** in Azure DevOps
4. Connect the pipeline to your **GitHub repository**
5. Configure a **YAML pipeline** with Maven build tasks
6. Run the pipeline and observe **automated builds and test results**

---

## 📖 Theory: What is Azure Pipeline?

Azure Pipeline is a **CI/CD service** provided by Azure DevOps that automatically:

- ✅ Builds applications from source code
- ✅ Runs unit tests automatically
- ✅ Generates build and test reports
- ✅ Deploys applications continuously

### Workflow

```
Developer → GitHub/Azure Repos → Azure Pipeline → Build → Test → Report
```

---

## 🚀 Step-by-Step Execution

# Part A — Prepare the Maven Project Locally

---

### Step 1: Create a Working Folder

Open **Command Prompt** and run:

```cmd
mkdir program10
cd program10
```

---

### Step 2: Generate the Maven Project

```cmd
mvn archetype:generate -DgroupId=com.example -DartifactId=azure-maven-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

**Expected Output (last few lines):**
```
[INFO] BUILD SUCCESS
```

---

### Step 3: Navigate into the Project

```cmd
cd azure-maven-app
```

---

### Step 4: Edit the `pom.xml` File

```cmd
notepad pom.xml
```

**Delete ALL existing content**, then **copy-paste the entire block below** and save (`Ctrl + S`):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>azure-maven-app</artifactId>
    <version>1.0-SNAPSHOT</version>
    <packaging>jar</packaging>

    <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

    <dependencies>
        <!-- JUnit Dependency for Unit Testing -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Maven Compiler Plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- Maven Surefire Plugin for running tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

---

### Step 5: Edit the `App.java` File

```cmd
notepad src\main\java\com\example\App.java
```

**Delete ALL existing content**, then **copy-paste the entire block below** and save:

```java
package com.example;

public class App {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public static void main(String[] args) {
        App app = new App();

        System.out.println("=== Azure DevOps Maven Build Demo ===");
        System.out.println("Addition: 10 + 5 = " + app.add(10, 5));
        System.out.println("Subtraction: 10 - 5 = " + app.subtract(10, 5));
        System.out.println("Multiplication: 10 * 5 = " + app.multiply(10, 5));
        System.out.println("Build completed successfully!");
    }
}
```

---

### Step 6: Edit the `AppTest.java` File

```cmd
notepad src\test\java\com\example\AppTest.java
```

**Delete ALL existing content**, then **copy-paste the entire block below** and save:

```java
package com.example;

import org.junit.Assert;
import org.junit.Test;

public class AppTest {

    @Test
    public void testAdd() {
        App app = new App();
        Assert.assertEquals(15, app.add(10, 5));
        System.out.println("Test Add: PASSED (10 + 5 = 15)");
    }

    @Test
    public void testSubtract() {
        App app = new App();
        Assert.assertEquals(5, app.subtract(10, 5));
        System.out.println("Test Subtract: PASSED (10 - 5 = 5)");
    }

    @Test
    public void testMultiply() {
        App app = new App();
        Assert.assertEquals(50, app.multiply(10, 5));
        System.out.println("Test Multiply: PASSED (10 * 5 = 50)");
    }

    @Test
    public void testAddNegative() {
        App app = new App();
        Assert.assertEquals(-5, app.add(-10, 5));
        System.out.println("Test Add Negative: PASSED (-10 + 5 = -5)");
    }

    @Test
    public void testSubtractNegative() {
        App app = new App();
        Assert.assertEquals(-15, app.subtract(-10, 5));
        System.out.println("Test Subtract Negative: PASSED (-10 - 5 = -15)");
    }
}
```

> This file contains **5 unit tests** so the pipeline output clearly shows multiple tests running.

---

### Step 7: Verify Locally (Optional but Recommended)

Before pushing to GitHub, verify everything works locally:

```cmd
mvn clean package
```

**Expected Output (key lines):**
```
Tests run: 5, Failures: 0, Errors: 0, Skipped: 0

[INFO] BUILD SUCCESS
```

---

# Part B — Push the Project to GitHub

---

### Step 8: Create a GitHub Repository

1. Open [https://github.com/new](https://github.com/new) in your browser
2. Enter:
   - **Repository name**: `azure-maven-app`
   - **Visibility**: Public (easier for Azure DevOps integration)
   - **Do NOT** initialize with README (we already have files)
3. Click **Create repository**

> ⚠️ Leave the repository **empty** — do not add README, .gitignore, or license.

---

### Step 9: Initialize Git and Push to GitHub

Go back to your **Command Prompt** (make sure you're inside `azure-maven-app` folder) and run these commands **one by one**:

```cmd
git init
```

```cmd
git checkout -b devops
```

```cmd
git add .
```

```cmd
git commit -m "Initial commit - Maven project with unit tests"
```

```cmd
git remote add origin https://github.com/YOUR_USERNAME/azure-maven-app.git
```

```cmd
git push -u origin devops
```

> ⚠️ **Replace `YOUR_USERNAME`** with your actual GitHub username.

> If prompted for credentials, enter your GitHub **username** and a **Personal Access Token** (not your password). Generate a token at [https://github.com/settings/tokens](https://github.com/settings/tokens) with `repo` scope.

**Expected Output:**
```
Branch 'devops' set up to track remote branch 'devops' from 'origin'.
```

---

# Part C — Create the Azure DevOps Build Pipeline

---

### Step 10: Open Azure DevOps

1. Open your browser and navigate to:
   ```
   https://dev.azure.com
   ```
2. Sign in with your Microsoft account
3. Select your **organization** and **project** (created in Program 9)

> If you need a new project, click **New Project** → Name: `AzureBuildPipeline` → Visibility: Private → Create

---

### Step 11: Open Azure Repos (Optional — View Only)

1. Go to **Repos** → **Files** from the left sidebar
2. This shows your Azure Repos (which may be empty if you're using GitHub)

> For this program, we'll use **GitHub** as the source. Azure Repos is just for reference.

---

### Step 12: Create the Build Pipeline

1. Go to **Pipelines** from the left sidebar
2. Click **Create Pipeline** (or **New Pipeline**)

---

### Step 13: Select Repository Source — GitHub

1. **Where is your code?** → Click **GitHub**
2. If this is your first time:
   - Azure DevOps will ask you to **authorize** with GitHub
   - Click **Authorize AzurePipelines**
   - Sign in to GitHub if prompted
   - Grant access to your repositories

---

### Step 14: Select Your Repository

1. You'll see a list of your GitHub repositories
2. Find and click **azure-maven-app**
3. If prompted, approve the Azure Pipelines app installation on the repository

---

### Step 15: Configure the Pipeline — Maven Template

1. **Configure your pipeline** — Azure DevOps may automatically detect it's a Maven project
2. Select **Maven** from the template list
   > If you don't see Maven, select **Starter pipeline** and we'll replace the YAML manually

---

### Step 16: Edit the Pipeline YAML

You'll see the YAML editor. **Replace ALL content** with the following:

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
    mavenVersionOption: 'Default'
    mavenAuthenticateFeed: false
```

**Key elements explained:**

| Element                        | Purpose                                                        |
| ------------------------------ | -------------------------------------------------------------- |
| `trigger: - devops`            | Pipeline triggers on push to `devops` branch                   |
| `vmImage: ubuntu-latest`      | Uses Microsoft-hosted Ubuntu VM as build agent                  |
| `task: Maven@3`               | Uses the built-in Maven task (version 3)                        |
| `mavenPomFile: 'pom.xml'`     | Points to the project's POM file                                |
| `goals: 'clean package'`      | Cleans old build, compiles, tests, and packages into JAR        |
| `publishJUnitResults: true`   | Publishes test results to Azure DevOps Test tab                 |
| `testResultsFiles: '...'`     | Path pattern to find JUnit XML test reports                     |
| `jdkVersionOption: '1.8'`     | Uses Java 8 on the build agent                                  |

---

### Step 17: Save and Run the Pipeline

1. Click **Save and run** (top-right)
2. In the dialog:
   - **Commit message**: `Add azure-pipelines.yml`
   - **Commit directly to the `devops` branch**: Selected
3. Click **Save and run**

> Azure DevOps commits the `azure-pipelines.yml` to your GitHub repo and immediately starts the pipeline.

---

### Step 18: Monitor the Build Execution

The pipeline run page shows the job executing. Click on the **Job** to see detailed live logs.

**You'll see these phases in the logs:**

| Phase                         | What Happens                                                  |
| ----------------------------- | ------------------------------------------------------------- |
| **Initialize job**            | Sets up the Ubuntu VM agent                                   |
| **Checkout**                  | Clones your GitHub repository                                 |
| **Maven Build and Test**      | Downloads dependencies, compiles code, runs all 5 unit tests  |
| **Publish Test Results**      | Uploads JUnit XML results to Azure DevOps                     |
| **Post-job cleanup**          | Cleans up the agent                                           |

**Expected key lines in the Maven Build log:**

```
[INFO] --- maven-clean-plugin:2.5:clean ---
[INFO] --- maven-compiler-plugin:3.8.1:compile ---
[INFO] Compiling 1 source file
[INFO] --- maven-surefire-plugin:2.22.2:test ---

 T E S T S
Running com.example.AppTest
Test Add: PASSED (10 + 5 = 15)
Test Subtract: PASSED (10 - 5 = 5)
Test Multiply: PASSED (10 * 5 = 50)
Test Add Negative: PASSED (-10 + 5 = -5)
Test Subtract Negative: PASSED (-10 - 5 = -15)

Tests run: 5, Failures: 0, Errors: 0, Skipped: 0

[INFO] --- maven-jar-plugin:2.4:jar ---
[INFO] Building jar: .../target/azure-maven-app-1.0-SNAPSHOT.jar
[INFO] BUILD SUCCESS
```

---

### Step 19: View Test Results

1. After the pipeline succeeds, go back to the **pipeline run summary**
2. Click the **Tests** tab
3. You should see:

```
Total Tests: 5
Passed: 5
Failed: 0
```

> Azure DevOps displays each test with its name, duration, and pass/fail status.

---

### Step 20: View Build Reports and Artifacts

1. On the pipeline run page, check:
   - ✅ **Pipeline Status**: `Succeeded` (green checkmark)
   - ✅ **Tests**: 5 passed, 0 failed
   - ✅ **Duration**: Time taken for the build

2. You can also go to **Pipelines** → click on your pipeline name → see the **run history** with all past builds.

---

## ✅ Summary of All Commands (Quick Reference)

### Part A — Create Maven Project Locally

```cmd
mkdir program10
cd program10
mvn archetype:generate -DgroupId=com.example -DartifactId=azure-maven-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd azure-maven-app
notepad pom.xml
notepad src\main\java\com\example\App.java
notepad src\test\java\com\example\AppTest.java
mvn clean package
```

### Part B — Push to GitHub

```cmd
git init
git checkout -b devops
git add .
git commit -m "Initial commit - Maven project with unit tests"
git remote add origin https://github.com/YOUR_USERNAME/azure-maven-app.git
git push -u origin devops
```

### Part C — Azure DevOps (Browser Steps)

| Step | Action                                                                  |
| ---- | ----------------------------------------------------------------------- |
| 1    | Open `https://dev.azure.com` → Sign in → Select project               |
| 2    | Pipelines → **Create Pipeline**                                        |
| 3    | Select **GitHub** → Authorize → Select `azure-maven-app` repo         |
| 4    | Configure: Select **Maven** (or Starter pipeline)                      |
| 5    | Replace YAML with the Maven pipeline configuration                     |
| 6    | Click **Save and run** → Commit to `devops` branch                    |
| 7    | Monitor logs → Verify: 5 tests passed, BUILD SUCCESS                  |
| 8    | Click **Tests** tab to see detailed test results                       |

---

## 📝 Theory Notes

### Azure Pipeline Key Concepts

| Concept        | Definition                                                              |
| -------------- | ----------------------------------------------------------------------- |
| **Pipeline**   | Automated workflow defined in YAML                                      |
| **Trigger**    | Event that starts the pipeline (push, PR, schedule)                     |
| **Pool**       | Set of agents (VMs) that run the pipeline                               |
| **Agent**      | Machine that executes pipeline tasks (Microsoft-hosted or self-hosted) |
| **Task**       | Pre-built action (Maven build, Docker push, file copy, etc.)           |
| **Step**       | Individual unit of work (a task or a script)                            |
| **Job**        | Collection of steps that run on the same agent                          |
| **Stage**      | Logical grouping of jobs (e.g., Build, Test, Deploy)                    |

### Maven Goals in the Pipeline

| Goal             | What it Does                                           |
| ---------------- | ------------------------------------------------------ |
| `clean`          | Deletes the `target/` directory (old build artifacts)  |
| `compile`        | Compiles source code in `src/main/java`                |
| `test`           | Runs unit tests in `src/test/java`                     |
| `package`        | Creates JAR/WAR in `target/` directory                 |
| `clean package`  | Combines clean + compile + test + package (used here)  |

### Pipeline YAML Structure

```yaml
trigger:        # WHEN to run
  - branch_name

pool:           # WHERE to run
  vmImage: ...

steps:          # WHAT to run
  - task: ...   # Pre-built task (Maven, Gradle, Docker)
  - script: ... # Custom shell commands
```

### GitHub vs Azure Repos as Source

| Feature              | GitHub                        | Azure Repos                    |
| -------------------- | ----------------------------- | ------------------------------ |
| Setup                | Requires authorization        | Integrated (no auth needed)    |
| Popularity           | Most widely used              | Azure ecosystem                |
| Free Private Repos   | Yes                           | Yes                            |
| Integration          | Via OAuth / GitHub App        | Native                         |

> Both work equally well with Azure Pipelines. GitHub is more common in the industry.

---

## ⚠️ Troubleshooting

| Problem                                          | Solution                                                                          |
| ------------------------------------------------ | --------------------------------------------------------------------------------- |
| `git push` fails — authentication error          | Use a Personal Access Token instead of password. Generate at GitHub → Settings → Developer settings → Tokens |
| GitHub not visible in pipeline source list        | Click "GitHub" and authorize the Azure Pipelines app                              |
| Repository not listed after authorization         | Click "All repositories" instead of "Selected repositories" during GitHub auth    |
| Pipeline stays "Queued" — no agent available      | Request free parallelism at `https://aka.ms/azpipelines-parallelism-request`      |
| Maven build fails — "No compiler provided"        | Change `jdkVersionOption` in YAML to `'1.11'` or `'1.17'`                       |
| Tests run: 0                                      | Verify `AppTest.java` has `@Test` annotations and is in `src/test/java/com/example/` |
| "pom.xml not found"                              | Ensure `mavenPomFile: 'pom.xml'` in YAML matches the actual file location        |
| Test results not showing in "Tests" tab           | Ensure `publishJUnitResults: true` is set in the YAML                            |
| Pipeline triggers on wrong branch                 | Update `trigger: - devops` to match your actual branch name                      |
| `mvn clean package` fails locally                 | Check `pom.xml` for typos; ensure proper XML encoding (straight quotes only)     |
| Cannot authorize GitHub — popup blocked           | Allow popups for `dev.azure.com` in browser settings                              |

---

**Program 10 Complete ✔**
