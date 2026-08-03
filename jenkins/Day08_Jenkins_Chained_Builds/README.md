# 🚀 Day 08: Jenkins Chained Builds

## 📖 Overview

This project demonstrates how to configure **Jenkins Chained Builds**, where multiple Jenkins jobs are linked together to create an automated CI/CD workflow. Each job performs a specific task and automatically triggers the next job upon successful completion.

This approach helps break complex workflows into smaller, manageable jobs, making automation easier to understand and maintain.

---

## 🎯 Objective

Create a Jenkins Chained Build workflow that performs the following sequence:

- Build Application
- Run Unit Tests
- Deploy Application

The goal is to eliminate manual intervention by automatically triggering downstream jobs after successful execution.

---

## 🌍 Real-World Scenario

In enterprise DevOps environments, software delivery is often divided into separate Jenkins jobs instead of using one large job.

A typical workflow looks like:

```text
Build
   │
   ▼
Test
   │
   ▼
Deploy
```

After the Build job completes successfully, Jenkins automatically starts the Test job. Once testing succeeds, the Deploy job begins without any manual intervention.

This workflow is known as **Jenkins Chained Builds**.

---

## 🛠 Prerequisites

- Jenkins Installed
- Git Installed
- Freestyle Projects
- Jenkins Pipeline Plugin (Recommended)

---

# 🚀 Implementation

## Step 1: Create the Build Job

Navigate to:

```text
Dashboard
   └── New Item
```

Create a new **Freestyle Project** named:

```text
Build-Job
```

Under **Build Steps**, select:

```text
Execute Shell
```

Add the following script:

```bash
echo "Building Application..."
sleep 5
echo "Build Completed"
```

Save the job.

---

## Step 2: Create the Test Job

Create another Freestyle Project named:

```text
Test-Job
```

Under **Build Steps**, add:

```bash
echo "Running Unit Tests..."
sleep 5
echo "All Tests Passed"
```

Save the job.

---

## Step 3: Create the Deploy Job

Create another Freestyle Project named:

```text
Deploy-Job
```

Under **Build Steps**, add:

```bash
echo "Deploying Application..."
sleep 5
echo "Deployment Successful"
```

Save the job.

---

## Step 4: Configure Build → Test Trigger

Open:

```text
Build-Job
```

Navigate to:

```text
Configure
   └── Post-build Actions
```

Choose:

```text
Build other projects
```

Specify:

```text
Project:
Test-Job
```

Trigger:

```text
Trigger only if build is stable
```

Save the configuration.

---

## Step 5: Configure Test → Deploy Trigger

Open:

```text
Test-Job
```

Navigate to:

```text
Configure
   └── Post-build Actions
```

Choose:

```text
Build other projects
```

Specify:

```text
Deploy-Job
```

Trigger:

```text
Trigger only if build is stable
```

Save the configuration.

---

## Step 6: Execute the Workflow

Start the process by running:

```text
Build-Job
```

Jenkins automatically executes the remaining jobs.

---

## 🔄 Workflow Diagram

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

# 📊 Expected Results

| Job | Expected Status |
|------|-----------------|
| Build-Job | ✅ SUCCESS |
| Test-Job | ✅ SUCCESS |
| Deploy-Job | ✅ SUCCESS |

---

# 🖥 Console Output

### Build Job

```text
Building Application...

Build Completed

Triggering Test-Job

Finished: SUCCESS
```

### Test Job

```text
Running Unit Tests...

All Tests Passed

Triggering Deploy-Job

Finished: SUCCESS
```

### Deploy Job

```text
Deploying Application...

Deployment Successful

Finished: SUCCESS
```

---

# 🌍 Enterprise Workflow Example

A typical production CI/CD workflow may include:

### Build Job

- Compile Source Code
- Maven Build
- Generate Artifacts

↓

### Test Job

- Unit Testing
- Integration Testing
- SonarQube Analysis

↓

### Docker Job

- Build Docker Image
- Push Image to Docker Hub

↓

### Deploy Job

- Pull Latest Image
- Restart Containers
- Verify Deployment

---

## Production Flow

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

### Benefits

- Independent jobs
- Easier troubleshooting
- Better scalability
- Reusable workflows
- Reduced manual effort

---

# 🚀 Modern Alternative

Most organizations now use **Jenkins Pipelines (Pipeline as Code)** instead of multiple Freestyle jobs.

Example:

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running Tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying Application...'
            }
        }
    }
}
```

### Advantages

- Pipeline as Code
- Stored in Git
- Easier maintenance
- Better visualization
- Supports complex CI/CD workflows

---

# 🎯 Interview Questions

### Q1. What are Jenkins Chained Builds?

Jenkins Chained Builds automatically trigger downstream jobs after the successful completion of upstream jobs.

---

### Q2. Why are Chained Builds used?

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

### Q3. Which post-build action is used to trigger another job?

```text
Build other projects
```

---

### Q4. What happens if the Build Job fails?

The downstream jobs are not triggered because Jenkins only executes the next job after a successful build.

---

### Q5. What is the modern alternative to Chained Builds?

Declarative or Scripted Jenkins Pipelines defined in a **Jenkinsfile**.

---

### Q6. Which approach is preferred in enterprise projects?

Jenkins Pipelines are preferred because they provide:

- Pipeline as Code
- Version Control
- Better Visualization
- Easier Maintenance
- Scalability

---

# 📌 Key Takeaways

- Jenkins Chained Builds automate sequential job execution.
- Each job has a single responsibility.
- Downstream jobs execute only after successful completion.
- Chained Builds improve automation and reduce manual work.
- Modern DevOps teams generally prefer Jenkins Pipelines for better maintainability and version control.

---

# 🎉 Conclusion

In this lab, I implemented **Jenkins Chained Builds** to automate a simple Build → Test → Deploy workflow.

This exercise demonstrates how Jenkins can orchestrate multiple jobs to create a structured CI/CD process. While Chained Builds are valuable for understanding job orchestration, modern DevOps practices typically use **Pipeline as Code** with Jenkinsfiles for greater flexibility, scalability, and maintainability.

---

## ⭐ Support

If you found this project helpful, consider giving this repository a **⭐ Star** and following my **100 Days of DevOps** journey.

Happy Learning! 🚀
