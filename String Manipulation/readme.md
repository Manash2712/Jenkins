**String manipulation** in Jenkins Pipelines is performed using Apache Groovy syntax, allowing you to dynamically modify, parse, and evaluate text data within your automation scripts.

Key String Manipulation Techniques in Jenkins
1. **Concatenation and Interpolation**
   Single Quotes ('...'): Define literal strings. They do not evaluate variables.
   
   Double Quotes ("..."): Define Groovy Strings (GStrings). They support variable interpolation using ${variable} syntax.

   Triple Quotes ('''...''' or """..."""): Define multi-line strings.
   
3. **Common Groovy String Methods**
   .split(String regex): Divides a string into a list based on a delimiter.
   
   .replace(char old, char new): Swaps target characters or sequences..contains(String sequence): Returns true if the substring exists.
   
   .substring(int start, int end): Extracts a specific portion of the string.
   
   .tokenize(String delimiter): Similar to split, but ignores empty tokens and works directly with characters.

### Top 5 Jenkins String Manipulation Interview Questions

**Q1: What is the difference between single quotes (') and double quotes (") in a Jenkins Pipeline?**

Answer: Single quotes create a standard Java String literal, meaning code like '${build_num}' prints the literal text without changes. 
Double quotes create a Groovy GString, which automatically evaluates expressions inside ${}.
```
def buildNum = 42
echo 'Build: ${buildNum}' // Outputs: Build: ${buildNum}
echo "Build: ${buildNum}" // Outputs: Build: 42
```

**Q2: How do you extract a Git branch name from env.BRANCH_NAME if it contains a path like feature/login-page?**

Answer: You can use the .split() method or a standard regular expression to isolate the target string segment.
```
def fullBranch = "feature/login-page"
// Using split and selecting the last element
def branchName = fullBranch.split('/')[-1] 
echo branchName // Outputs: login-page
```

**Q3: How do you check if a build log or an environment variable contains a specific keyword?**

Answer: You can utilize the .contains() method for straightforward matches, or the Groovy regex find operator (=~) for complex pattern matching.
```
def status = "Deployment status: SUCCESSFUL"
if (status.contains("SUCCESSFUL")) {
    echo "Deploy passed!"
}
```

**Q4: Why do you sometimes see an error like Scripts not permitted to use method java.lang.String ... in Jenkins?**

Answer: Jenkins uses a security mechanism called In-process Script Approval. Administrators restrict arbitrary method execution to prevent malicious scripts from compromising the master node.
Solution: An administrator must approve the specific string method in Manage Jenkins > In-process Script Approval, or the script must be run within a trusted Jenkins Shared Library.

**Q5: How do you safely remove leading/trailing whitespaces or newlines from shell output in a pipeline?**

Answer: When capturing shell output via returnStdout: true, Jenkins often appends a trailing newline. You can clean this up using the .trim() method.
```
def version = sh(script: "stdout-version-command", returnStdout: true).trim()
```
