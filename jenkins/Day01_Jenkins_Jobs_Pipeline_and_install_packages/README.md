# 🚀 Jenkins Hands-on Labs

Welcome to my **Jenkins Hands-on Labs** repository! This repository documents my practical learning journey through Jenkins, covering essential concepts used in real-world DevOps and CI/CD environments.

Each lab includes:
- 📖 Detailed explanation
- 🛠️ Step-by-step implementation
- 💻 Commands and pipeline scripts
- 🌍 Real-world use cases
- 🎯 Interview questions
- 📌 Key takeaways

---

## 📚 Jenkins Learning Roadmap

| Day | Lab | Status |
|:---:|------|:------:|
| Day 01 | Set Up Jenkins Server | ✅ |
| Day 02 | Install Jenkins Plugins | ✅ |
| Day 03 | Jenkins Parameterized Builds | ✅ |
| Day 04 | Jenkins Scheduled Jobs | ✅ |
| Day 05 | Jenkins Deploy Pipeline | ✅ |
| Day 06 | Jenkins Conditional Pipeline | ✅ |
| Day 07 | Jenkins Deployment Job | ✅ |
| Day 08 | Jenkins Chained Builds | ✅ |
| Day 09 | Jenkins Multistage Pipeline | ✅ |

---

## 🎯 Skills Covered

Throughout these labs, I explored the following Jenkins concepts:

- Jenkins Installation & Configuration
- Plugin Management
- Parameterized Builds
- Scheduled Jobs (Cron)
- Declarative Pipelines
- Conditional Pipelines
- Deployment Automation
- Chained Builds
- Multistage Pipelines
- Pipeline as Code (Jenkinsfile)
- CI/CD Best Practices




# 🚀 Day 01: Set Up Jenkins Server

## 📖 Overview

This lab demonstrates how to install and configure **Jenkins** on an Ubuntu server. It covers Java installation, Jenkins installation, service management, security group configuration, and accessing the Jenkins web interface.

---

## 🎯 Objective

By completing this lab, you will learn how to:

- Update the Ubuntu system
- Install Java (OpenJDK)
- Install Jenkins
- Start and enable the Jenkins service
- Configure port **8080** for remote access
- Access the Jenkins dashboard from a web browser

---

## 🛠️ Prerequisites

- Ubuntu Server (AWS EC2 or Virtual Machine)
- SSH Access
- sudo privileges
- Internet connection

---

# 🚀 Implementation

## Step 1: Update the System

Update the package repository and upgrade installed packages.

```bash
sudo apt update
sudo apt upgrade -y
```

---

## Step 2: Install Java

Check whether Java is already installed.

```bash
java -version
```

Install OpenJDK 17.

```bash
sudo apt install openjdk-17-jdk -y
```

Verify the installation.

```bash
java -version
```

Example Output:

```text
OpenJDK Runtime Environment (build 21.0.11+10-1-26.04.2-Ubuntu)
```

---

## Step 3: Install Jenkins

### Import the Jenkins GPG Key

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```

### Add the Jenkins Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | \
sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### Install Jenkins

```bash
sudo apt update

sudo apt install jenkins -y
```

---

## Step 4: Start Jenkins Service

Enable Jenkins to start automatically after every reboot.

```bash
sudo systemctl enable jenkins
```

Start the Jenkins service.

```bash
sudo systemctl start jenkins
```

Check the service status.

```bash
sudo systemctl status jenkins
```

Expected Status:

```text
Active: active (running)
```

---

## Step 5: Configure Port 8080

Open **TCP Port 8080** in your AWS EC2 Security Group.

| Type | Port | Source |
|------|------|--------|
| Custom TCP | 8080 | 0.0.0.0/0 *(Learning Purpose Only)* |

> **Note:** In production environments, restrict access to trusted IP addresses instead of allowing access from anywhere.

---

## Step 6: Access Jenkins

Open your browser and navigate to:

```text
http://<EC2-Public-IP>:8080
```

Example:

```text
http://13.233.xxx.xxx:8080
```

You should now see the **Jenkins Unlock Screen**.

---

# 🏗️ Installation Workflow

```text
Ubuntu Server
      │
      ▼
Update Packages
      │
      ▼
Install Java
      │
      ▼
Install Jenkins
      │
      ▼
Start Jenkins Service
      │
      ▼
Open Port 8080
      │
      ▼
Access Jenkins Dashboard
```

---

# 💡 Why is Java Required for Jenkins?

Jenkins is developed using the **Java programming language**.

Therefore, Java Runtime Environment (JRE) or Java Development Kit (JDK) must be installed before Jenkins can run.

Without Java, the Jenkins service cannot start.

---

# 📊 Expected Outcome

After completing this lab:

- ✅ Ubuntu system updated
- ✅ Java installed successfully
- ✅ Jenkins installed successfully
- ✅ Jenkins service running
- ✅ Port 8080 configured
- ✅ Jenkins dashboard accessible from the browser

---

# 🎯 Interview Question

### Q1. Why is Java required for Jenkins?

**Answer:**

Jenkins is a Java-based application. It requires the Java Runtime Environment (JRE) or Java Development Kit (JDK) to execute. Without Java, Jenkins cannot start or run.

---

# 📌 Key Takeaways

- Jenkins requires Java to run.
- Jenkins can be installed using the official Jenkins repository.
- `systemctl` is used to manage the Jenkins service.
- Port **8080** must be open to access the Jenkins web interface.
- This setup serves as the foundation for building CI/CD pipelines.

---

# 🎉 Conclusion

In this lab, we successfully installed and configured Jenkins on an Ubuntu server. We also enabled the Jenkins service, configured network access, and verified the installation by accessing the Jenkins dashboard through a web browser.

This environment is now ready for creating Jenkins jobs and building CI/CD pipelines.

---

## ⭐ Support

If you found this project helpful, consider giving this repository a **⭐ Star**.

Happy Learning! 🚀
