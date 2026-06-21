# BCSL657D — Program 4

## Practical Exercise: Build and Run a Java Application with Maven, Migrate the Same Application to Gradle

---

## 📋 Prerequisites

Before starting, ensure the following are installed and configured:

| Requirement       | Verify Command          | Expected Output                |
| ----------------- | ----------------------- | ------------------------------ |
| Java JDK (8+)     | `java -version`         | java version "1.8.x" or higher |
| Maven             | `mvn -v`                | Apache Maven 3.x.x            |
| Gradle            | `gradle -v`             | Gradle 8.x.x (or similar)     |

> **If any tool is not installed**, refer to Program 1 for installation and environment variable setup.

---

## 🗂️ Overview

In this program, you will:

1. **Part A** — Create and run a Java application using **Maven**
2. **Part B** — Migrate the same project from Maven to **Gradle**
3. Verify that both builds produce **identical output**

---

# Part A — Build and Run with Maven

---

### Step 1: Create a Working Folder

Open **Command Prompt** and run:

```cmd
mkdir program4
cd program4
```

---

### Step 2: Generate the Maven Project

```cmd
mvn archetype:generate -DgroupId=com.example -DartifactId=maven-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

**Expected Output (last few lines):**
```
[INFO] BUILD SUCCESS
```

> A folder named `maven-example` is created inside `program4`.

---

### Step 3: Navigate into the Project Folder

```cmd
cd maven-example
```

---

### Step 4: Edit the `pom.xml` File

Open the POM file:

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

---

### Step 6: Build the Maven Project

```cmd
mvn clean install
```

**Expected Output (last few lines):**
```
[INFO] BUILD SUCCESS
```

---

### Step 7: Run the Maven Application

```cmd
mvn exec:java -Dexec.mainClass="com.example.App"
```

**Expected Output:**
```
Hello, Maven
This is the simple realworld example....
Sum of 5 and 10 is 15
```

> ⚠️ **If `mvn exec:java` gives an error**, you can also run the application using the JAR directly. First package it, then run:
>
> ```cmd
> mvn package
> java -cp target/maven-example-1.0-SNAPSHOT.jar com.example.App
> ```

---

# Part B — Migrate from Maven to Gradle

> You will now convert the **same project** from Maven to Gradle, without leaving the project folder.

---

### Step 8: Initialize Gradle inside the Maven Project

Make sure you are still inside the `maven-example` folder, then run:

```cmd
gradle init
```

**It will ask several questions interactively. Enter the responses shown below:**

```
Found a Maven build. Generate a Gradle build from this? (default: yes) [yes, no]
yes

Select build script DSL:
  1: Kotlin
  2: Groovy
Enter selection (default: Kotlin) [1..2]
2

Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no]
no
```

**Expected Output (last few lines):**
```
BUILD SUCCESSFUL
```

> ⚠️ **Important:** Gradle detects the existing `pom.xml` and automatically converts it to a Gradle build. It creates `build.gradle`, `settings.gradle`, and the Gradle wrapper files.

---

### Step 9: Verify the Generated `build.gradle` File

Open the generated file:

```cmd
notepad build.gradle
```

**Delete ALL existing content**, then **copy-paste the entire block below** and save:

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

**Key elements explained:**

| Element                             | Purpose                                              |
| ----------------------------------- | ---------------------------------------------------- |
| `id 'java'`                        | Applies the Java plugin for compiling Java code      |
| `group` / `version`                | Project coordinates (same as Maven's groupId/version)|
| `mavenCentral()`                   | Downloads dependencies from Maven Central            |
| `testImplementation 'junit:...'`   | JUnit dependency for testing only                    |
| `task run(type: JavaExec)`         | Custom task to run the application                   |
| `main = 'com.example.App'`        | Specifies the class containing `main()` method       |

---

### Step 10: Build the Gradle Project

```cmd
gradlew build
```

> ⚠️ **Note:** Use `gradlew` (Gradle Wrapper) instead of `gradle`. The wrapper was generated during `gradle init` and ensures the correct Gradle version is used.

**Expected Output (last few lines):**
```
BUILD SUCCESSFUL
```

---

### Step 11: Run the Gradle Application

```cmd
gradlew run
```

**Expected Output:**
```
> Task :run
Hello, Maven
This is the simple realworld example....
Sum of 5 and 10 is 15

BUILD SUCCESSFUL
```

---

### Step 12: Verify the Migration

Compare the output from **both** builds — they should be identical:

| Build Tool | Output                                                                                        |
| ---------- | --------------------------------------------------------------------------------------------- |
| **Maven**  | `Hello, Maven` / `This is the simple realworld example....` / `Sum of 5 and 10 is 15`       |
| **Gradle** | `Hello, Maven` / `This is the simple realworld example....` / `Sum of 5 and 10 is 15`       |

✅ **Migration successful** — both Maven and Gradle produce the same output from the same source code.

---

## ✅ Summary of All Commands (Quick Reference)

### Part A — Maven

```cmd
mkdir program4
cd program4
mvn archetype:generate -DgroupId=com.example -DartifactId=maven-example -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
cd maven-example
notepad pom.xml
notepad src\main\java\com\example\App.java
mvn clean install
mvn exec:java -Dexec.mainClass="com.example.App"
```

### Part B — Gradle Migration (run from inside `maven-example` folder)

```cmd
gradle init
notepad build.gradle
gradlew build
gradlew run
```

> For each `notepad` command, replace the file content with the code blocks provided in the steps above.

---

## 📝 Theory Notes

### What is Build Migration?
Build migration is the process of converting a project from one build tool to another (e.g., Maven → Gradle) while keeping the **same source code, dependencies, and behavior**.

### Why Migrate from Maven to Gradle?
| Advantage              | Explanation                                                   |
| ---------------------- | ------------------------------------------------------------- |
| **Faster builds**      | Gradle uses caching and incremental builds                    |
| **Concise scripts**    | `build.gradle` is much shorter than `pom.xml`                 |
| **Flexible**           | Gradle supports custom tasks and complex build logic easily   |
| **Multi-language**     | Gradle supports Java, Kotlin, Groovy, C++, and more          |

### How `gradle init` Converts Maven Projects
When `gradle init` detects a `pom.xml` in the current directory, it:
1. Reads all Maven dependencies and converts them to Gradle format
2. Creates `build.gradle` (or `build.gradle.kts`) with equivalent configuration
3. Generates `settings.gradle` with the project name
4. Creates the Gradle Wrapper (`gradlew` / `gradlew.bat`) for portable builds

### Maven vs Gradle — Configuration Comparison

**Maven (`pom.xml`):**
```xml
<dependencies>
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.12</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Gradle (`build.gradle`):**
```groovy
dependencies {
    testImplementation 'junit:junit:4.12'
}
```

> The same dependency takes **7 lines in Maven** vs **1 line in Gradle**.

### Gradle Wrapper (`gradlew`)
The Gradle Wrapper is a script that downloads and uses a specific Gradle version for the project. This ensures everyone on the team uses the same Gradle version, regardless of what's installed globally.

| File                             | Purpose                                   |
| -------------------------------- | ----------------------------------------- |
| `gradlew` (Linux/Mac)           | Wrapper shell script                      |
| `gradlew.bat` (Windows)         | Wrapper batch script                      |
| `gradle/wrapper/gradle-wrapper.properties` | Specifies which Gradle version to use |

---

## ⚠️ Troubleshooting

| Problem                                         | Solution                                                                             |
| ------------------------------------------------ | ------------------------------------------------------------------------------------ |
| `mvn exec:java` gives plugin error               | Run `mvn package` then `java -cp target/maven-example-1.0-SNAPSHOT.jar com.example.App` instead |
| `gradle init` doesn't detect Maven               | Ensure you are inside the `maven-example` folder that contains `pom.xml`             |
| `gradlew` is not recognized                      | Use `.\gradlew` (with dot-backslash prefix) in PowerShell, or `gradlew` in CMD       |
| `gradlew build` fails — "Could not find method main()" | Ensure `build.gradle` uses `main = 'com.example.App'` (not `mainClass`)        |
| BUILD FAILED — "Could not resolve junit"         | Check internet connection; Gradle needs to download dependencies                     |
| Output differs between Maven and Gradle          | Verify `App.java` was not modified between builds                                    |
| `gradle init` asks different questions            | Gradle versions vary; answer based on context — say Yes to Maven conversion, pick Groovy DSL |
| Smart quotes (`"` `"`) cause errors              | If pasting from PDF, replace curly/smart quotes with straight quotes `"`             |

---

**Program 4 Complete ✔**
