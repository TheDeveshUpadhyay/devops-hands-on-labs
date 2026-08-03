# 🚀 Day 09: Jenkins Multistage Pipeline

Build a production-style Jenkins Declarative Pipeline by organizing the CI/CD workflow into multiple logical stages such as **Build**, **Unit Test**, **Code Analysis**, **Package**, and **Deploy**.

---

## 📖 Overview

In modern DevOps projects, CI/CD pipelines are divided into multiple stages instead of using one large build script.

A Multistage Pipeline provides:

- Better visualization
- Easier debugging
- Pipeline as Code
- Improved maintainability
- Faster issue identification

---

## 🎯 Objective

Create a Jenkins Declarative Pipeline that automates the following workflow:

```text
Build
   ↓
Unit Test
   ↓
Code Analysis
   ↓
Package
   ↓
Deploy
```

---

## 🛠 Prerequisites

- Jenkins
- Git
- Pipeline Plugin
- Java
- Maven
- Jenkins Agent (Optional)

---

# 📂 Project Workflow

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
 Unit Test
     │
     ▼
 Code Analysis
     │
     ▼
 Package
     │
     ▼
 Deploy
     │
     ▼
 Application Server
```

---

# 🚀 Implementation

## Step 1 — Create Pipeline Job

Create a new Jenkins Pipeline Job.

| Setting | Value |
|---------|-------|
| Job Name | Multistage-Pipeline |
| Job Type | Pipeline |

---

## Step 2 — Configure Pipeline

Navigate to:

```
Pipeline
    ↓
Pipeline Script
```

Paste the following Jenkinsfile.

```groovy
pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                echo 'Building Application...'
            }
        }

        stage('Unit Test') {
            steps {
                echo 'Running Unit Tests...'
            }
        }

        stage('Code Analysis') {
            steps {
                echo 'Performing Code Quality Check...'
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging Application...'
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
            echo 'Pipeline Completed Successfully'
        }

        failure {
            echo 'Pipeline Failed'
        }
    }
}
```

Save the Pipeline.

---

## Step 3 — Execute Pipeline

Click

```
Build Now
```

---

## ✅ Expected Stage View

```
✔ Build

✔ Unit Test

✔ Code Analysis

✔ Package

✔ Deploy
```

---

## 🖥 Expected Console Output

```text
Building Application...

Running Unit Tests...

Performing Code Quality Check...

Packaging Application...

Deploying Application...

Pipeline Completed Successfully

Finished: SUCCESS
```

---

# 📦 Understanding Pipeline Stages

| Stage | Purpose |
|--------|----------|
| Build | Compile application source code |
| Unit Test | Execute automated test cases |
| Code Analysis | Analyze code quality using SonarQube |
| Package | Generate deployable artifact |
| Deploy | Deploy application to target environment |

---

# ⚙ Maven Commands Explained

## Build

```bash
mvn clean compile
```

Removes previous build files and compiles Java source code.

---

## Unit Test

```bash
mvn test
```

Executes unit tests.

---

## Code Analysis

```bash
mvn sonar:sonar
```

Runs static code analysis using SonarQube.

---

## Package

```bash
mvn package
```

Creates the deployable JAR/WAR artifact.

Example Output

```
target/
└── app.jar
```

---

## Deploy

```bash
scp target/app.jar ubuntu@<Server-IP>:/opt/app/

ssh ubuntu@<Server-IP> "systemctl restart myapp"
```

Deploys the application to the target server.

---

# 🔍 Understanding `mvn clean compile`

The command contains three parts.

```bash
mvn clean compile
```

| Command | Description |
|----------|-------------|
| mvn | Executes Maven |
| clean | Removes previous build artifacts (`target/`) |
| compile | Compiles Java source files into `.class` files |

Execution Flow

```text
mvn clean compile

      │

      ▼

Delete target/

      │

      ▼

Read pom.xml

      │

      ▼

Download dependencies

      │

      ▼

Compile Java Files

      │

      ▼

Generate .class Files
```

---

# 📚 Maven Lifecycle

| Command | Purpose |
|----------|----------|
| mvn clean | Delete previous build |
| mvn compile | Compile Java source code |
| mvn test | Run Unit Tests |
| mvn package | Create JAR/WAR |
| mvn install | Install artifact into Local Repository |
| mvn deploy | Upload artifact to Remote Repository |

---

# 🌍 Real-World CI/CD Pipeline

Enterprise Jenkins pipelines typically follow this workflow.

```text
Checkout

     │

     ▼

Build

     │

     ▼

Unit Test

     │

     ▼

Code Analysis

     │

     ▼

Package

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

---

# 🐳 Docker Deployment Example

```groovy
stage('Build Docker Image') {
    steps {
        sh 'docker build -t myapp:latest .'
    }
}

stage('Push Image') {
    steps {
        sh 'docker push myrepo/myapp:latest'
    }
}

stage('Deploy') {
    steps {
        sh 'kubectl apply -f deployment.yaml'
    }
}
```

---

# 💼 DevOps Best Practices

- Keep each stage focused on a single responsibility.
- Fail fast if Build or Unit Test fails.
- Run Code Analysis before packaging.
- Store Jenkinsfile in Git.
- Automate deployments using Pipeline as Code.
- Use separate environments for Dev, QA, and Production.

---

# 🎯 Interview Questions

### What is a Jenkins Multistage Pipeline?

A Multistage Pipeline divides the CI/CD workflow into multiple logical stages for better visualization and maintainability.

---

### Why are stages used?

They improve readability, debugging, monitoring, and simplify troubleshooting.

---

### What happens if one stage fails?

The pipeline stops execution, and subsequent stages are skipped by default.

---

### What are common CI/CD stages?

- Checkout
- Build
- Unit Test
- Code Analysis
- Package
- Docker Build
- Docker Push
- Deploy

---

### Why are Multistage Pipelines preferred?

They support Pipeline as Code, improve maintainability, enable version control, and provide better visibility into the CI/CD process.

---

# 📌 Key Takeaways

- Implemented a Jenkins Declarative Multistage Pipeline.
- Learned how CI/CD workflows are organized into logical stages.
- Understood Maven build lifecycle commands.
- Explored production-ready Jenkins pipeline design.
- Practiced Pipeline as Code following industry best practices.

---

# ⭐ Connect With Me

If you found this project helpful, consider giving this repository a **Star ⭐**.

Feel free to connect with me on LinkedIn to follow my **100 Days of DevOps** journey.

Happy Learning! 🚀
