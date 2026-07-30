````markdown
# 🚀 Day 07: Jenkins Deployment Job

## 📌 Objective

Learn how to create a **Jenkins Deployment Job** that automatically deploys an application to a target server after a successful build.

---

# 📖 Scenario

In a real DevOps environment, once an application is built successfully, Jenkins automatically deploys the latest version to a target server such as:

- Amazon EC2
- Virtual Machine
- Kubernetes Cluster

Instead of manually logging into servers, copying files, or restarting services, Jenkins automates the entire deployment process.

---

# 🛠️ Prerequisites

- Jenkins Installed
- Git Installed
- SSH Access to Deployment Server
- Java or Docker Installed on Deployment Server (depending on the application)
- Jenkins Pipeline Plugin Installed

---

# 📁 Step 1: Create a Deployment Job

Navigate to:

```text
Dashboard
    ↓
New Item
```

Enter the job name:

```text
Deployment-Job
```

Select:

```text
Pipeline
```

Click:

```text
OK
```

---

# ⚙️ Step 2: Configure the Pipeline

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

        stage('Deploy') {
            steps {
                echo 'Deploying Application...'
            }
        }
    }

    post {
        success {
            echo 'Deployment Completed Successfully'
        }

        failure {
            echo 'Deployment Failed'
        }
    }
}
```

Click **Save**.

---

# ▶️ Step 3: Build the Pipeline

Click:

```text
Build Now
```

---

# 🖥️ Step 4: View Console Output

Navigate to:

```text
Build History
      ↓
Latest Build
      ↓
Console Output
```

Expected Output:

```text
Building Application...
Deploying Application...
Deployment Completed Successfully
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
Build Stage
    │
    ▼
Deploy Stage
    │
    ▼
Application Server
```

---

# 🌍 Real Project Deployment Example 1 – Docker

Many organizations deploy applications using Docker containers.

```groovy
stage('Deploy') {
    steps {
        sh '''
        ssh ubuntu@<Server-IP> "
        cd /opt/application
        docker compose pull
        docker compose up -d
        "
        '''
    }
}
```

### Deployment Workflow

```text
SSH Login
    ↓
Navigate to Project Directory
    ↓
Pull Latest Docker Images
    ↓
Restart Containers
```

**Explanation**

- Connects to the remote server using SSH.
- Navigates to the application directory.
- Pulls the latest Docker images.
- Restarts containers using Docker Compose.

---

# ☕ Real Project Deployment Example 2 – Java Application

```groovy
stage('Deploy') {
    steps {
        sh '''
        scp target/app.jar ubuntu@<Server-IP>:/opt/application/

        ssh ubuntu@<Server-IP> "
        systemctl restart springboot-app
        "
        '''
    }
}
```

### Deployment Workflow

```text
Copy JAR File
      ↓
Login to Server
      ↓
Restart Spring Boot Service
```

**Explanation**

- Copies the latest JAR file to the deployment server using **SCP**.
- Connects to the remote server using **SSH**.
- Restarts the application service.

---

# ☸️ Real Project Deployment Example 3 – Kubernetes

```groovy
stage('Deploy') {
    steps {
        sh '''
        kubectl apply -f deployment.yaml
        '''
    }
}
```

### Deployment Workflow

```text
Build Image
      ↓
Push Image
      ↓
kubectl apply
      ↓
Rolling Update
```

**Explanation**

Jenkins updates the Kubernetes Deployment using the latest deployment manifest.

---

# 🏗️ Real Project Deployment Example 4 – Terraform

Infrastructure deployment can also be automated using Jenkins.

```groovy
stage('Deploy Infrastructure') {
    steps {
        sh '''
        terraform init
        terraform apply -auto-approve
        '''
    }
}
```

### Deployment Workflow

```text
Initialize Terraform
      ↓
Validate Configuration
      ↓
Provision AWS Infrastructure
```

**Explanation**

Jenkins automatically provisions or updates cloud infrastructure using Terraform.

---

# ✅ Expected Outcome

- Deployment Job created successfully.
- Build stage executed successfully.
- Deploy stage executed successfully.
- Application deployed automatically.
- Manual deployment eliminated.

---

# 💡 Benefits of Deployment Automation

- Faster software delivery
- Reduced manual effort
- Consistent deployments
- Fewer human errors
- Easier rollback strategy
- Supports Continuous Delivery (CD)

---

# 🎯 Interview Questions

## Q1. What is a Deployment Job?

**Answer:**

A Deployment Job is a Jenkins job that automatically deploys an application to a target environment after a successful build.

---

## Q2. Why do we automate deployment?

**Answer:**

Deployment automation:

- Reduces manual effort
- Minimizes human errors
- Ensures consistent deployments
- Speeds up software delivery

---

## Q3. How does Jenkins deploy applications?

**Answer:**

Jenkins can deploy applications using:

- SSH
- SCP
- Docker
- Kubernetes
- Ansible
- Terraform
- Cloud deployment services

---

## Q4. What are common deployment targets?

- Amazon EC2
- Virtual Machines
- Docker Hosts
- Kubernetes Clusters
- AWS ECS
- AWS EKS

---

## Q5. Which command is used to copy files to a remote server?

**Answer**

```bash
scp
```

---

## Q6. Which command is commonly used to connect to a remote server?

**Answer**

```bash
ssh
```

---

# 📌 Key Takeaways

- Jenkins Deployment Jobs automate application deployment after successful builds.
- Deployment can be performed using SSH, SCP, Docker, Kubernetes, Terraform, Ansible, or cloud-native deployment services.
- Automated deployments improve consistency, reliability, and delivery speed.
- Deployment Jobs are a critical part of modern CI/CD pipelines.
- In production environments, deployment jobs are often combined with **Conditional Pipelines**, **Approval Gates**, and **Rollback Strategies** to ensure safe and controlled releases.

---

# 🎉 Conclusion

Today, I learned how to create a **Jenkins Deployment Job** to automate application deployment after a successful build.

Whether deploying a **Java application**, **Docker containers**, **Kubernetes workloads**, or **AWS infrastructure with Terraform**, Jenkins provides a reliable and scalable way to automate deployments, making CI/CD pipelines faster, safer, and more efficient.

---excellent

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

Happy Learning! 🚀
````

