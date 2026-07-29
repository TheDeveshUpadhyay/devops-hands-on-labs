# 🚀 Day 77: Jenkins Deploy Pipeline

## 🎯 Objective

Learn how to build a Jenkins Pipeline that automates application deployment after a successful build.

---

# 📌 Scenario

In a real DevOps environment, deployment is automated through Jenkins Pipelines. After the application is built successfully, Jenkins deploys it automatically to a target server, reducing manual effort and ensuring faster, consistent releases.

---

# ✅ Prerequisites

- Jenkins Installed
- Jenkins Agent Configured (Optional - from Day 75)
- Git Installed
- Pipeline Plugin Installed
- SSH Access to Deployment Server

---

# 📝 Step 1: Create a Pipeline Job

Navigate to:

```
New Item
```

Enter:

```
Deploy-Pipeline
```

Select:

```
Pipeline
```

Click:

```
OK
```

---

# 📝 Step 2: Configure the Pipeline

Scroll to:

```
Pipeline
```

Definition:

```
Pipeline Script
```

Paste the following Jenkins Pipeline:

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

    post {

        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Build Failed'
        }
    }
}
```

Click:

```
Save
```

---

# 📝 Step 3: Build the Pipeline

Click:

```
Build Now
```

---

# 📝 Step 4: View Stage View

After the build completes successfully, Jenkins displays:

```
✔ Build

✔ Test

✔ Deploy
```

---

# 📝 Step 5: View Console Output

Navigate to:

```
Build History
      ↓
Latest Build
      ↓
Console Output
```

Expected Output:

```text
Building Application...
Running Tests...
Deploying Application...
Deployment Successful
Finished: SUCCESS
```

---

# 🔄 Pipeline Flow

```text
Developer
     │
     ▼
 Push Code
     │
     ▼
 Jenkins Pipeline
     │
     ▼
   Build
     │
     ▼
    Test
     │
     ▼
   Deploy
     │
     ▼
Application Server
```

---

# 🏢 Real Project Deployment Example

In production, the **Deploy** stage does much more than printing a message. It typically connects to a remote server using SSH and deploys the latest application.

## Example 1: Docker Deployment

```groovy
stage('Deploy') {
    steps {
        sh '''
        ssh ubuntu@<Server-IP> "
            cd /opt/app &&
            docker compose pull &&
            docker compose up -d
        "
        '''
    }
}
```

---

## Example 2: Java Application Deployment

```groovy
stage('Deploy') {
    steps {
        sh '''
        scp target/app.jar ubuntu@<Server-IP>:/opt/app/
        ssh ubuntu@<Server-IP> "systemctl restart myapp"
        '''
    }
}
```

---

# ✅ Expected Outcome

- Jenkins Pipeline created successfully.
- Build stage executed successfully.
- Test stage executed successfully.
- Deploy stage executed successfully.
- Pipeline completed with **SUCCESS** status.

---

# 🎤 Interview Questions

## Q1. What is a Jenkins Pipeline?

**Answer:**

A Jenkins Pipeline is a collection of automated stages defined as code that builds, tests, and deploys an application using a **Jenkinsfile**.

---

## Q2. What are the two types of Jenkins Pipelines?

**Answer:**

- Declarative Pipeline
- Scripted Pipeline

---

## Q3. Difference between Freestyle Job and Pipeline Job

| Freestyle Job | Pipeline Job |
|---------------|--------------|
| GUI-based configuration | Pipeline as Code (Jenkinsfile) |
| Limited flexibility | Highly flexible |
| Difficult to version control | Stored in Git and version controlled |

---

## Q4. What is a Stage in Jenkins Pipeline?

**Answer:**

A stage is a logical phase of a pipeline such as **Build**, **Test**, or **Deploy**, used to organize and visualize the CI/CD workflow.

---

## Q5. What is the purpose of `agent any`?

**Answer:**

`agent any` instructs Jenkins to execute the pipeline on any available Jenkins Agent or on the Jenkins Controller if no agent is available.

---

## Q6. Why are Jenkins Pipelines preferred in real projects?

**Answer:**

Because they support:

- Pipeline as Code
- Version Control
- Automation
- Reusable Stages
- Better Visualization
- Seamless integration with Git, Docker, Kubernetes, and Cloud Platforms

---

# 📚 Jenkins Pipeline Terminology

## Pipeline

The complete CI/CD workflow written as code.

```groovy
pipeline {

}
```

---

## Agent

Specifies **where** the pipeline will run.

```groovy
agent any
```

Example:

- Jenkins Controller
- Jenkins Agent

---

## Stages

A collection of all stages.

```groovy
stages {

}
```

---

## Stage

Represents one logical phase of the pipeline.

Examples:

- Build
- Test
- Deploy

```groovy
stage('Build') {

}
```

---

## Steps

Actual commands executed inside a stage.

```groovy
steps {
    echo 'Building Application...'
}
```

Examples:

```groovy
sh 'mvn clean package'
```

```groovy
git 'https://github.com/user/project.git'
```

---

## Post

Runs after all stages have completed.

```groovy
post {

}
```

Common blocks:

```groovy
success {

}
```

Runs only when the pipeline succeeds.

```groovy
failure {

}
```

Runs only when the pipeline fails.

---

# 🧠 Easy Way to Remember

| Keyword | Meaning |
|----------|---------|
| Pipeline | Complete CI/CD Workflow |
| Agent | Machine where the pipeline runs |
| Stages | Collection of all phases |
| Stage | One phase (Build, Test, Deploy) |
| Steps | Commands executed inside a stage |
| Post | Actions performed after pipeline completion |

---

# 🏆 Mini Hackathon

### Task 1

Create a Jenkins Pipeline with three stages:

- Build
- Test
- Deploy

---

### Task 2

Create a Pipeline with four stages:

- Checkout
- Build
- Test
- Deploy

---

### Task 3

Print a different message in every stage using `echo`.

---

### Task 4

Which directive specifies where the pipeline runs?

**Answer:**

```groovy
agent any
```

---

### Task 5

Which file stores the Pipeline code in real projects?

**Answer:**

```
Jenkinsfile
```

---

# 🎯 Key Takeaways

- Jenkins Pipeline automates Build, Test, and Deployment.
- Pipelines are written as code using a **Jenkinsfile**.
- `agent` specifies where the pipeline runs.
- `stages` organize the workflow into logical phases.
- `steps` execute the actual commands.
- `post` performs actions after pipeline execution.
- Jenkins Pipelines are the preferred approach for implementing CI/CD in modern DevOps environments.
