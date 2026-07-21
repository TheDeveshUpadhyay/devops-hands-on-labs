# Day 69: Install Jenkins Plugins

## Solution

Jenkins provides only the core features after installation. To integrate with tools like **Git, Docker, Kubernetes, Maven, AWS, SonarQube, and Slack**, you need to install plugins.

Plugins make Jenkins flexible and customizable for different **CI/CD** requirements.

---

## Step 1: Install Plugins

Navigate to:

**Manage Jenkins → Plugins → Available Plugins**

Search for and install the following plugins:

| Plugin | Purpose |
|--------|---------|
| Git | Integrates Jenkins with Git repositories |
| Pipeline: Stage View | Displays pipeline stages visually |
| Blue Ocean *(Optional)* | Provides a modern CI/CD pipeline interface |
| SonarQube Scanner | Integrates Jenkins with SonarQube for code quality analysis |
| Docker Pipeline | Enables Docker commands within Jenkins pipelines |
| Kubernetes CLI | Allows Jenkins to interact with Kubernetes clusters |
| Maven Integration | Supports Maven build projects |
| Pipeline Maven Integration | Integrates Maven with Jenkins Pipelines |
| Config File Provider | Manages configuration files and credentials |
| Email Extension | Sends build notifications via email |
| Slack Notification | Sends Jenkins notifications to Slack |
| Role-Based Authorization Strategy | Implements Role-Based Access Control (RBAC) |

---

## Step 2: Restart Jenkins (If Required)

Restart Jenkins:

```bash
sudo systemctl restart jenkins
```

Verify the Jenkins service:

```bash
sudo systemctl status jenkins
```

### Expected Output

```text
Active: active (running)
```

---

## Interview Questions

### Q1. What are Jenkins plugins?

**Answer:**

Jenkins plugins are extensions that add new features and integrations to Jenkins, enabling it to work with tools such as Git, Docker, Kubernetes, Maven, AWS, SonarQube, and Slack.

---

### Q2. Why are plugins required in Jenkins?

**Answer:**

Plugins extend Jenkins functionality, allowing it to integrate with various DevOps tools and automate tasks such as:

- Source code management
- Build automation
- Testing
- Code quality analysis
- Containerization
- Deployment
- Notifications
- Infrastructure automation

Without plugins, Jenkins provides only its core CI/CD capabilities.
