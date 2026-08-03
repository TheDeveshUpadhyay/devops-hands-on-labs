# 🚀 Day 05: Jenkins Deploy Pipeline

## 📖 Overview

This project demonstrates how to create a **Jenkins Declarative Pipeline** that automates the software deployment process. The pipeline is divided into multiple stages—**Build**, **Test**, and **Deploy**—to provide a structured, reliable, and repeatable CI/CD workflow.

---

## 🎯 Objective

Learn how to create a Jenkins Pipeline that:

- Builds an application
- Executes automated tests
- Deploys the application automatically
- Provides deployment status after execution

---

## 🌍 Real-World Scenario

In modern DevOps practices, software deployment is fully automated using Jenkins Pipelines.

After developers push code to a Git repository:

- Jenkins builds the application.
- Automated tests validate the code.
- The application is deployed to the target environment if all previous stages succeed.

This automation reduces manual effort, minimizes deployment errors, and ensures consistent software delivery.

---

## 🛠 Prerequisites

- Jenkins Installed
- Git Installed
- Pipeline Plugin Installed
- SSH Access to Deployment Server
- Jenkins Agent (Optional)

---

# 🚀 Implementation

## Step 1: Create a Pipeline Job

Navigate to:

```text
Dashboard
    ↓
New Item
```

Enter the job name:

```text
Deploy-Pipeline
```

Select:

```text
Pipeline
```

Click **OK**.

---

## Step 2: Configure the Pipeline

Navigate to:

```text
Pipeline
```

Definition:

```text
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

Click **Save**.

---

## Step 3: Execute the Pipeline

Click:

```text
Build Now
```

---

## Step 4: View Stage View

After successful execution, Jenkins displays:

```text
✔ Build
✔ Test
✔ Deploy
```

Each completed stage is highlighted in the Stage View.

---

## Step 5: View Console Output

Navigate to:

```text
Build History
      ↓
Latest Build
      ↓
Console Output
```

Expected output:

```text
Building Application...

Running Tests...

Deploying Application...

Deployment Successful

Finished: SUCCESS
```

---

# 🔄 Pipeline Workflow

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

# 💼 Real Project Deployment Example

In production environments, the Deploy stage connects to a remote server using SSH.

### Example: Docker Deployment

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

### Deployment Workflow

```text
SSH Login
     │
     ▼
Navigate to Application Directory
     │
     ▼
Pull Latest Docker Images
     │
     ▼
Restart Containers
```

---

## Java Application Deployment

Instead of Docker, Jenkins can deploy Java applications.

```groovy
stage('Deploy') {
    steps {
        sh '''
        scp target/app.jar ubuntu@<Server-IP>:/opt/app/

        ssh ubuntu@<Server-IP> "
        systemctl restart myapp
        "
        '''
    }
}
```

### Deployment Workflow

```text
Build JAR
     │
     ▼
Copy JAR using SCP
     │
     ▼
Connect via SSH
     │
     ▼
Restart Application Service
```

---

# 🚀 Why Use Jenkins Pipelines?

Compared to traditional Freestyle Jobs, Jenkins Pipelines provide:

- Pipeline as Code
- Version Control using Git
- Better Visualization
- Easier Maintenance
- Reusable Stages
- Improved Scalability
- Integration with Docker, Kubernetes, Terraform, and Cloud Platforms

---

# ✅ Expected Outcome

- Jenkins Pipeline created successfully.
- Build stage executed successfully.
- Test stage executed successfully.
- Deploy stage executed successfully.
- Application deployment completed successfully.

---

# 🎯 Interview Questions

### Q1. What is a Jenkins Pipeline?

**Answer:**

A Jenkins Pipeline is a collection of automated stages defined as code (Jenkinsfile) that build, test, and deploy an application.

---

### Q2. What are the two types of Jenkins Pipelines?

**Answer:**

- Declarative Pipeline
- Scripted Pipeline

---

### Q3. What is the difference between Freestyle Jobs and Pipeline Jobs?

| Freestyle Job | Pipeline Job |
|---------------|--------------|
| GUI-based configuration | Pipeline as Code (Jenkinsfile) |
| Limited flexibility | Highly flexible |
| Difficult to version control | Stored in Git and version controlled |
| Less reusable | Modular and reusable |

---

### Q4. What is a Stage in Jenkins Pipeline?

**Answer:**

A stage is a logical phase of the CI/CD workflow, such as **Build**, **Test**, or **Deploy**, that improves organization and visualization.

---

### Q5. What is the purpose of `agent any`?

**Answer:**

`agent any` instructs Jenkins to execute the pipeline on any available Jenkins agent or on the controller if no agent is available.

---

### Q6. Why are Jenkins Pipelines preferred in real projects?

**Answer:**

Because they provide:

- Pipeline as Code
- Version Control
- Better Visualization
- Automation
- Reusable Stages
- Easy Integration with Git, Docker, Kubernetes, Terraform, and Cloud Services

---

# 📌 Key Takeaways

- Jenkins Pipelines automate the complete CI/CD workflow.
- Pipelines divide software delivery into logical stages.
- Declarative Pipelines improve readability and maintainability.
- Automated deployments reduce manual effort and deployment failures.
- Jenkins Pipelines are widely adopted in enterprise DevOps environments.

---

# 🎉 Conclusion

Today, I learned how to create a **Jenkins Deploy Pipeline** that automates the **Build**, **Test**, and **Deploy** stages of a CI/CD workflow.

By defining the deployment process as code using a **Jenkinsfile**, software delivery becomes more reliable, consistent, and scalable. This approach is widely used in modern DevOps environments to streamline application deployments across on-premises and cloud platforms.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and following my DevOps learning journey.

Happy Learning! 🚀
