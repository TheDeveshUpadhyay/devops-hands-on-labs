# Jenkins

## Jenkins Learning Roadmap

| Day | Topic | Status |
|-----|-----------------------------------------------|:------:|
| Day 68 | Set Up Jenkins Server | ✅ |
| Day 69 | Install Jenkins Plugins | ⏳ |
| Day 70 | Configure Jenkins User Access | ⏳ |
| Day 71 | Configure Jenkins Job for Package Installation | ⏳ |
| Day 72 | Jenkins Parameterized Builds | ⏳ |
| Day 73 | Jenkins Scheduled Jobs | ⏳ |
| Day 74 | Jenkins Database Backup Job | ⏳ |
| Day 75 | Jenkins Slave Nodes | ⏳ |
| Day 76 | Jenkins Project Security | ⏳ |
| Day 77 | Jenkins Deploy Pipeline | ⏳ |
| Day 78 | Jenkins Conditional Pipeline | ⏳ |
| Day 79 | Jenkins Deployment Job | ⏳ |
| Day 80 | Jenkins Chained Builds | ⏳ |
| Day 81 | Jenkins Multistage Pipeline | ⏳ |

---

# Day 68: Set Up Jenkins Server

## Solution

### 🎯 Objective

Install and configure Jenkins on an Ubuntu server running on AWS EC2.

---

## Step 1: Update the System

Update the package repository and upgrade existing packages.

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

### Verify Java Installation

```bash
java -version
```

**Expected Output**

```text
OpenJDK Runtime Environment (build 21.0.11+10-1-26.04.2-Ubuntu)
```

---

## Step 3: Install Jenkins

### Download the Jenkins Repository Key

```bash
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
```

### Add the Jenkins Repository

```bash
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
/etc/apt/sources.list.d/jenkins.list > /dev/null
```

### Update Package Repository

```bash
sudo apt update
```

### Install Jenkins

```bash
sudo apt install jenkins -y
```

---

## Step 4: Start Jenkins

Enable Jenkins to start automatically after every reboot.

```bash
sudo systemctl enable jenkins
```

Start the Jenkins service.

```bash
sudo systemctl start jenkins
```

Verify the Jenkins service.

```bash
sudo systemctl status jenkins
```

**Expected Output**

```text
Active: active (running)
```

---

## Step 5: Open Port 8080

Navigate to:

```text
AWS Console
    └── EC2
         └── Security Groups
              └── Inbound Rules
```

Add the following inbound rule:

| Type | Port | Source |
|------|------|--------|
| Custom TCP | 8080 | 0.0.0.0/0 *(For learning only. Restrict access in production.)* |

---

## Step 6: Access Jenkins

Open your browser:

```text
http://<EC2-Public-IP>:8080
```

Example:

```text
http://13.233.xxx.xxx:8080
```

You should now see the Jenkins Unlock screen.

---

## Architecture

```text
                Developer
                    │
                    ▼
          AWS EC2 Ubuntu Server
                    │
                    ▼
            Install OpenJDK 17
                    │
                    ▼
             Install Jenkins
                    │
                    ▼
         Start Jenkins Service
                    │
                    ▼
        Open Security Group Port 8080
                    │
                    ▼
          Access Jenkins Web UI
```

---

## Verification Checklist

- ✅ Ubuntu packages updated
- ✅ Java installed successfully
- ✅ Jenkins installed successfully
- ✅ Jenkins service is running
- ✅ Port 8080 opened in the AWS Security Group
- ✅ Jenkins dashboard accessible through the browser

---

## Interview Question

### Q1. Why is Java required for Jenkins?

**Answer:**

Jenkins is a Java-based automation server developed using the Java programming language. Therefore, it requires the Java Runtime Environment (JRE) or Java Development Kit (JDK) to execute its application code.

---

## Key Takeaways

- Installed OpenJDK 17.
- Installed Jenkins from the official Jenkins repository.
- Started and enabled the Jenkins service.
- Opened port **8080** in the AWS Security Group.
- Successfully accessed the Jenkins web interface.
