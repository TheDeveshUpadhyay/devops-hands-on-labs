````markdown
# 🚀 Day 08: Jenkins Chained Builds

## 📌 Objective

Learn how to chain multiple Jenkins jobs so that one job automatically triggers another after successful completion.

---

# 📖 Scenario

In real-world DevOps projects, software delivery is usually divided into multiple independent jobs.

Instead of creating one large Jenkins job, different stages of the CI/CD process are separated.

For example:

- **Job 1 → Build** the application
- **Job 2 → Run Tests**
- **Job 3 → Deploy** the application

Rather than manually starting each job, Jenkins automatically triggers the next job after the previous one completes successfully.

This process is known as **Jenkins Chained Builds**.

---

# 🛠️ Prerequisites

- Jenkins Installed
- Git Installed
- Freestyle or Pipeline Jobs
- Jenkins Pipeline Plugin Installed

---

# 📁 Step 1: Create the Build Job

Navigate to:

```text
Dashboard
    ↓
New Item
```

Enter the job name:

```text
Build-Job
```

Select:

```text
Freestyle Project
```

Click:

```text
OK
```

---

## Configure the Build Job

Navigate to:

```text
Build Steps
```

Choose:

```text
Execute Shell
```

Add the following script:

```bash
echo "Building Application..."
sleep 5
echo "Build Completed"
```

Click **Save**.

---

# 📁 Step 2: Create the Test Job

Create another Freestyle Project.

Navigate to:

```text
Dashboard
    ↓
New Item
```

Enter:

```text
Test-Job
```

Select:

```text
Freestyle Project
```

Click **OK**.

Under **Build Steps → Execute Shell**, add:

```bash
echo "Running Unit Tests..."
sleep 5
echo "All Tests Passed"
```

Click **Save**.

---

# 📁 Step 3: Create the Deploy Job

Create one more Freestyle Project.

Enter:

```text
Deploy-Job
```

Select:

```text
Freestyle Project
```

Click **OK**.

Under **Build Steps → Execute Shell**, add:

```bash
echo "Deploying Application..."
sleep 5
echo "Deployment Successful"
```

Click **Save**.

---

# 🔗 Step 4: Chain Build → Test

Open:

```text
Build-Job
```

Navigate to:

```text
Configure
    ↓
Post-build Actions
```

Click:

```text
Add Post-build Action
```

Choose:

```text
Build other projects
```

Project to build:

```text
Test-Job
```

Trigger:

```text
Trigger only if build is stable
```

> Jenkins triggers the next job only if the current job completes successfully without errors.

Click **Save**.

---

# 🔗 Step 5: Chain Test → Deploy

Open:

```text
Test-Job
```

Navigate to:

```text
Configure
    ↓
Post-build Actions
```

Choose:

```text
Build other projects
```

Project:

```text
Deploy-Job
```

Trigger:

```text
Trigger only if build is stable
```

Click **Save**.

---

# ▶️ Step 6: Execute the Build Job

Go to:

```text
Dashboard
    ↓
Build-Job
```

Click:

```text
Build Now
```

---

# 🔄 Expected Workflow

```text
Build-Job
      │
      ▼
Test-Job
      │
      ▼
Deploy-Job
```

No manual intervention is required after starting the first job.

---

# 📊 Build History

After execution, verify each job.

| Job | Expected Status |
|------|-----------------|
| Build-Job | ✅ SUCCESS |
| Test-Job | ✅ SUCCESS |
| Deploy-Job | ✅ SUCCESS |

---

# 🛠️ Troubleshooting

If **Test-Job** does not start automatically, verify the following:

### Build-Job Configuration

- ✅ Post-build Action → **Build other projects**
- ✅ Project Name → **Test-Job**
- ✅ Trigger → **Trigger only if build is stable**

### Test-Job Configuration

- ✅ Project Name → **Deploy-Job**
- ✅ Trigger → **Trigger only if build is stable**

### Build Result

Ensure **Build-Job** finishes with:

```text
SUCCESS
```

A failed build will stop the chain.

---

# 🖥️ Console Output

## Build Job

```text
Building Application...

Build Completed

Triggering a new build of Test-Job

Finished: SUCCESS
```

---

## Test Job

```text
Running Unit Tests...

All Tests Passed

Triggering a new build of Deploy-Job

Finished: SUCCESS
```

---

## Deploy Job

```text
Deploying Application...

Deployment Successful

Finished: SUCCESS
```

---

# 🔄 Complete Pipeline Flow

```text
Developer
      │
      ▼
Push Code
      │
      ▼
Build Job
      │
      ▼
Test Job
      │
      ▼
Deploy Job
      │
      ▼
Production Server
```

---

# 🌍 Real Project Example

Suppose your organization develops a **Spring Boot** application.

Instead of creating one huge Jenkins job, the CI/CD pipeline is divided into multiple jobs.

## Build Job

- Compile Source Code
- Run Maven Build
- Create JAR File
- Archive Artifacts

⬇️ Automatically triggers

## Test Job

- Unit Testing
- Integration Testing
- SonarQube Analysis

⬇️ Automatically triggers

## Docker Job

- Build Docker Image
- Push Image to Docker Hub

⬇️ Automatically triggers

## Deploy Job

- Pull Latest Docker Image
- Restart Containers
- Verify Deployment

---

## Production Workflow

```text
Build
   │
   ▼
Test
   │
   ▼
Docker Build
   │
   ▼
Docker Push
   │
   ▼
Deploy
```

### Why use this approach?

- Easier troubleshooting
- Independent stages
- Faster debugging
- Better scalability
- Reusable jobs

---

# 🚀 Modern Alternative: Jenkins Pipeline

Today, most organizations use **Pipeline as Code** instead of multiple Freestyle jobs.

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building...'
            }
        }

        stage('Test') {
            steps {
                echo 'Testing...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying...'
            }
        }
    }
}
```

### Advantages of Pipelines

- Everything stored in a **Jenkinsfile**
- Version controlled with Git
- Easier maintenance
- Better visualization
- Supports complex CI/CD workflows

---

# ✅ Expected Outcome

- Build Job created successfully.
- Test Job created successfully.
- Deploy Job created successfully.
- Jobs execute automatically in sequence.
- No manual triggering required after the first job.

---

# 🎯 Interview Questions

## Q1. What are Jenkins Chained Builds?

**Answer:**

Jenkins Chained Builds automatically trigger downstream jobs after the successful completion of upstream jobs.

---

## Q2. Why are Chained Builds used?

**Answer:**

They automate sequential workflows such as:

```text
Build
   ↓
Test
   ↓
Deploy
```

without requiring manual intervention.

---

## Q3. Which post-build action is used to trigger another job?

**Answer**

```text
Build other projects
```

---

## Q4. What happens if the Build Job fails?

**Answer:**

The downstream jobs are **not triggered** because the chain continues only when the previous job finishes successfully.

---

## Q5. What is the modern alternative to Chained Builds?

**Answer:**

Declarative or Scripted **Jenkins Pipelines**, where the complete CI/CD workflow is defined in a single **Jenkinsfile**.

---

## Q6. Which approach is preferred in real projects?

**Answer:**

**Jenkins Pipelines** are preferred because they provide:

- Pipeline as Code
- Version Control
- Better Visualization
- Easier Maintenance
- Scalability

---

# 📌 Key Takeaways

- Jenkins Chained Builds automate sequential job execution.
- Downstream jobs run only after upstream jobs complete successfully.
- Chained Builds improve workflow automation and reduce manual effort.
- Splitting Build, Test, and Deploy into separate jobs simplifies troubleshooting.
- Modern DevOps teams typically prefer **Jenkins Pipelines** because they offer a single, version-controlled CI/CD workflow.

---

# 🎉 Conclusion

Today, I learned how **Jenkins Chained Builds** automate Build → Test → Deploy workflows by triggering downstream jobs automatically.

Although Chained Builds are still useful for understanding job orchestration, modern DevOps practices favor **Pipeline as Code** using a **Jenkinsfile**, which provides greater flexibility, maintainability, and scalability for enterprise CI/CD pipelines.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

Happy Learning! 🚀
````

