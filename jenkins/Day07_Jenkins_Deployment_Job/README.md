# 🚀 Day 07: Jenkins Deployment Job

## 📖 Overview

This project demonstrates how to create a **Jenkins Deployment Job** that automates application deployment after a successful build. Instead of manually copying files or logging into servers, Jenkins executes deployment tasks automatically, enabling faster, consistent, and reliable software releases.

---

## 🎯 Objective

Create a Jenkins Deployment Job that:

- Builds the application
- Deploys the application automatically
- Simulates a real-world Continuous Delivery (CD) workflow

---

## 🌍 Real-World Scenario

In modern DevOps environments, deployments are fully automated as part of the CI/CD pipeline. Once the application is built successfully, Jenkins deploys the latest version to the target environment.

Common deployment targets include:

- Amazon EC2
- Virtual Machines
- Docker Hosts
- Kubernetes Clusters

Automating deployments eliminates manual intervention, reduces human error, and accelerates software delivery.

---

## 🛠 Prerequisites

- Jenkins Installed
- Git Installed
- SSH Access to Target Server
- Java or Docker Installed on Target Server
- Jenkins Pipeline Plugin

---

# 🚀 Implementation

## Step 1: Create a Deployment Pipeline

Navigate to:

```text
Dashboard
   └── New Item
```

Create a new Pipeline named:

```text
Deployment-Job
```

Click **OK**.

---

## Step 2: Configure the Pipeline

Navigate to:

```text
Pipeline
```

Choose:

```text
Definition → Pipeline Script
```

Paste the following pipeline:

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

Save the pipeline configuration.

---

## Step 3: Execute the Pipeline

Run:

```text
Build Now
```

---

## Step 4: Verify Console Output

Navigate to:

```text
Build History
   └── Latest Build
          └── Console Output
```

Expected output:

```text
Building Application...

Deploying Application...

Deployment Completed Successfully

Finished: SUCCESS
```

---

# 🔄 Deployment Workflow

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

# 🌍 Enterprise Deployment Examples

## Docker Deployment

Many organizations deploy containerized applications using Docker.

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

### Workflow

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

### Explanation

- Connects to the remote server using SSH.
- Navigates to the application directory.
- Pulls the latest Docker images.
- Restarts application containers.

---

## Java Application Deployment

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

### Workflow

```text
Copy JAR File
     │
     ▼
Login to Server
     │
     ▼
Restart Application Service
```

### Explanation

- Copies the application JAR using SCP.
- Connects to the deployment server using SSH.
- Restarts the Spring Boot service.

---

## Kubernetes Deployment

```groovy
stage('Deploy') {
    steps {
        sh '''
        kubectl apply -f deployment.yaml
        '''
    }
}
```

### Workflow

```text
Build Image
     │
     ▼
Push Image
     │
     ▼
Apply Kubernetes Manifest
     │
     ▼
Rolling Update
```

### Explanation

Jenkins deploys the latest application version to the Kubernetes cluster using deployment manifests.

---

## Infrastructure Deployment with Terraform

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

### Workflow

```text
Terraform Init
     │
     ▼
Validate Configuration
     │
     ▼
Provision AWS Infrastructure
```

### Explanation

Jenkins provisions or updates cloud infrastructure automatically using Terraform.

---

# 📊 Expected Results

- Deployment pipeline created successfully.
- Build stage completed successfully.
- Deploy stage executed automatically.
- Application deployed to the target environment.
- Manual deployment process eliminated.

---

# 💡 Benefits of Deployment Automation

- Faster software delivery
- Reduced manual effort
- Consistent deployments
- Fewer human errors
- Simplified rollback process
- Supports Continuous Delivery (CD)

---

# 🎯 Interview Questions

### Q1. What is a Jenkins Deployment Job?

A Jenkins Deployment Job automatically deploys an application to a target environment after a successful build.

---

### Q2. Why is deployment automation important?

Deployment automation:

- Reduces manual effort
- Minimizes human errors
- Ensures consistent deployments
- Accelerates software delivery

---

### Q3. How can Jenkins deploy applications?

Jenkins supports deployment through:

- SSH
- SCP
- Docker
- Kubernetes
- Ansible
- Terraform
- Cloud deployment services

---

### Q4. What are common deployment targets?

- Amazon EC2
- Virtual Machines
- Docker Hosts
- Kubernetes Clusters
- AWS ECS
- AWS EKS

---

### Q5. Which command is used to copy files to a remote server?

```bash
scp
```

---

### Q6. Which command is commonly used to connect to a remote server?

```bash
ssh
```

---

# 📌 Key Takeaways

- Jenkins Deployment Jobs automate application delivery after successful builds.
- Deployments can target virtual machines, Docker containers, Kubernetes clusters, or cloud infrastructure.
- Automation improves consistency, reliability, and deployment speed.
- Deployment Jobs are a core component of modern CI/CD pipelines.
- Production environments often combine deployment jobs with approval gates, rollback strategies, and conditional pipelines for safer releases.

---

# 🎉 Conclusion

In this lab, I created a **Jenkins Deployment Job** to automate application deployment after a successful build.

Whether deploying a Java application, Docker containers, Kubernetes workloads, or cloud infrastructure with Terraform, Jenkins provides a scalable and reliable approach to Continuous Delivery by reducing manual effort and ensuring consistent deployments.

---

## ⭐ Support

If you found this project helpful, consider giving this repository a **⭐ Star** and following my **100 Days of DevOps** journey.

Happy Learning! 🚀
