# BCSL657D — Programs 5 & 6

## Program 5: Introduction to Jenkins — Installing and Configuring Jenkins
## Program 6: Implementing CI Pipelines Using Jenkins — Freestyle Project with Git and Maven

---

## 📋 Prerequisites

Before starting, ensure the following are installed and configured:

| Requirement        | Verify Command          | Expected Output                |
| ------------------ | ----------------------- | ------------------------------ |
| Java JDK 21        | `java -version`         | java version "21.x.x"         |
| Maven              | `mvn -v`                | Apache Maven 3.x.x            |
| Git                | `git --version`         | git version 2.x.x             |
| Web Browser        | —                       | Chrome / Edge / Firefox        |
| Internet Access    | —                       | Required for downloads         |

> ⚠️ **Jenkins requires Java JDK 21.** If you have a different version, download JDK 21 from [Oracle](https://www.oracle.com/java/technologies/downloads/#java21) or [Adoptium](https://adoptium.net/).

---

## 🗂️ Overview

| Program | What You'll Do                                                     |
| ------- | ------------------------------------------------------------------ |
| **5**   | Install Jenkins on Windows, unlock it, install plugins, create admin |
| **6**   | Create a CI pipeline (Freestyle Project) with Git + Maven integration |

---

# PART 1 — Program 5: Installing and Configuring Jenkins

---

## 📖 Theory: What is Jenkins?

Jenkins is an **open-source automation server** used for **Continuous Integration (CI)** and **Continuous Delivery (CD)**. It automates building, testing, and deploying software.

**Key Features:**

| Feature             | Description                                                    |
| ------------------- | -------------------------------------------------------------- |
| CI/CD               | Automates code integration, testing, and deployment            |
| Plugins             | 1800+ plugins for Git, Maven, Docker, Slack, and more          |
| Pipeline as Code    | Define build pipelines in Groovy or YAML                       |
| Cross-platform      | Runs on Windows, Linux, macOS                                  |
| Distributed Builds  | Supports master-agent architecture for parallel builds         |

---

### Step 1: Download Jenkins

Open your browser and go to:

```
https://www.jenkins.io/download/
```

- Under **LTS (Long Term Support)**, click **Windows** to download the `.msi` installer.

---

### Step 2: Run the Jenkins Installer

1. Double-click the downloaded `.msi` file to start installation.
2. Click **Next** on the welcome screen.
3. **Logon Type** — Select:
   ```
   ✔ Run service as LocalSystem (not recommended)
   ```
   > This is simpler for lab purposes. In production, use a dedicated service account.
4. Click **Next**.

---

### Step 3: Configure the Port

1. Jenkins will ask for a **port number**. You can either:
   - Use the **default port**: `8080`
   - Or enter a **custom port** (e.g., `3030`)
2. Click **Test** to verify the port is available.
3. Click **Next**.

> ⚠️ Remember the port you choose — you'll use it to access Jenkins in the browser.

---

### Step 4: Select Java JDK Path

1. Jenkins will ask for the **Java installation path**.
2. Click **Browse** and navigate to your JDK 21 directory:

   ```
   C:\Program Files\Java\jdk-21\
   ```

   > If your JDK is installed elsewhere, navigate to that folder instead. The folder should contain `bin`, `lib`, etc.

3. Click **Next**.

---

### Step 5: Complete the Installation

1. Click **Next** through the remaining screens.
2. When prompted for permissions, click **Yes**.
3. Wait for the installation to finish.
4. Click **Finish**.

> Jenkins is now installed and running as a Windows service.

---

### Step 6: Open Jenkins in Browser

Open your browser and navigate to:

```
http://localhost:8080
```

> If you used a custom port (e.g., 3030), use `http://localhost:3030` instead.

You should see the **"Unlock Jenkins"** page asking for an **Administrator password**.

---

### Step 7: Unlock Jenkins — Get the Admin Password

The page shows a file path like:

```
C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

**Option A — Using Command Prompt:**

```cmd
type C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

**Option B — Using Notepad:**

```cmd
notepad C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
```

> ⚠️ **If the path shown on the Jenkins page is different**, use the path displayed on YOUR screen instead.

**Copy the password** from the file, **paste it** into the Jenkins browser page, and click **Continue**.

---

### Step 8: Install Plugins

On the **"Customize Jenkins"** page:

- Click **Install suggested plugins**

> Jenkins will automatically download and install all essential plugins (Git, Maven, Pipeline, etc.). This may take a few minutes depending on your internet speed.

---

### Step 9: Create Admin User

Fill in the form:

| Field              | Example Value         |
| ------------------ | --------------------- |
| Username           | `admin`               |
| Password           | `admin`               |
| Confirm Password   | `admin`               |
| Full Name          | `Admin User`          |
| Email Address      | `admin@example.com`   |

Click **Save and Continue** → **Save and Finish** → **Start using Jenkins**

---

### Step 10: Verify Jenkins Dashboard

You should now see the **Jenkins Dashboard** with options like:

- ✅ New Item
- ✅ Manage Jenkins
- ✅ Build Queue
- ✅ Build Executor Status

> 🎉 **Jenkins is successfully installed and configured!**

---

### Step 11: Configure Maven in Jenkins

Before creating a CI pipeline, Jenkins needs to know where Maven is installed.

1. Go to **Manage Jenkins** → **Tools** (or **Global Tool Configuration** in older versions)
2. Scroll down to **Maven** section
3. Click **Add Maven**
4. Enter:
   - **Name**: `Maven` (or any name you prefer)
   - **Uncheck** "Install automatically"
   - **MAVEN_HOME**: Enter your Maven installation path, e.g.:
     ```
     C:\Program Files\apache-maven-3.9.6
     ```
5. Click **Save**

> ⚠️ **To find your Maven path**, run `mvn -v` in Command Prompt — it shows the Maven home directory.

---

### Step 12: Configure Git in Jenkins

1. Go to **Manage Jenkins** → **Tools**
2. Scroll down to **Git** section
3. Verify Git is configured. If not:
   - **Name**: `Default`
   - **Path to Git executable**: `git.exe` (if Git is in PATH) or the full path like:
     ```
     C:\Program Files\Git\bin\git.exe
     ```
4. Click **Save**

---

# PART 2 — Program 6: Creating a CI Pipeline (Freestyle Project)

---

## 📖 Theory: What is Continuous Integration (CI)?

Continuous Integration is a practice where developers **frequently integrate code**, and an automation server (Jenkins) automatically:

- **Builds** the project
- **Runs tests**
- **Reports results**

**CI Pipeline Flow:**
```
Code Push → Jenkins Pulls Code → Build → Test → Result
```

---

## 🔧 Preparation: Push a Maven Project to GitHub

> Before creating a Jenkins CI pipeline, you need a **Maven project hosted on GitHub** (or any Git repository). If you already have one, skip to Step 13.

### Prep Step A: Create a Maven Project (if needed)

Open Command Prompt:

```cmd
mkdir jenkins-demo
cd jenkins-demo
mvn archetype:generate -DgroupId=com.example -DartifactId=jenkins-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd jenkins-app
```

### Prep Step B: Edit `App.java`

```cmd
notepad src\main\java\com\example\App.java
```

**Delete ALL content**, paste this, and save:

```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello from Jenkins CI Pipeline!");
        System.out.println("Build is successful.");

        int a = 10;
        int b = 20;
        System.out.println("Sum of " + a + " and " + b + " is " + (a + b));
    }

    public static int add(int x, int y) {
        return x + y;
    }
}
```

### Prep Step C: Push to GitHub

> Create a **new repository** on [GitHub](https://github.com/new) (e.g., `jenkins-app`). Then run:

```cmd
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/jenkins-app.git
git push -u origin main
```

> ⚠️ Replace `YOUR_USERNAME` with your actual GitHub username.

---

## 🚀 Creating the Jenkins CI Pipeline

### Step 13: Create a New Jenkins Job

1. Open Jenkins in browser: `http://localhost:8080`
2. Click **New Item** (on the left sidebar)
3. Enter an item name:
   ```
   maven-ci-project
   ```
4. Select **Freestyle project**
5. Click **OK**

---

### Step 14: Configure Source Code Management (Git)

In the job configuration page:

1. Scroll down to **Source Code Management**
2. Select **Git**
3. Enter your **Repository URL**:
   ```
   https://github.com/YOUR_USERNAME/jenkins-app.git
   ```
4. **Branch Specifier**: Change to match your branch:
   ```
   */main
   ```
   > Use `*/master` if your default branch is named `master`.

5. **Credentials**: If your repository is **private**, click **Add** → **Jenkins** and enter your GitHub username and [Personal Access Token](https://github.com/settings/tokens).

   > For **public** repositories, no credentials are needed.

---

### Step 15: Add Maven Build Step

1. Scroll down to **Build** section
2. Click **Add build step** → **Invoke top-level Maven targets**
3. Enter in the **Goals** field:
   ```
   clean install
   ```
4. Click **Advanced...**
5. In the **POM** field, enter:
   ```
   pom.xml
   ```

> **What `clean install` does:**
> - `clean` — Deletes old build files from the `target/` directory
> - `install` — Compiles the code, runs tests, packages the JAR, and installs it locally

---

### Step 16: Save the Project

Scroll to the bottom and click **Save**.

---

### Step 17: Run the CI Pipeline (Build Now)

1. On the project page, click **Build Now** (left sidebar)
2. A new build appears under **Build History** (e.g., `#1`)

> Wait for the build to complete — a **blue circle** ✅ means success, **red** ❌ means failure.

---

### Step 18: View Console Output

1. Click on **Build #1** (or the latest build number) under Build History
2. Click **Console Output**

**Expected Output (key lines):**

```
Started by user admin
Cloning the repository
 > git init ...
 > git fetch ...
Building project using Maven
[INFO] --- maven-clean-plugin ---
[INFO] --- maven-compiler-plugin --- Compiling source files
[INFO] --- maven-surefire-plugin --- Running tests
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0
[INFO] --- maven-jar-plugin --- Building jar
[INFO] BUILD SUCCESS
Finished: SUCCESS
```

> 🎉 **Your CI pipeline is working!** Every time code is pushed to the repository, you can click "Build Now" to trigger the pipeline.

---

## ✅ Summary of All Commands (Quick Reference)

### Program 5 — Jenkins Installation

| Step | Action                                                                 |
| ---- | ---------------------------------------------------------------------- |
| 1    | Download Jenkins `.msi` from `https://www.jenkins.io/download/`       |
| 2    | Run installer → Select "Run service as LocalSystem"                    |
| 3    | Set port (default `8080` or custom like `3030`) → Click Test → Next   |
| 4    | Set Java path: `C:\Program Files\Java\jdk-21\`                        |
| 5    | Finish installation                                                    |
| 6    | Open `http://localhost:8080` in browser                                |
| 7    | Get password: `type C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword` |
| 8    | Click "Install suggested plugins"                                      |
| 9    | Create admin user → Save and Continue → Start using Jenkins           |
| 10   | Configure Maven and Git in Manage Jenkins → Tools                     |

### Program 6 — CI Pipeline

```cmd
mkdir jenkins-demo
cd jenkins-demo
mvn archetype:generate -DgroupId=com.example -DartifactId=jenkins-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd jenkins-app
notepad src\main\java\com\example\App.java
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/jenkins-app.git
git push -u origin main
```

Then in Jenkins UI:
1. **New Item** → Name: `maven-ci-project` → **Freestyle project** → OK
2. **Source Code Management** → Git → Enter repo URL → Branch: `*/main`
3. **Build** → Add build step → Invoke top-level Maven targets → Goals: `clean install` → Advanced → POM: `pom.xml`
4. **Save** → **Build Now** → **Console Output**

---

## 📝 Theory Notes

### Jenkins Architecture

```
┌─────────────────────────────────────────────────────┐
│                  Jenkins Server                     │
│                                                     │
│  ┌──────────┐   ┌──────────┐   ┌────────────────┐  │
│  │   Jobs   │──▶│  Build   │──▶│  Test & Report  │  │
│  │ (Config) │   │ (Maven)  │   │  (JUnit/Surefire)│ │
│  └──────────┘   └──────────┘   └────────────────┘  │
│       ▲                                             │
│       │                                             │
│  ┌──────────┐                                       │
│  │   Git    │  ← Pulls code from repository         │
│  │  (SCM)   │                                       │
│  └──────────┘                                       │
└─────────────────────────────────────────────────────┘
```

### CI Pipeline Flow

```
Developer pushes code
        ↓
Jenkins detects change (or manual "Build Now")
        ↓
Jenkins pulls latest code from Git
        ↓
Maven compiles the source code
        ↓
Maven runs unit tests (Surefire plugin)
        ↓
Maven packages the application (JAR/WAR)
        ↓
Jenkins reports: SUCCESS or FAILURE
```

### Jenkins Job Types

| Type                | Use Case                                            |
| ------------------- | --------------------------------------------------- |
| **Freestyle**       | Simple builds with GUI configuration (used here)    |
| **Pipeline**        | Complex builds defined in `Jenkinsfile` (code-based)|
| **Multibranch**     | Automatic pipelines for each Git branch             |
| **Folder**          | Organize multiple jobs into groups                  |

### Jenkins Plugins Used

| Plugin                    | Purpose                                    |
| ------------------------- | ------------------------------------------ |
| **Git Plugin**            | Integrates Git repositories with Jenkins   |
| **Maven Integration**     | Adds Maven build step support              |
| **JUnit Plugin**          | Publishes test results in Jenkins UI       |
| **Pipeline Plugin**       | Enables Pipeline-as-Code (Jenkinsfile)     |

---

## ⚠️ Troubleshooting

| Problem                                          | Solution                                                                          |
| ------------------------------------------------ | --------------------------------------------------------------------------------- |
| Jenkins page doesn't load in browser             | Check if Jenkins service is running: `services.msc` → find "Jenkins" → Start     |
| Port 8080 already in use                         | Change port during installation, or edit `jenkins.xml` in Jenkins install folder   |
| `initialAdminPassword` file not found            | Try path: `C:\Windows\System32\config\systemprofile\.jenkins\secrets\initialAdminPassword` |
| Git not found in Jenkins                         | Configure Git path in Manage Jenkins → Tools → Git                                |
| Maven not found in Jenkins build step            | Configure Maven in Manage Jenkins → Tools → Maven                                 |
| BUILD FAILURE — "No compiler is provided"        | Install JDK (not just JRE) and configure JAVA_HOME                                |
| Git clone fails — "authentication required"      | For private repos, add GitHub credentials (username + Personal Access Token)       |
| Build runs but tests fail                        | Check Console Output for exact error; verify `App.java` code matches              |
| "Invoke top-level Maven targets" not visible     | Install "Maven Integration" plugin from Manage Jenkins → Plugins                  |
| Jenkins service won't start after install        | Verify Java 21 is installed and the path in Step 4 is correct                     |

---

**Programs 5 & 6 Complete ✔**
