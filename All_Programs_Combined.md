# BCSL657D — DevOps Lab Master Guide

This guide compiles all lab programs with only the essential commands, configuration files, and code. No long theory or explanations—just simple, copy-paste-ready guides.

---

## 📌 Table of Contents

- [Program 2: Working with Maven](#program-2-working-with-maven)
- [Program 3: Working with Gradle (Kotlin DSL)](#program-3-working-with-gradle-kotlin-dsl)
- [Program 4: Migrate Maven to Gradle](#program-4-migrate-maven-to-gradle)
- [Programs 5 & 6: Jenkins CI Pipeline](#programs-5--6-jenkins-ci-pipeline)
- [Program 9: Azure DevOps Intro & Basic Pipeline](#program-9-azure-devops-intro--basic-pipeline)
- [Program 10: Azure Build Pipeline (Maven + Tests)](#program-10-azure-build-pipeline-maven--tests)
- [Program 11: Release Pipeline & Key Vault](#program-11-release-pipeline--key-vault)

---

## Program 2: Working with Maven

Create a Maven project, write an application and JUnit tests, compile, package, and run.

### Step 1: Create a Working Folder
```cmd
mkdir program2
cd program2
```

### Step 2: Generate the Maven Project
```cmd
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

### Step 3: Navigate into the Project Folder
```cmd
cd myapp
```

### Step 4: Edit `pom.xml`
Replace all content inside `pom.xml` with:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myapp</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <!-- JUnit Dependency for Testing -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <!-- Maven Surefire Plugin for running tests -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
                <configuration>
                    <redirectTestOutputToFile>false</redirectTestOutputToFile>
                    <useSystemOut>true</useSystemOut>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### Step 5: Edit `src/main/java/com/example/App.java`
Replace all content with:
```java
package com.example;

public class App {

    public int add(int a, int b) {
        return a + b;
    }

    public static void main(String[] args) {
        App app = new App();

        int result = app.add(2, 3);
        System.out.println("2 + 3 = " + result);

        System.out.println("Application executed successfully!");
    }
}
```

### Step 6: Edit `src/test/java/com/example/AppTest.java`
Replace all content with:
```java
package com.example;

import org.junit.Assert;
import org.junit.Test;

public class AppTest {

    @Test
    public void testAdd() {
        App app = new App();
        int result = app.add(2, 3);

        System.out.println("Running test: 2 + 3 = " + result);

        Assert.assertEquals(5, result);
    }
}
```

### Step 7: Build & Run the Application
Run these commands sequentially:
```cmd
mvn compile
mvn test
mvn package
java -cp target/myapp-1.0-SNAPSHOT.jar com.example.App
```

[Back to top](#-table-of-contents)

---

## Program 3: Working with Gradle (Kotlin DSL)

Initialize a Gradle project with Kotlin DSL, restructuring directories, writing Kotlin code, and building.

### Step 1: Create a Working Folder
```cmd
mkdir program3
cd program3
```

### Step 2: Initialize the Gradle Project
```cmd
gradle init --type java-application
```
**Interactive prompts:**
- Enter target Java version: `17`
- Project name: `kotlinProject`
- Select application structure: `1` (Single application project)
- Select build script DSL: `1` (Kotlin)
- Select test framework: `1` (JUnit 4)
- Generate build using new APIs: `no`

### Step 3: Pin Gradle version (Fixes Gradle 9+ incompatibilities)
Run this inside `program3`:
```cmd
gradle wrapper --gradle-version 8.5
cd app
```

### Step 4: Restructure Directories (Convert package to com.example)
```cmd
rmdir /S /Q src\main\java\org
rmdir /S /Q src\test\java\org
mkdir src\main\java\com\example
mkdir src\test\java\com\example
```

### Step 5: Edit `build.gradle.kts`
Replace all content with:
```kotlin
plugins {
    kotlin("jvm") version "1.9.22"
    application
}

repositories {
    mavenCentral()
}

dependencies {
    implementation(kotlin("stdlib"))
    testImplementation("junit:junit:4.13.2")
}

application {
    mainClass.set("com.example.MainKt")
}

tasks.test {
    useJUnit()

    testLogging {
        events("passed", "failed", "skipped")
        exceptionFormat = org.gradle.api.tasks.testing.logging.TestExceptionFormat.FULL
        showStandardStreams = true
    }

    outputs.upToDateWhen { false }
}

java {
    toolchain {
        languageVersion.set(JavaLanguageVersion.of(17))
    }
}
```

### Step 6: Create `src/main/java/com/example/Main.kt`
Create and open this file, then add:
```kotlin
package com.example

fun addNumbers(num1: Double, num2: Double): Double {
    return num1 + num2
}

fun main() {
    val num1 = 10.0
    val num2 = 5.0
    val result = addNumbers(num1, num2)
    println("The sum of $num1 and $num2 is: $result")
}
```

### Step 7: Create `src/test/java/com/example/MainTest.kt`
Create and open this file, then add:
```kotlin
package com.example

import org.junit.Assert.*
import org.junit.Test

class MainTest {

    @Test
    fun testAddNumbers() {
        val num1 = 10.0
        val num2 = 5.0

        val result = addNumbers(num1, num2)

        assertEquals("The sum of $num1 and $num2 should be 15.0", 15.0, result, 0.001)
    }
}
```

### Step 8: Build & Run using the Wrapper
Run inside the `app` folder:
```cmd
..\gradlew build --no-configuration-cache
..\gradlew run --no-configuration-cache
..\gradlew test --no-configuration-cache
```

[Back to top](#-table-of-contents)

---

## Program 4: Migrate Maven to Gradle

Create a Java application using Maven, then automatically migrate it to Gradle using `gradle init`.

### Step 1: Create working folder & Generate Maven Project
```cmd
mkdir program4
cd program4
mvn archetype:generate -DgroupId=com.example -DartifactId=maven-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd maven-example
```

### Step 2: Edit `pom.xml`
Replace all content with:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>maven-example</artifactId>
    <version>1.0-SNAPSHOT</version>

    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### Step 3: Edit `src/main/java/com/example/App.java`
Replace all content with:
```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello, Maven");
        System.out.println("This is the simple realworld example....");

        int a = 5;
        int b = 10;
        System.out.println("Sum of " + a + " and " + b + " is " + sum(a, b));
    }

    public static int sum(int x, int y) {
        return x + y;
    }
}
```

### Step 4: Run Maven App
```cmd
mvn clean install
mvn exec:java -Dexec.mainClass="com.example.App"
```

### Step 5: Migrate to Gradle
Ensure you are in `maven-example` folder, then run:
```cmd
gradle init
```
**Interactive prompts:**
- Found a Maven build. Generate a Gradle build? `yes`
- Select build script DSL: `2` (Groovy DSL)
- Generate build using new APIs: `no`

### Step 6: Edit the Generated `build.gradle` (Fixes Gradle 9+ incompatibilities)
Replace the entire `build.gradle` file with:
```groovy
plugins {
    id 'java'
    id 'application'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'junit:junit:4.12'
}

application {
    mainClass = 'com.example.App'
}
```

### Step 7: Run the migrated Gradle App
```cmd
.\gradlew build
.\gradlew run
```

[Back to top](#-table-of-contents)

---

## Programs 5 & 6: Jenkins CI Pipeline

Configure Jenkins, set up a Git repository with a Maven app, push it to GitHub, and run a Freestyle build.

### Part A: Install Jenkins
1. Download installer `.msi` from `https://www.jenkins.io/download/`
2. Run installer → Select **Run service as LocalSystem**
3. Set Port (e.g., `8080` or `3030`) → click Test → Next.
4. Set Java JDK Path (e.g., `C:\Program Files\Java\jdk-21\`).
5. Complete installer. Open `http://localhost:8080` in browser.
6. Retrieve initial password:
   ```cmd
   type C:\ProgramData\Jenkins\.jenkins\secrets\initialAdminPassword
   ```
7. Click **Install suggested plugins**, create admin account, and finish setup.

### Part B: Configure Tools in Jenkins
1. Go to **Manage Jenkins** → **Tools**.
2. **JDK installations** → Name: `JAVA_HOME`, Path: (your JDK path like `C:\Program Files\Java\jdk-21`).
3. **Maven installations** → Name: `MAVEN_HOME`, Path: (your Maven path like `C:\maven\`).

### Part C: Prepare the App and push to GitHub
Run sequentially:
```cmd
mkdir jenkins-demo
cd jenkins-demo
mvn archetype:generate -DgroupId=com.example -DartifactId=jenkins-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd jenkins-app
```
Replace `src\main\java\com\example\App.java` with:
```java
package com.example;

public class App {
    public static void main(String[] args) {
        System.out.println("Hello, Jenkins CI!");
    }
}
```
Push to GitHub:
```cmd
git init
git add .
git commit -m "Initial Maven App Commit"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/jenkins-app.git
git push -u origin main
```

### Part D: Create Freestyle Project in Jenkins
1. Go to Jenkins Dashboard → **New Item**.
2. Name: `jenkins-app-ci` → **Freestyle project** → Click **OK**.
3. Under **Source Code Management**:
   - Select **Git**.
   - Repository URL: `https://github.com/YOUR_GITHUB_USERNAME/jenkins-app.git`
   - Branch Specifier: `*/main`
4. Under **Build Steps**:
   - Add **Invoke top-level Maven targets**.
   - Maven Version: Select your configured `MAVEN_HOME`.
   - Goals: `clean package`
5. Click **Save**.
6. On Job page → Click **Build Now**.
7. Click the green check/build number under History → **Console Output** to verify.

[Back to top](#-table-of-contents)

---

## Program 9: Azure DevOps Intro & Basic Pipeline

Create an Azure DevOps account, set up a project, initialize an Azure Repo, and build a starter pipeline.

### Step 1: Account & Project Setup
1. Open `https://dev.azure.com` and sign in with your Microsoft account.
2. Create an Organization (e.g., `yourname-devops`).
3. Create a Project:
   - Name: `DevOpsLabProject`
   - Visibility: `Private`
   - Click **Create**.

### Step 2: Initialize Git Repository
1. Navigate to **Repos** → **Files** (left sidebar).
2. Look at the bottom for *Initialize main branch with a README or .gitignore*.
3. Check `✔ Add a README` and click **Initialize**.

### Step 3: Create and Run Starter Pipeline
1. Go to **Pipelines** (left sidebar) → Click **Create Pipeline**.
2. Select **Azure Repos Git**.
3. Select your repository **DevOpsLabProject**.
4. Select **Starter pipeline**.
5. Keep default starter YAML:
   ```yaml
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
6. Click **Save and run** → click **Save and run** again on the pop-up.
7. Click the **Job** block in the pipeline run view to monitor live logs and verify execution.

[Back to top](#-table-of-contents)

---

## Program 10: Azure Build Pipeline (Maven + Tests)

Write a local Maven project with multiple unit tests, push to GitHub, and integrate with Azure Pipelines to run build and tests automatically.

### Part A: Prepare Maven Project Locally
Run these commands:
```cmd
mkdir program10
cd program10
mvn archetype:generate -DgroupId=com.example -DartifactId=azure-maven-app -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd azure-maven-app
```

#### Edit `pom.xml`
Replace all content with:
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
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.13.2</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.22.2</version>
            </plugin>
        </plugins>
    </build>
</project>
```

#### Edit `src/main/java/com/example/App.java`
Replace all content with:
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

#### Edit `src/test/java/com/example/AppTest.java`
Replace all content with:
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

#### Create `azure-pipelines.yml`
Create and open this file in the root directory `azure-maven-app\`, then add:
```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: Maven@3
  displayName: 'Build and Test with Maven'
  inputs:
    mavenPomFile: 'pom.xml'
    goals: 'clean package'
    publishJUnitResults: true
    testResultsFiles: '**/surefire-reports/TEST-*.xml'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '1.11'
    mavenVersionOption: 'Default'
    mavenAuthenticateFeed: false
```

### Part B: Push to GitHub
Run inside `azure-maven-app\` folder:
```cmd
git init
git add .
git commit -m "Azure DevOps Maven App"
git branch -M main
git remote add origin https://github.com/YOUR_GITHUB_USERNAME/azure-maven-app.git
git push -u origin main
```

### Part C: Configure Build Pipeline in Azure DevOps
1. Go to Azure DevOps Dashboard → **Pipelines** → Click **New Pipeline**.
2. Select **GitHub**. Authorize and sign in to your GitHub account if prompted.
3. Select your repository **azure-maven-app**.
4. Select **Existing Azure Pipelines YAML file** → Path: `/azure-pipelines.yml` → Continue.
5. Click **Run**.
6. Monitor the running job. Once finished, click the **Tests** tab next to "Summary" to view detailed JUnit results.

[Back to top](#-table-of-contents)

---

## Program 11: Release Pipeline & Key Vault

Set up an Azure App Service, configure the build pipeline to publish artifacts, deploy them via a release pipeline, and link Key Vault secrets.

### Part A: Create Azure App Service (Azure Portal)
1. Sign in to `https://portal.azure.com`.
2. Click **Create a resource** → **Web App** → Create.
3. Configuration:
   - Resource Group: `devops-lab-rg`
   - Name: `your-app-name` (must be globally unique)
   - Publish: `Code`
   - Runtime Stack: `Java 8` or `Java 11` (with Web Server Stack `Java SE`)
   - Operating System: `Linux`
   - App Service Plan: Free F1 tier (or basic shared plan)
4. Click **Review + create** → **Create**.

### Part B: Modify Build Pipeline to Publish Artifacts
Open and update the `azure-pipelines.yml` in your code repository with these copying steps to publish the packaged `.jar`:
```yaml
trigger:
- main

pool:
  vmImage: ubuntu-latest

steps:
- task: Maven@3
  displayName: 'Build and Test with Maven'
  inputs:
    mavenPomFile: 'pom.xml'
    goals: 'clean package'
    publishJUnitResults: true
    testResultsFiles: '**/surefire-reports/TEST-*.xml'
    javaHomeOption: 'JDKVersion'
    jdkVersionOption: '1.11'

- task: CopyFiles@2
  displayName: 'Copy JAR file to staging directory'
  inputs:
    sourceFolder: '$(system.defaultworkingdirectory)/target'
    contents: '*.jar'
    targetFolder: '$(build.artifactstagingdirectory)'

- task: PublishBuildArtifacts@1
  displayName: 'Publish staging directory artifact'
  inputs:
    pathToPublish: '$(build.artifactstagingdirectory)'
    artifactName: 'drop'
    publishLocation: 'Container'
```
Commit and push this change. It will trigger a build and publish an artifact named `drop`.

### Part C: Create the Release Pipeline (Azure DevOps)
1. In Azure DevOps, go to **Pipelines** → **Releases** → Click **New pipeline**.
2. Select Template: **Azure App Service deployment** → Click **Apply**.
3. Under **Artifacts**:
   - Click **Add an artifact**.
   - Source Type: **Build**.
   - Source (build pipeline): Select your Program 10/11 build pipeline.
   - Click **Add**.
4. Click the Lightning bolt ⚡ icon on the newly added artifact block → check **Enabled** under Continuous deployment trigger.
5. In **Stage 1** block:
   - Click the link **1 job, 1 task** to configure the deployment task.
   - Azure Subscription: Select your Azure subscription (click "Authorize" if prompted).
   - App Service Name: Select your unique Web App name created in Part A.
6. Click **Save** → **Create release** → **Create** (manually triggers the first deployment).

### Part D: Integrate Azure Key Vault (Manage Secrets)
1. Go to **Azure Portal** → Search for **Key Vaults** → Click **Create**.
2. Resource Group: `devops-lab-rg` / Vault Name: `unique-vault-name`. Click **Review + create** → **Create**.
3. Go to your Key Vault → **Secrets** (left sidebar) → Click **Generate/Import**.
   - Name: `DB-PASSWORD`
   - Value: `SuperSecretPassword123`
   - Click **Create**.
4. Go to **Azure DevOps** → **Pipelines** → **Library** (left sidebar).
5. Click **+ Variable group**:
   - Variable Group Name: `KeyVaultSecretsGroup`
   - Toggle **Link secrets from an Azure key vault as variables** ON.
   - Select your Azure Subscription and Key Vault name.
   - Click **+ Add** → Check your `DB-PASSWORD` secret → click **OK**.
   - Click **Save**.
6. Go back to your **Release Pipeline** → Click **Edit** → **Variables** tab → **Variable groups**.
7. Click **Link variable group** → Choose `KeyVaultSecretsGroup` → click **Link** and **Save**.
8. In your deployment task, you can now use this secret in environment configurations by referencing: `$(DB-PASSWORD)`.

[Back to top](#-table-of-contents)
