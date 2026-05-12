# Comprehensive Lab Programs Guide

---

## Program 2: Maven Project - Complete Step by Step Guide

### 🔧 Prerequisites (Do This First)
Before anything, make sure you have these installed:

**Check Java:**
```bash
java -version
```

**Check Maven:**
```bash
mvn -version
```
If either is missing, install them first. 

### 📁 Step 1: Create a Working Folder
Open Command Prompt (search "cmd" in Windows start menu)
```bash
mkdir program2
cd program2
```

### 🏗️ Step 2: Generate the Maven Project
Paste this full command and press Enter:
```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
⏳ Wait for it to finish. You'll see `BUILD SUCCESS`. Then navigate into the project folder:
```bash
cd myapp
```

### 📝 Step 3: Edit `pom.xml`
Open the file:
```bash
notepad pom.xml
```
Select all existing content → Delete it → Paste the code below → Save & Close:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>myapp</artifactId>
    <version>1.0-SNAPSHOT</version>

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

### ☕ Step 4: Edit `App.java`
Open the file:
```bash
notepad src\main\java\com\example\App.java
```
Select all → Delete → Paste the code below → Save & Close:
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

### 🧪 Step 5: Edit `AppTest.java`
Open the file:
```bash
notepad src\test\java\com\example\AppTest.java
```
Select all → Delete → Paste the code below → Save & Close:
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

### ▶️ Step 6: Build & Run (Run These One by One)

**1. Compile**
```bash
mvn compile
```
✅ **Expected:** `BUILD SUCCESS`

**2. Run Tests**
```bash
mvn test
```
✅ **Expected:** `Tests run: 1, Failures: 0` and `BUILD SUCCESS`

**3. Package into JAR**
```bash
mvn package
```
✅ **Expected:** `BUILD SUCCESS`

**4. Run the Application**
```bash
java -cp target/myapp-1.0-SNAPSHOT.jar com.example.App
```
✅ **Final Expected Output:**
```text
2 + 3 = 5
Application executed successfully!
```

### 🆘 Common Issues

| Problem | Fix |
|---|---|
| `mvn` not recognized | Maven not installed or not in PATH |
| `java` not recognized | JDK not installed or not in PATH |
| `BUILD FAILURE` on compile | Check that you saved the files correctly |
| Notepad opens blank file | You may be in the wrong folder — double check with `cd myapp` |

---

## Program 3: Working with Gradle Project (Kotlin DSL)

### ✅ Prerequisites — Check These First
Open Command Prompt and verify:
```bash
java -version
gradle -version
```
Both must work. If not, install JDK 17 and Gradle first.

### 📁 Step 1: Create a Working Folder
```bash
mkdir program3
cd program3
```

### 🏗️ Step 2: Initialize the Gradle Project
Run this command:
```bash
gradle init --type java-application
```
When it asks questions, answer exactly like this:

| Question | Answer |
|---|---|
| Enter target Java version | `17` |
| Project name | `kotlinProject` |
| Select application structure | `1` |
| Select build script DSL | `1` (Kotlin) |
| Select test framework | `1` (JUnit 4) |
| Generate build using new APIs? | `no` |

Wait for `BUILD SUCCESSFUL`. Then navigate into the project:
```bash
cd kotlinProject
```

### 📁 Step 3: Understand the Folder Structure
After init, your folder looks like this:
```text
kotlinProject/
├── app/
│   ├── build.gradle.kts        ← edit this
│   └── src/
│       ├── main/java/org/example/App.java   ← rename + edit
│       └── test/java/org/example/AppTest.java ← rename + edit
├── gradle/
├── gradlew
├── gradlew.bat
└── settings.gradle.kts
```
⚠️ **Note:** All source files are inside the `app` subfolder, not the root!

### 📝 Step 4: Edit `build.gradle.kts`
Navigate to the `app` folder and open the file:
```bash
cd app
notepad build.gradle.kts
```
Delete everything and paste this exact code, then Save & Close:
```kotlin
plugins {
    kotlin("jvm") version "1.8.21"
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

### 📁 Step 5: Fix the Source Folder Structure
The default project creates `java` folders. We need to create `kotlin` folders instead.
Run these commands (you should still be inside the `app` folder):
```bash
mkdir src\main\kotlin\com\example
mkdir src\test\kotlin\com\example
```

### ☕ Step 6: Create `Main.kt`
```bash
notepad src\main\kotlin\com\example\Main.kt
```
A dialog will ask "Do you want to create a new file?" — click **Yes**.
Paste this code → Save & Close:
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

### 🧪 Step 7: Create `MainTest.kt`
```bash
notepad src\test\kotlin\com\example\MainTest.kt
```
Click **Yes** to create. Paste this code → Save & Close:
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

### 🗑️ Step 8: Delete the Old Java Files
The default Java files will cause conflicts. Delete them:
```bash
del src\main\java\org\example\App.java
del src\test\java\org\example\AppTest.java
```

### ▶️ Step 9: Go Back to Root and Run Commands
Go back to the project root (where `gradlew.bat` is):
```bash
cd ..
```

**1. Build the project:**
```bash
gradlew build
```
✅ **Expected:** `BUILD SUCCESSFUL`

**2. Run the application:**
```bash
gradlew run
```
✅ **Expected output:** `The sum of 10.0 and 5.0 is: 15.0`

**3. Run the tests:**
```bash
gradlew test
```
✅ **Expected output:** 
```text
com.example.MainTest > testAddNumbers PASSED
BUILD SUCCESSFUL
```

### 🆘 Common Errors & Fixes

| Error | Fix |
|---|---|
| Could not find `kotlin-gradle-plugin` | You need internet connection for first build |
| Main class not found | Make sure `mainClass.set("com.example.MainKt")` is in `build.gradle.kts` |
| Unresolved reference: `addNumbers` | Make sure both files have `package com.example` at the top |
| `BUILD FAILED` on old Java files | Make sure you deleted `App.java` and `AppTest.java` in Step 8 |
| `gradlew` not recognized | Make sure you're in the root `kotlinProject/` folder, not inside `app/` |

---

## Program 4: Build with Maven, then Migrate to Gradle

**Base:** We continue from Program 2's `program2` folder. If you already did Program 2, just open a new terminal and follow from Step 1.

### 📁 Step 1: Create a Fresh Working Folder
```bash
mkdir program4
cd program4
```

### 🏗️ Step 2: Create the Maven Project
```bash
mvn archetype:generate -DgroupId=com.example -DartifactId=maven-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```
Wait for `BUILD SUCCESS`, then navigate in:
```bash
cd maven-example
```

### 📝 Step 3: Edit `pom.xml`
```bash
notepad pom.xml
```
Delete everything → Paste the code below → Save & Close:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
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

### ☕ Step 4: Edit `App.java`
```bash
notepad src\main\java\com\example\App.java
```
Delete everything → Paste the code below → Save & Close:
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
⚠️ **Note:** No test file needed for this program. The existing `AppTest.java` can stay as-is.

### ▶️ Step 5: Build & Run with Maven
Make sure you are inside `maven-example` folder:
```bash
cd maven-example
```
**1. Build the project:**
```bash
mvn clean install
```
✅ **Expected:** `BUILD SUCCESS`

**2. Run the application:**
```bash
mvn exec:java -Dexec.mainClass="com.example.App"
```
✅ **Maven Output:**
```text
Hello, Maven
This is the simple realworld example....
Sum of 5 and 10 is 15
```

### 🔄 PART 2 — Migrate Maven → Gradle

### 🔧 Step 6: Initialize Gradle Inside the Same Project
You are still inside the `maven-example` folder. Run:
```bash
gradle init
```
It will ask questions — answer exactly like this:

| Question | Answer |
|---|---|
| Found a Maven build. Generate a Gradle build? | `yes` |
| Select build script DSL — 1: Kotlin / 2: Groovy | `2` |
| Generate build using new APIs? | `no` |

Wait for `BUILD SUCCESSFUL`.

### 📝 Step 7: Edit `build.gradle`
```bash
notepad build.gradle
```
Delete everything → Paste the code below → Save & Close:
```groovy
plugins {
    id 'java'
}

group = 'com.example'
version = '1.0-SNAPSHOT'

repositories {
    mavenCentral()
}

dependencies {
    testImplementation 'junit:junit:4.12'
}

task run(type: JavaExec) {
    main = 'com.example.App'
    classpath = sourceSets.main.runtimeClasspath
}
```

### ▶️ Step 8: Build & Run with Gradle

**1. Build the project:**
```bash
gradlew build
```
✅ **Expected:** `BUILD SUCCESSFUL`

**2. Run the application:**
```bash
gradlew run
```
✅ **Gradle Output:**
```text
Hello, Maven
This is the simple realworld example....
Sum of 5 and 10 is 15
```

### ✅ Step 9: Verify Both Outputs Match

| Tool | Output |
|---|---|
| **Maven** | Hello, Maven → This is the simple realworld example.... → Sum of 5 and 10 is 15 |
| **Gradle** | Hello, Maven → This is the simple realworld example.... → Sum of 5 and 10 is 15 |

Both outputs are identical — migration successful! ✅

### 🆘 Common Errors & Fixes

| Error | Fix |
|---|---|
| `mvn` not recognized | Maven not in PATH — reinstall or fix environment variables |
| `gradlew` not recognized | Make sure you're inside `maven-example` folder |
| Could not find or load main class | Check `App.java` has `package com.example;` at the top |
| Task 'run' not found | Make sure `build.gradle` was saved correctly with the task run block |
| `BUILD FAILED` on `gradlew build` | Run `gradlew build --info` to see detailed error, share it for help |

---

## Program 5: Introduction to Jenkins: Complete Beginner Guide

### 📌 What is Jenkins?
Jenkins is a free automation tool that automatically:
* Builds your code
* Tests your code
* Deploys your application

Think of it as a robot assistant that does all the repetitive work for you whenever you write new code.

### ✅ Step 1: Check if Java is Installed
Open Command Prompt and type:
```bash
java -version
```
If you see something like `java version "21.0.x"`, you're good.
If you get an error — Download and install JDK 21 from: [Oracle JDK 21 Downloads](https://www.oracle.com/in/java/technologies/downloads/#jdk21-windows)
⚠️ **Note:** Jenkins requires Java 21 specifically. Other versions may cause issues.

### 📥 Step 2: Download Jenkins
Open your browser and go to: [https://www.jenkins.io/download/](https://www.jenkins.io/download/)
* Under Windows, click the **Windows Installer (.msi)** download button
* Wait for the `.msi` file to download to your computer

### 🔧 Step 3: Install Jenkins (Windows Installer)
Go to your Downloads folder and double-click the `.msi` file.
A setup wizard will open — follow these steps carefully:

**🪟 Installation Screen by Screen:**
1. **Welcome:** Click **Next**
2. **Destination Folder:** Leave the default path as is, click **Next**
3. **Service Logon Credentials:** Select "Run service as LocalSystem"
   *(⚠️ The installer says "not recommended" — but for local learning/lab use, this is fine and simplest)*, click **Next**
4. **Port Selection:** Default port is `8080`. If port `8080` is already in use on your PC, change it to `3030` or any free port. Click **Test Port** button — wait for green checkmark ✅. Click **Next**
5. **Java Path:** It will try to auto-detect Java. If not detected, manually browse to your JDK path which looks like: `C:\Program Files\Java\jdk-21\`. Click **Next**
6. **Firewall Exception:** Leave default checked, click **Next**
7. **Ready to Install:** Click **Install**. A popup will ask "Do you want to allow this app to make changes?" — click **Yes**
8. **Installation Progress:** Wait for the progress bar to complete ⏳
9. **Finished:** Click **Finish**

### 🌐 Step 4: Open Jenkins in Browser
After installation, open your browser and type: `http://localhost:8080` (or `http://localhost:3030` if you changed it).
You should see the "Unlock Jenkins" screen.

### 🔑 Step 5: Get the Admin Password
Open File Explorer (`Windows + E`) and navigate to this path (paste it in the address bar):
`C:\ProgramData\Jenkins\.jenkins\secrets\`
*(⚠️ `ProgramData` is a hidden folder. In File Explorer → click View tab → check Hidden Items)*

You will see a file called `initialAdminPassword`.
Right-click on it → Open with → Notepad.
You will see a long password. Select all → Copy (`Ctrl+A` → `Ctrl+C`).
Go back to the browser → Paste it in the password box → Click **Continue**.

### 🔌 Step 6: Install Suggested Plugins
Click **"Install suggested plugins"** (the left option).
Jenkins will automatically install all required plugins. Wait for it to finish (this may take 3-5 minutes).

### 👤 Step 7: Create Your Admin Account
Fill in the details:

| Field | What to Enter |
|---|---|
| Username | `admin` (or any name you like) |
| Password | Choose a password you'll remember |
| Confirm Password | Same password again |
| Full Name | Your name |
| Email | Your email address |

Click **"Save and Continue"**.

### 🌍 Step 8: Configure Jenkins URL
The next screen shows the Jenkins URL (e.g. `http://localhost:8080/`). Leave it as default and click **"Save and Finish"**.

### 🚀 Step 9: Start Using Jenkins
Click **"Start using Jenkins"**. You will land on the Jenkins Dashboard! 🎉

### 🆘 Common Problems & Fixes

| Problem | Fix |
|---|---|
| Browser shows "This site can't be reached" | Wait 1-2 mins after install, Jenkins takes time to start |
| Port `8080` already in use | Change to port `3030` during installation |
| `initialAdminPassword` file not visible | Enable hidden files in File Explorer → View → Hidden Items |
| Plugins fail to install | Check your internet connection and try again |
| Jenkins service not running | Search "Services" in Start Menu → find "Jenkins" → click Start |

---

## Program 6: Integrating Jenkins with Maven/Gradle & Running Automated Builds and Tests

### 📌 What We Will Do
* Connect Jenkins to a Maven project
* Run automated builds and tests from Jenkins
* See build results on Jenkins Dashboard

✅ **Prerequisite:** Jenkins must be installed and running (Program 5 done). Maven must be installed. We will use the Program 2 Maven project as our base.

### 🔧 Step 1: Configure Java & Maven Inside Jenkins
Before creating any job, we need to tell Jenkins where Java and Maven are installed on your computer.

1. **Open Jenkins in Browser:** `http://localhost:8080` and Login.
2. **Go to Global Tool Configuration:** On the Dashboard, click **"Manage Jenkins"** (left sidebar), then click **"Tools"** (or "Global Tool Configuration").
3. **Configure JDK:** Scroll down to the JDK section:
   * Click **"Add JDK"**
   * Uncheck **"Install automatically"**
   * Fill in `Name`: `JDK-21`, `JAVA_HOME`: `C:\Program Files\Java\jdk-21`
4. **Configure Maven:** Scroll down to the Maven section:
   * Click **"Add Maven"**
   * Uncheck **"Install automatically"**
   * Fill in `Name`: `Maven-3`, `MAVEN_HOME`: `C:\Program Files\Maven\apache-maven-3.x.x`
   * Click **"Save"** at the bottom.

### 📁 Step 2: Prepare Your Maven Project
We will use the exact project from Program 2. Note down the full path of your project (e.g., `C:\Users\YourName\program2\myapp`).

### 🆕 Step 3: Create a New Jenkins Job
* On Jenkins Dashboard, click **"New Item"**
* Type the name: `MavenBuildJob`
* Select **"Freestyle project"**
* Click **OK**

### ⚙️ Step 4: Configure the Job
1. **General Section:** Description: `Maven build and test job using Program 2 project`
2. **Source Code Management:** Select **"None"**
3. **Build Steps:**
   * Click **"Add build step"** → Select **"Invoke top-level Maven targets"**
   * Maven Version: `Maven-3`
   * Goals: `clean install`
   * Click **"Advanced"** → In POM field, enter the full path to `pom.xml` (e.g. `C:\Users\YourName\program2\myapp\pom.xml`)
4. **Add a Second Build Step for Tests:**
   * Click **"Add build step"** → Select **"Invoke top-level Maven targets"**
   * Maven Version: `Maven-3`
   * Goals: `test`
   * Click **"Advanced"** → POM field: `C:\Users\YourName\program2\myapp\pom.xml`
5. **Post-Build Actions:**
   * Click **"Add post-build action"** → Select **"Publish JUnit test results report"**
   * Test report XMLs field: `C:\Users\YourName\program2\myapp\target\surefire-reports\*.xml`
6. **Save the Job:** Click **"Save"**

### ▶️ Step 5: Run the Build
On the `MavenBuildJob` page, click **"Build Now"**. Look at the "Build History" (bottom left). A blue/green dot = SUCCESS ✅, red dot = FAILED ❌.

### 📋 Step 6: View Build Output (Console Log)
Click on `#1` in Build History, then click **"Console Output"**. Look for `[INFO] BUILD SUCCESS` and `Tests run: 1, Failures: 0`.

### 📊 Step 7: View Test Results on Dashboard
Go back to `MavenBuildJob` page, click **"Test Result"** link to see the test report.

### 🔄 Step 8: Integrate with Gradle (Same Project)
Now we will create a second job for the Gradle version using the same migrated project from Program 4.

1. **Configure Gradle in Jenkins Tools:**
   * Manage Jenkins → Tools → Gradle section
   * Click **"Add Gradle"** → Uncheck **"Install automatically"**
   * Name: `Gradle-8`, GRADLE_HOME: *Your Gradle installation path*
   * Click **Save**
2. **Create a New Gradle Job:**
   * Dashboard → **"New Item"** → Name: `GradleBuildJob` → **"Freestyle project"** → **OK**
3. **Configure the Gradle Job:**
   * Build Steps → **"Add build step"** → **"Invoke Gradle script"**
   * Select **"Use Gradle Wrapper"** or **"Invoke Gradle"** (pick `Gradle-8`)
   * Tasks: `clean build test`
   * Build File: *Full path to your build.gradle* (e.g., `C:\Users\YourName\program4\maven-example\build.gradle`)
   * Click **Save** → Click **"Build Now"**

### 📋 Step 9: View Gradle Build Console Output
Click `#1` → Console Output. Look for `BUILD SUCCESSFUL`.

### 🆘 Common Errors & Fixes

| Error | Fix |
|---|---|
| `JAVA_HOME` not set | Re-check JDK path in Manage Jenkins → Tools |
| Maven not found | Re-check Maven path in Manage Jenkins → Tools |
| `pom.xml` not found | Make sure the full path in POM field is correct |
| Build #1 shows red | Click Console Output — read the error message |
| No test reports found | Run `mvn test` manually first to generate `surefire-reports` folder |
