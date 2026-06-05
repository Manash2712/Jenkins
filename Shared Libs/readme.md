# Jenkins Pipeline Analysis: Shared Libraries and Modular Architecture

This document breaks down the advanced modular concepts used in the provided `Jenkinsfile` and compiles relevant interview preparation materials for Jenkins Shared Libraries.

---

## Core Jenkins Topics Covered in the Script

Here are the three distinct Jenkins concepts implemented in the file:

### 1. Jenkins Shared Libraries (`@Library` annotation)
* **What it is**: A mechanism to share reusable Groovy code across different Jenkins pipelines to reduce code duplication.
* **Code segment**: `@Library('jenkins-shared-library-demo@main') _`
* **Purpose**: Pulls a specific branch (`main`) of a pre-configured library repository named `jenkins-shared-library-demo`. The underscore (`_`) imports all its functions globally into the script's classpath.

### 2. Global Variables / Custom Steps (`welcome('Manash')`)
* **What it is**: Executing custom steps defined in the `vars/` directory of a Shared Library.
* **Code segment**: `welcome('Manash')`
* **Purpose**: Invokes a custom script (likely named `welcome.groovy`) from the library. It abstracts repetitive tasks, like printing complex greeting formats or initializing standard environment alerts, behind a single keyword.

### 3. Shared Library Script Object Instantiation (`calculator` methods)
* **What it is**: Calling structured Object/Class methods inside a pipeline stage.
* **Code segment**: `calculator.add(20,50)` and `calculator.mulltiplication(25,25)`
* **Purpose**: Calls object-oriented utility files (likely structured under `src/` or `vars/` as a static map) to compute business logic arithmetic outside of standard shell executions.

---

## Topic-Specific Interview Questions & Answers

### Topic 1: Jenkins Shared Libraries Configuration

#### Q1: What does the underscore (`_`) mean at the end of the `@Library` declaration statement?
* **Answer**: The underscore is a shortcut notation in Groovy that tells Jenkins to immediately import and load all global variables and steps defined within that library into the script's namespace. Without it, you would have to manually use Java-style `import` statements for specific classes.

#### Q2: How do you configure a Shared Library name like `jenkins-shared-library-demo` inside Jenkins?
* **Answer**: An administrator must navigate to **Manage Jenkins** ➔ **System** (Global Pipeline Libraries). There, they register the library name, provide its default version/branch, and hook it up to a source control repository (like Git or GitHub) using valid access credentials.

### Topic 2: Custom Steps (`vars/` folder layout)

#### Q3: How must the backend files be structured inside the repository for the `welcome('Manash')` step to function?
* **Answer**: The file must be placed in a directory named `vars/` at the root of the library repo, named exactly `welcome.groovy`. Inside that file, it must implement a public `call` method, which receives the string parameter.
* **Example Structure**:
  ```groovy
  // vars/welcome.groovy
  def call(String name) {
      echo "Welcome to the automation pipeline, \${name}!"
  }
  ```

#### Q4: Can you use declarative pipeline directives (like `sh` or `git`) inside a custom shared library step script?
* **Answer**: Yes. Custom steps inside the `vars/` directory have direct access to the standard Jenkins workflow steps DSL. You can call commands like `sh`, `error`, or `archiveArtifacts` just like you would inside a standard `Jenkinsfile`.

### Topic 3: Object/Class Utilities (`calculator`)

#### Q5: What is the main structural difference between placing library scripts inside the `vars/` folder versus the `src/` folder?
* **Answer**: 
  * The `vars/` folder holds global variable scripts that act as single, standalone pipeline steps (like custom keywords). 
  * The `src/` folder houses standard Object-Oriented Groovy classes (using packages like `com.company.utils`). Code in `src/` needs to be initialized using `new` operators or static class methods, which is perfect for complex computing data models.

#### Q6: Why must `calculator.add(20,50)` be placed inside a `script {}` block?
* **Answer**: The `add()` method is dynamic Groovy script execution logic. Declarative pipelines strictly allow only explicit native steps (like `sh`, `echo`, `git`) inside a `steps {}` block. To run direct variable manipulation or object-oriented method calls, you must open a `script {}` escape hatch block.
