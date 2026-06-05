
## Core Jenkins Topics Covered in the Script

Here are the four distinct Jenkins concepts implemented in the file:

### 1. Build Customisation (`currentBuild` Globals)
* **What it is**: Modifying internal run properties during execution.
* **Code segment**: `currentBuild.displayName = "online-shopping-#"+currentBuild.number`
* **Purpose**: Replaces the default build number prefix (e.g., `#12`) in the Jenkins UI with a friendly name (`online-shopping-#12`) for tracking releases easily.

### 2. Global Tool Configuration (`tools` block)
* **What it is**: Injecting managed build tools directly into the execution path.
* **Code segment**: `tools { maven 'maven3' }`
* **Purpose**: Automatically downloads or links pre-configured Apache Maven versions (named `'maven3'` in the global configurations) so the `mvn` command works natively in shell scripts.

### 3. Secure Credential Management (`sshagent` plugin)
* **What it is**: Injecting SSH keys without exposing secrets or hardcoding passwords.
* **Code segment**: `sshagent(['dev-server']) { ... }`
* **Purpose**: Starts an SSH agent containing the private key tied to the credential ID `'dev-server'`, enabling secure, passwordless `scp` and `ssh` remote commands.

### 4. Automated Deployment & Remote Executions (`sh` block orchestration)
* **What it is**: Combining file transfers with remote terminal execution blocks.
* **Code segment**: `scp -o StrictHostKeyChecking=no ...` and `ssh ... sudo systemctl restart tomcat9`
* **Purpose**: Copies the build artifact (`myweb.war`) to an isolated AWS EC2 internal IP address, bypassing unknown host prompts (`StrictHostKeyChecking=no`), and restarts Apache Tomcat to reflect modifications.

---

## Topic-Specific Interview Questions & Answers

### Topic 1: Build Customisation & `currentBuild`

#### Q1: Why would you modify `currentBuild.displayName` or `currentBuild.description` in a pipeline?
* **Answer**: By default, Jenkins builds show arbitrary integers like `#1`, `#2`. Modifying `displayName` allows teams to inject contextual variables, like a Git release tag, pull request ID, or application name, making it easier to scan the build history table.

#### Q2: Where should global properties like `currentBuild.displayName` be declared in a Declarative Pipeline?
* **Answer**: They should strictly be declared inside a `script {}` block within a stage, or inside an explicit `options {}` directive block. Placing it completely outside the `pipeline {}` wrapper block (as done in the snippet) defaults back to Scripted Groovy syntax parsing, which can cause validation issues depending on your Jenkins version.

### Topic 2: Tools Directive

#### Q3: What prerequisites must be met before using `maven 'maven3'` inside a Pipeline script?
* **Answer**: An administrator must first register a Maven installation under **Manage Jenkins** ➔ **Tools** (Global Tool Configuration) and explicitly name it exactly `'maven3'`. If the names do not match, the pipeline fails with a missing tool definition exception.

#### Q4: How does the `tools` directive affect the environment variables of a build execution workspace?
* **Answer**: Jenkins automatically modifies the system executable `PATH` variable for that specific run, appending the exact `/bin` directory of the defined tool, while injecting specific tracking variables like `M2_HOME` or `MAVEN_HOME`.

### Topic 3: SSH Agent & Secure Credentials

#### Q5: What is the advantage of using `sshagent(['id'])` over storing a static password variable?
* **Answer**: The `sshagent` plugin utilizes an in-memory Unix socket to provision private keys securely. Private keys are never written to disk or exposed inside logs, and the active session automatically gets killed and cleaned up once the wrapper block finishes execution.

#### Q6: Why is `-o StrictHostKeyChecking=no` used during the `scp` command, and what is its security downside?
* **Answer**: It prevents the build script from hanging and failing when waiting for an interactive console prompt to accept the target host's signature fingerprint. The downside is that it leaves the connection vulnerable to Man-In-The-Middle (MITM) attacks. A safer approach is to pre-populate the `known_hosts` file on the Jenkins worker nodes.

### Topic 4: Artifact Management & Deployment Strategy

#### Q7: Your pipeline runs `mv target/*war target/myweb.war`. Why is normalizing artifact filenames important?
* **Answer**: Maven builds dynamically append version numbers to output files (e.g., `myweb-1.0.0-SNAPSHOT.war`). If copied directly into Tomcat's `/webapps/` folder, the context root path of the application changes with every release. Renaming it to a static name ensures the application is always reached at the identical URI endpoint (e.g., `http://<IP>/myweb/`).

#### Q8: If the `tomcat9` restart command fails on the target machine, will the Jenkins build fail?
* **Answer**: Yes. Because both commands are chained inside a triple-quoted shell script (`""" ... """`), any non-zero exit status returned by the target SSH agent execution bubbles up to Jenkins, marking the entire `Deploy-Dev` stage as a failure.
