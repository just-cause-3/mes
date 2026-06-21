# BCSL657D — Program 3

## Working with Gradle: Setting Up a Gradle Project, Understanding Build Scripts (Kotlin DSL), Dependency Management and Task Automation

---

## 📋 Prerequisites

Before starting, ensure the following are installed and configured:

| Requirement       | Verify Command          | Expected Output                |
| ----------------- | ----------------------- | ------------------------------ |
| Java JDK 17+      | `java -version`         | java version "17.x.x" or higher |
| Gradle            | `gradle -v`             | Gradle 8.x.x (or similar)     |

> **If Gradle or Java is not installed**, refer to Program 1 for installation and environment variable setup.

---

## 🗂️ Overview

In this program, you will:

1. Create a Gradle project using `gradle init` with **Kotlin DSL**
2. Understand the `build.gradle.kts` build script
3. Write a Kotlin application (`Main.kt`)
4. Write a JUnit 4 test (`MainTest.kt`)
5. Build, run, and test the project using Gradle commands

---

## 🚀 Step-by-Step Execution

### Step 1: Create a Working Folder

Open **Command Prompt** and run:

```cmd
mkdir program3
cd program3
```

---

### Step 2: Initialize the Gradle Project

Run the following command:

```cmd
gradle init --type java-application
```

**It will ask several questions interactively. Enter the responses shown below:**

```
Enter target Java version (min: 7, default: 21): 17

Project name (default: program3): kotlinProject

Select application structure:
  1: Single application project
  2: Application and library project
Enter selection (default: Single application project) [1..2]: 1

Select build script DSL:
  1: Kotlin
  2: Groovy
Enter selection (default: Kotlin) [1..2]: 1

Select test framework:
  1: JUnit 4
  2: TestNG
  3: Spock
  4: JUnit Jupiter
Enter selection (default: JUnit Jupiter) [1..4]: 1

Generate build using new APIs and behavior (some features may change in the next minor release)? (default: no) [yes, no]: no
```

> ⚠️ **Important:** Type each response exactly as shown and press **Enter** after each one.

**Expected Output (last few lines):**
```
BUILD SUCCESSFUL
```

---

### Step 3: Navigate into the Generated Project

After `gradle init`, the project files are created inside an `app` subfolder. Navigate into it:

```cmd
cd app
```

**Verify the project structure:**

```cmd
tree /F
```

**Expected structure (simplified):**

```
app/
├── build.gradle.kts
└── src/
    ├── main/
    │   └── java/
    │       └── org/
    │           └── example/
    │               └── App.java
    └── test/
        └── java/
            └── org/
                └── example/
                    └── AppTest.java
```

> ⚠️ **Note:** Gradle generates the default package as `org.example`. We will change this to `com.example` and rename the files to Kotlin (`.kt`) files.

---

### Step 4: Restructure the Source Directories

We need to replace `org/example` with `com/example` and change `.java` files to `.kt` files.

**Run these commands one by one:**

**4a. Remove the old source directories:**

```cmd
rmdir /S /Q src\main\java\org
rmdir /S /Q src\test\java\org
```

**4b. Create the new package directories:**

```cmd
mkdir src\main\java\com\example
mkdir src\test\java\com\example
```

---

### Step 5: Edit the `build.gradle.kts` File

Open the build script:

```cmd
notepad build.gradle.kts
```

**Delete ALL existing content**, then **copy-paste the entire block below** and save (`Ctrl + S`):

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

**Key elements explained:**

| Element                                  | Purpose                                                      |
| ---------------------------------------- | ------------------------------------------------------------ |
| `kotlin("jvm") version "1.8.21"`         | Applies the Kotlin JVM plugin for compiling Kotlin code      |
| `application`                            | Enables the `run` task to execute the main class             |
| `mavenCentral()`                         | Downloads dependencies from Maven Central repository         |
| `implementation(kotlin("stdlib"))`       | Adds Kotlin standard library as a dependency                 |
| `testImplementation("junit:junit:4.13.2")` | Adds JUnit 4 for unit testing                              |
| `mainClass.set("com.example.MainKt")`    | Tells Gradle which class has the `main()` function           |
| `useJUnit()`                             | Configures test task to use JUnit framework                  |
| `showStandardStreams = true`             | Shows `println` output during test execution                 |
| `JavaLanguageVersion.of(17)`             | Sets Java 17 as the compilation target                       |

> ⚠️ **Why `MainKt`?** In Kotlin, a top-level `main()` function in a file named `Main.kt` compiles to a class called `MainKt`. This is how Kotlin maps file names to JVM class names.

---

### Step 6: Create the `Main.kt` File

Create and open the Kotlin source file:

```cmd
notepad src\main\java\com\example\Main.kt
```

When Notepad asks *"Do you want to create a new file?"*, click **Yes**.

**Copy-paste the entire block below** and save:

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

---

### Step 7: Create the `MainTest.kt` File

Create and open the Kotlin test file:

```cmd
notepad src\test\java\com\example\MainTest.kt
```

When Notepad asks *"Do you want to create a new file?"*, click **Yes**.

**Copy-paste the entire block below** and save:

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

---

### Step 8: Build the Project

> **Important:** Make sure you are inside the `app` folder (the one containing `build.gradle.kts`).

```cmd
gradle build
```

**Expected Output (last few lines):**
```
BUILD SUCCESSFUL in Xs
```

> The first build may take a while as Gradle downloads the Kotlin plugin and dependencies.

---

### Step 9: Run the Application

```cmd
gradle run
```

**Expected Output:**
```
> Task :app:run
The sum of 10.0 and 5.0 is: 15.0

BUILD SUCCESSFUL
```

---

### Step 10: Run the Unit Tests

```cmd
gradle test
```

**Expected Output:**
```
> Task :app:test

com.example.MainTest > testAddNumbers PASSED

BUILD SUCCESSFUL
```

---

## ✅ Summary of All Commands (Quick Reference)

Run these commands sequentially from a fresh Command Prompt:

```cmd
mkdir program3
cd program3
gradle init --type java-application
cd app
rmdir /S /Q src\main\java\org
rmdir /S /Q src\test\java\org
mkdir src\main\java\com\example
mkdir src\test\java\com\example
notepad build.gradle.kts
notepad src\main\java\com\example\Main.kt
notepad src\test\java\com\example\MainTest.kt
gradle build
gradle run
gradle test
```

> For each `notepad` command, replace/create the file content with the code blocks provided in Steps 5, 6, and 7 above.

> During `gradle init`, enter the interactive responses from Step 2.

---

## 📝 Theory Notes

### What is Gradle?
Gradle is a modern, flexible **build automation tool** that supports multiple languages (Java, Kotlin, Groovy, etc.). Unlike Maven's verbose XML, Gradle uses a **DSL (Domain-Specific Language)** — either Groovy or Kotlin — for concise, readable build scripts.

### Build Script — Kotlin DSL (`build.gradle.kts`)
The `.kts` extension indicates a **Kotlin-based** build script. It defines:
- **Plugins** — tools that add build capabilities (e.g., `kotlin("jvm")`, `application`)
- **Repositories** — where to download dependencies from (e.g., `mavenCentral()`)
- **Dependencies** — external libraries the project needs
- **Tasks** — configurable build actions (compile, test, run, etc.)

### Dependency Management
Gradle uses **configuration scopes** to define when dependencies are needed:

| Scope                  | Purpose                                        |
| ---------------------- | ---------------------------------------------- |
| `implementation`       | Available at compile time and runtime          |
| `testImplementation`   | Available only during testing                  |
| `runtimeOnly`          | Needed only at runtime, not during compilation |
| `compileOnly`          | Needed only during compilation, not at runtime |

### Task Automation
Gradle's build is composed of **tasks**. Common tasks include:

| Command          | What it Does                                      |
| ---------------- | ------------------------------------------------- |
| `gradle build`   | Compiles code, runs tests, creates build artifacts |
| `gradle run`     | Executes the application's `main()` function      |
| `gradle test`    | Runs all unit tests and generates reports          |
| `gradle clean`   | Deletes the `build/` directory                    |
| `gradle tasks`   | Lists all available tasks                          |

### Gradle vs Maven (Key Differences)

| Aspect             | Maven                 | Gradle                          |
| ------------------ | --------------------- | ------------------------------- |
| Config File        | `pom.xml` (XML)       | `build.gradle.kts` (Kotlin DSL) |
| Performance        | Slower                | Faster (caching & incremental)  |
| Flexibility        | Convention-based      | Highly customizable             |
| Script Readability | Verbose               | Concise                         |

---

## ⚠️ Troubleshooting

| Problem                                  | Solution                                                                       |
| ---------------------------------------- | ------------------------------------------------------------------------------ |
| `gradle` is not recognized               | Gradle is not in your PATH. Add Gradle's `bin` folder to environment variables |
| BUILD FAILED — "Could not resolve kotlin" | Check internet connection; Gradle needs to download the Kotlin plugin          |
| `MainKt` class not found when running    | Verify `mainClass.set("com.example.MainKt")` in `build.gradle.kts`            |
| Tests not found / 0 tests executed       | Ensure `MainTest.kt` is in `src/test/java/com/example/` and has `@Test`       |
| Package name mismatch errors             | Ensure both `.kt` files start with `package com.example`                       |
| `rmdir` fails — "directory not empty"    | Use `rmdir /S /Q` (the `/S` flag removes subdirectories recursively)           |
| Old test results shown (tests skipped)   | Run `gradle clean test` to force re-execution                                  |
| Kotlin version conflict                  | If using a newer Gradle, change `version "1.8.21"` to a compatible version     |

---

**Program 3 Complete ✔**
