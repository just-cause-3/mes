# BCSL657D — Program 2

## Working with Maven: Creating a Maven Project, Understanding the POM File, Dependency Management and Plugins

---

## 📋 Prerequisites

Before starting, ensure the following are installed and configured:

| Requirement       | Verify Command          | Expected Output              |
| ----------------- | ----------------------- | ---------------------------- |
| Java JDK (8+)     | `java -version`         | java version "1.8.x" or higher |
| Maven             | `mvn -v`                | Apache Maven 3.x.x          |

> **If Maven or Java is not installed**, refer to Program 1 for installation and environment variable setup.

---

## 🗂️ Overview

In this program, you will:

1. Create a Maven project using the command line
2. Understand the POM (`pom.xml`) file structure
3. Add dependencies (JUnit) and plugins (Surefire)
4. Write a simple Java application (`App.java`)
5. Write a JUnit test (`AppTest.java`)
6. Compile, test, package, and run the application

---

## 🚀 Step-by-Step Execution

### Step 1: Create a Working Folder

Open **Command Prompt** and run:

```cmd
mkdir program2
cd program2
```

---

### Step 2: Generate the Maven Project

Run this single command (copy-paste the entire line):

```cmd
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

**What this does:**
- `-DgroupId=com.example` → Sets the project group/package name
- `-DartifactId=myapp` → Sets the project/folder name to `myapp`
- `-DarchetypeArtifactId=maven-archetype-quickstart` → Uses the quickstart template
- `-DinteractiveMode=false` → Skips all prompts for automatic generation

**Expected Output (last few lines):**
```
[INFO] BUILD SUCCESS
[INFO] Total time: ...
```

> A folder named `myapp` is now created inside `program2`.

---

### Step 3: Navigate into the Project Folder

```cmd
cd myapp
```

**Verify the generated directory structure by running:**

```cmd
tree /F
```

**Expected structure:**

```
myapp/
├── pom.xml
└── src/
    ├── main/
    │   └── java/
    │       └── com/
    │           └── example/
    │               └── App.java
    └── test/
        └── java/
            └── com/
                └── example/
                    └── AppTest.java
```

---

### Step 4: Edit the `pom.xml` File

Open the `pom.xml` file in Notepad:

```cmd
notepad pom.xml
```

**Delete ALL existing content** in the file, then **copy-paste the entire block below** and save (`Ctrl + S`):

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

**Key elements explained:**

| Element              | Purpose                                                    |
| -------------------- | ---------------------------------------------------------- |
| `<groupId>`          | Organization/group identifier (like a package namespace)   |
| `<artifactId>`       | Project name                                               |
| `<version>`          | Project version (`SNAPSHOT` = under development)           |
| `<dependencies>`     | External libraries the project needs                       |
| `<scope>test</scope>`| JUnit is only needed during testing, not at runtime        |
| `<build><plugins>`   | Configures build behavior (Surefire runs unit tests)       |

---

### Step 5: Edit the `App.java` File

Open the main application file:

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

    public static void main(String[] args) {
        App app = new App();

        int result = app.add(2, 3);
        System.out.println("2 + 3 = " + result);

        System.out.println("Application executed successfully!");
    }
}
```

---

### Step 6: Edit the `AppTest.java` File

Open the test file:

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
        int result = app.add(2, 3);

        System.out.println("Running test: 2 + 3 = " + result);

        Assert.assertEquals(5, result);
    }
}
```

---

### Step 7: Compile the Project

> **Important:** Make sure you are inside the `myapp` folder before running any Maven commands.

```cmd
mvn compile
```

**Expected Output (last few lines):**
```
[INFO] BUILD SUCCESS
```

---

### Step 8: Run the Unit Tests

```cmd
mvn test
```

**Expected Output (key lines):**
```
Running test: 2 + 3 = 5
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] BUILD SUCCESS
```

---

### Step 9: Package the Project into a JAR

```cmd
mvn package
```

**Expected Output (last few lines):**
```
[INFO] Building jar: ...\target\myapp-1.0-SNAPSHOT.jar
[INFO] BUILD SUCCESS
```

> This creates a JAR file at `target\myapp-1.0-SNAPSHOT.jar`.

---

### Step 10: Run the Application

```cmd
java -cp target/myapp-1.0-SNAPSHOT.jar com.example.App
```

**Expected Final Output:**
```
2 + 3 = 5
Application executed successfully!
```

---

## ✅ Summary of All Commands (Quick Reference)

Run these commands sequentially from a fresh Command Prompt:

```cmd
mkdir program2
cd program2
mvn archetype:generate -DgroupId=com.example -DartifactId=myapp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd myapp
notepad pom.xml
notepad src\main\java\com\example\App.java
notepad src\test\java\com\example\AppTest.java
mvn compile
mvn test
mvn package
java -cp target/myapp-1.0-SNAPSHOT.jar com.example.App
```

> For each `notepad` command, replace the file content with the code blocks provided in Steps 4, 5, and 6 above.

---

## 📝 Theory Notes

### What is Maven?
Maven is a **build automation tool** primarily used for Java projects. It uses an XML configuration file called `pom.xml` (Project Object Model) to define project settings, dependencies, and build steps.

### POM File (`pom.xml`)
The POM file is the **heart** of a Maven project. It contains:
- Project coordinates (`groupId`, `artifactId`, `version`)
- Dependencies (external libraries)
- Build plugins (compile, test, package tools)

### Dependency Management
- Maven automatically downloads dependencies from **Maven Central** repository
- **Transitive dependencies** are resolved automatically (if library A depends on B, Maven downloads both)
- Dependency **scopes**: `compile` (default), `provided`, `runtime`, `test`

### Plugins
Plugins perform tasks during the build lifecycle:

| Plugin                    | Purpose                           |
| ------------------------- | --------------------------------- |
| `maven-compiler-plugin`   | Compiles Java source code         |
| `maven-surefire-plugin`   | Runs unit tests                   |
| `maven-jar-plugin`        | Packages project as a JAR file    |
| `maven-clean-plugin`      | Cleans the `target/` directory    |

### Common Maven Commands

| Command             | What it Does                                         |
| ------------------- | ---------------------------------------------------- |
| `mvn compile`       | Compiles the source code                             |
| `mvn test`          | Runs unit tests                                      |
| `mvn package`       | Compiles + tests + packages into JAR                 |
| `mvn clean install` | Cleans old build, compiles, tests, packages, installs|
| `mvn clean`         | Deletes the `target/` directory                      |

---

## ⚠️ Troubleshooting

| Problem                            | Solution                                                                  |
| ---------------------------------- | ------------------------------------------------------------------------- |
| `mvn` is not recognized            | Maven is not in your PATH. Add Maven's `bin` folder to environment variables |
| `java` is not recognized           | Java JDK is not in your PATH. Add JDK's `bin` folder to environment variables |
| BUILD FAILURE during `mvn compile` | Check `pom.xml` for typos — ensure you copied the exact XML from Step 4    |
| Tests fail                         | Verify `AppTest.java` matches exactly — check the `assertEquals` line      |
| JAR not found when running         | Make sure you ran `mvn package` first and are in the `myapp` directory     |
| Smart quotes (`"` `"`) in XML      | If you pasted from a PDF, replace curly/smart quotes with straight quotes `"` |

---

**Program 2 Complete ✔**
