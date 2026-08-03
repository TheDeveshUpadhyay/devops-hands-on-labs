# 🚀 Day 02: Install Jenkins Plugins

## 📖 Overview

This lab demonstrates how to install and manage **Jenkins Plugins**, which extend Jenkins with additional features and integrations. Plugins enable Jenkins to work with popular DevOps tools such as Git, Docker, Kubernetes, Maven, SonarQube, Slack, and AWS, making Jenkins a powerful CI/CD automation server.

---

## 🎯 Objective

By completing this lab, you will learn how to:

- Understand the purpose of Jenkins Plugins
- Install commonly used plugins
- Restart Jenkins after plugin installation (if required)
- Verify Jenkins service status
- Explore plugins commonly used in real-world DevOps projects

---

## 🛠️ Prerequisites

- Jenkins Installed
- Jenkins Running
- Administrator Access
- Internet Connection

---

# 🚀 Implementation

## Step 1: Install Jenkins Plugins

Navigate to:

```text
Dashboard
   └── Manage Jenkins
         └── Plugins
               └── Available Plugins
```

Search for and install the following plugins.

| Plugin | Purpose |
|---------|---------|
| Git | Integrates Jenkins with Git repositories |
| Pipeline: Stage View | Visualizes pipeline stages |
| Blue Ocean *(Optional)* | Modern CI/CD pipeline interface |
| SonarQube Scanner | Performs code quality analysis |
| Docker Pipeline | Builds and deploys Docker containers |
| Kubernetes CLI | Executes Kubernetes commands from Jenkins |
| Maven Integration | Builds Java applications using Maven |
| Pipeline Maven Integration | Integrates Maven with Jenkins Pipelines |
| Config File Provider | Manages configuration files and credentials |
| Email Extension | Sends build notifications via email |
| Slack Notification | Sends build notifications to Slack |
| Role-Based Authorization Strategy | Implements Role-Based Access Control (RBAC) |

> **Tip:** Install only the plugins required for your project to keep Jenkins lightweight and easier to maintain.

---

## Step 2: Restart Jenkins (If Required)

Some plugins require Jenkins to restart before they become active.

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Verify the Jenkins service:

```bash
sudo systemctl status jenkins
```

Expected Output:

```text
Active: active (running)
```

---

# 🏗️ Plugin Installation Workflow

```text
Jenkins Dashboard
        │
        ▼
Manage Jenkins
        │
        ▼
Plugins
        │
        ▼
Available Plugins
        │
        ▼
Search Plugin
        │
        ▼
Install Plugin
        │
        ▼
Restart Jenkins (If Required)
        │
        ▼
Verify Installation
```

---

# 🌍 Real-World Usage

In enterprise environments, Jenkins plugins are used to integrate CI/CD pipelines with various tools and platforms.

| Tool | Jenkins Plugin |
|------|----------------|
| GitHub | Git Plugin |
| Docker | Docker Pipeline |
| Kubernetes | Kubernetes CLI |
| Maven | Maven Integration |
| SonarQube | SonarQube Scanner |
| Slack | Slack Notification |
| Email | Email Extension |
| AWS | AWS-related Plugins |

These integrations allow Jenkins to automate the complete software delivery lifecycle, from code checkout to deployment and notifications.

---

# 📊 Expected Outcome

After completing this lab:

- ✅ Required plugins installed successfully
- ✅ Jenkins restarted (if required)
- ✅ Jenkins service running successfully
- ✅ Jenkins ready for advanced CI/CD pipeline development

---

# 🎯 Interview Questions

### Q1. What are Jenkins Plugins?

**Answer:**

Jenkins Plugins are extensions that add new features and integrations to Jenkins. They enable Jenkins to work with tools such as Git, Docker, Kubernetes, Maven, SonarQube, AWS, Slack, and many others.

---

### Q2. Why are Plugins required in Jenkins?

**Answer:**

Plugins extend Jenkins functionality by enabling integrations with external tools and services. They help automate source code management, building, testing, deployment, notifications, security, and many other CI/CD tasks.

---

# 📌 Key Takeaways

- Jenkins provides only core functionality after installation.
- Plugins extend Jenkins capabilities and integrate it with DevOps tools.
- Different plugins support different stages of the CI/CD pipeline.
- Restart Jenkins after installing plugins if required.
- Installing only necessary plugins improves performance and simplifies maintenance.

---

# 🎉 Conclusion

In this lab, I explored how Jenkins Plugins enhance the capabilities of Jenkins by integrating it with popular DevOps tools. Installing the right plugins allows Jenkins to automate building, testing, code analysis, deployments, notifications, and security, making it an essential component of modern CI/CD pipelines.

---

## ⭐ Support

If you found this project helpful, consider giving this repository a **⭐ Star** and following my **100 Days of DevOps** journey.

Happy Learning! 🚀
