# Jenkins Pipeline Analysis: Scripted Pipelines and Parameterized Builds

This document breaks down the concepts used in the provided Scripted `Jenkinsfile` and compiles relevant interview preparation materials for Parameterized Builds and Dynamic SCM.

---

## Core Jenkins Topics Covered in the Script

Here are the three distinct Jenkins concepts implemented in the file:

### 1. Scripted Pipeline Syntax (`node` Block)
* **What it is**: The original, imperative scripting model for Jenkins pipelines based on a Groovy engine wrapper.
* **Code segment**: `node { stage('SCM Checkout') { ... } }`
* **Purpose**: Allocates an available executor node on the Jenkins controller or an attached agent to run the specific steps written inside its block sequentially.

### 2. Parameterized Builds (`properties` Block)
* **What it is**: A mechanism to accept user-defined inputs before a job execution begins.
* **Code segment**: `properties([parameters([choice(choices: [...], name: 'branch')])])`
* **Purpose**: Programmatically injects a dropdown parameter option inside the Jenkins UI named `branch`, forcing the user or an external webhook to explicitly select a target environment branch (`master`, `feature-1`, or `feature-2`).

### 3. Dynamic Parameter Interpolation (`params` Object)
* **What it is**: Accessing user inputs via runtime object maps to drive pipeline variables.
* **Code segment**: `branch: "${params.branch}"`
* **Purpose**: Reads the selected UI dropdown value from the execution context. This dynamically switches the Git checkout target without needing to hardcode separate jobs for each branch.

---

## Topic-Specific Interview Questions & Answers

### Topic 1: Scripted Pipelines vs. Declarative Pipelines

#### Q1: What is the main structural difference between the `node` block used here and a standard `pipeline` block?
* **Answer**: The `node` block signifies a **Scripted Pipeline**, which provides an imperative environment. You can use native Groovy control structures (like `if-else` blocks or loops) anywhere. The `pipeline` block signifies a **Declarative Pipeline**, which enforces a strict, pre-parsed structure (`stages`, `steps`, `agent`) designed to prevent complex coding inside the UI.

#### Q2: Why are Scripted pipelines sometimes preferred over Declarative syntax despite being older?
* **Answer**: Scripted pipelines offer complete flexibility for complex workflows. If your pipeline requires dynamic stage generation based on an API payload, heavy programming logic, or complex error-handling catch loops that go beyond standard `post` rules, Scripted syntax can handle it natively.

### Topic 2: Programmatic Pipeline Parameters

#### Q3: What happens the very first time you run a pipeline that defines its parameters via a `properties` block code?
* **Answer**: The first execution will ignore the choices and may run with defaults or fail, because Jenkins must parse the script first to register the parameters into the database. Once that first run finishes, the "Build Now" button in the Jenkins UI updates to **"Build with Parameters"** for all subsequent runs.

#### Q4: How can you access parameters inside a script, and what is the best practice for doing so?
* **Answer**: You access parameters via the global `params` object map (e.g., `params.VARIABLE_NAME`). While you can also access them directly as regular environment variables (`env.VARIABLE_NAME`), using `params` explicitly signals to anyone reading the code that the value came from user input rather than an internal system configuration.

### Topic 3: Dynamic SCM / SCM Checkout

#### Q5: What is the risk of using variable interpolation like `"${params.branch}"` inside the `git` checkout step?
* **Answer**: If the parameters are not properly validated or restricted to specific choices, string interpolation can open up a vector for pipeline injection or unexpected checkout failures if a user types an invalid or malformed branch string. Using a `choice` parameter type mitigates this by locking inputs down to safe defaults.

#### Q6: How does using a parameter for the branch selection help save resources on your Jenkins controller?
* **Answer**: Instead of creating three separate Jenkins jobs for `master`, `feature-1`, and `feature-2`—which duplicates configurations and fills up your dashboard—you maintain a **single single-source-of-truth job** that dynamically handles code routing based on runtime input.
