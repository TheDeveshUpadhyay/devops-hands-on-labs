# 🚀 Day 05: Jenkins Project Security

## 📖 Overview

This project demonstrates how to secure a Jenkins server by enabling authentication, creating multiple users, and implementing **Role-Based Access Control (RBAC)** using the **Role-Based Authorization Strategy** plugin.

---

## 🎯 Objective

Learn how to secure Jenkins by:

- Enabling Security
- Creating Multiple Users
- Implementing Role-Based Access Control (RBAC)
- Assigning different permissions based on user roles

---

## 🌍 Real-World Scenario

In enterprise environments, not every team member should have full administrative access to Jenkins.

Different users require different permission levels based on their responsibilities.

| Role | Responsibilities |
|------|------------------|
| **Admin** | Manage Jenkins, install plugins, create jobs, manage users |
| **Developer** | Build jobs, view logs, access assigned projects |
| **Tester** | View build status, monitor job results |

Implementing **RBAC** ensures secure and controlled access to Jenkins resources.

---

## 🛠 Prerequisites

- Jenkins Installed
- Administrative Access to Jenkins
- Role-Based Authorization Strategy Plugin
- Jenkins Own User Database Enabled

---

# 🚀 Implementation

## Step 1: Install the Role-Based Authorization Plugin

Navigate to:

```text
Dashboard
    ↓
Manage Jenkins
    ↓
Plugins
    ↓
Available Plugins
```

Search for:

```text
Role-Based Authorization Strategy
```

Install the plugin.

Restart Jenkins if required.

---

## Step 2: Enable Jenkins Security

Navigate to:

```text
Dashboard
    ↓
Manage Jenkins
    ↓
Security
```

Configure the following settings:

### Enable Security

```text
✔ Enable Security
```

### Security Realm

Select:

```text
Jenkins' own user database
```

### Authorization

Select:

```text
Role-Based Strategy
```

Click **Save**.

---

## Step 3: Create Jenkins Users

Navigate to:

```text
Dashboard
    ↓
Manage Jenkins
    ↓
Users
    ↓
Create User
```

Create the following users:

```text
admin
developer
tester
```

---

## Step 4: Create Roles

Navigate to:

```text
Dashboard
    ↓
Manage Jenkins
    ↓
Manage and Assign Roles
```

Create the following roles:

```text
admin
developer
tester
```

---

## Step 5: Assign Permissions

Configure permissions for each role.

### 👨‍💼 Admin

Grant full administrative permissions.

Example permissions:

- Overall → Administer
- Job → Create
- Job → Configure
- Job → Delete
- Credentials → Create
- Credentials → Update
- Credentials → Delete

---

### 👨‍💻 Developer

Grant permissions required for development activities.

Example permissions:

- Overall → Read
- Job → Read
- Job → Build
- Job → Workspace
- Job → Discover

Developers cannot modify Jenkins system settings.

---

### 🧪 Tester

Grant read-only permissions.

Example permissions:

- Overall → Read
- Job → Read
- View → Read

Testers can monitor builds but cannot trigger or modify jobs.

---

## Step 6: Assign Users to Roles

Navigate to:

```text
Dashboard
    ↓
Manage Jenkins
    ↓
Manage and Assign Roles
    ↓
Assign Roles
```

Assign users:

| User | Role |
|------|------|
| admin | Admin |
| developer | Developer |
| tester | Tester |

Click **Save**.

---

## Step 7: Verify Access

Log in with each user account and verify access.

### Admin

- Full Jenkins access
- Create Jobs
- Install Plugins
- Manage Users
- Configure Jenkins

---

### Developer

- View Jobs
- Trigger Builds
- View Console Output

Cannot:

- Install Plugins
- Create Users
- Configure Jenkins Security

---

### Tester

- View Jobs
- View Build History
- View Console Logs

Cannot:

- Trigger Builds
- Modify Jobs
- Configure Jenkins

---

# 🔄 User Access Flow

```text
Users
   │
   ▼
Authentication
   │
   ▼
Role-Based Authorization
   │
   ▼
Assigned Permissions
   │
   ▼
Access Jenkins Resources
```

---

# 💼 Real-World Example

A software development team may have the following Jenkins access model:

```text
Admin
   │
   ├── Configure Jenkins
   ├── Install Plugins
   ├── Create Users
   └── Manage Credentials

Developer
   │
   ├── Trigger Builds
   ├── View Logs
   └── Access Assigned Jobs

Tester
   │
   ├── Monitor Builds
   ├── View Reports
   └── Verify Deployment Status
```

This separation of responsibilities improves security and reduces operational risks.

---

# 🔒 Why Use RBAC?

Without Role-Based Access Control:

- Every user has excessive permissions.
- Higher risk of accidental changes.
- Increased security vulnerabilities.

With RBAC:

- Least privilege access.
- Improved security.
- Better compliance.
- Easier user management.
- Reduced operational risk.

---

# ✅ Expected Outcome

- Jenkins Security enabled successfully.
- Authentication configured.
- Multiple users created.
- Roles created using RBAC.
- Permissions assigned correctly.
- Users can access only the resources allowed by their roles.

---

# 🎯 Interview Questions

### Q1. What is Jenkins Project Security?

**Answer:**

Jenkins Project Security protects Jenkins by controlling user authentication and authorization, ensuring users have only the permissions required for their role.

---

### Q2. What is Role-Based Access Control (RBAC)?

**Answer:**

RBAC is a security model where permissions are assigned to roles rather than individual users. Users inherit permissions based on their assigned role.

---

### Q3. Why is RBAC important?

**Answer:**

RBAC:

- Improves security
- Reduces unauthorized access
- Simplifies permission management
- Supports the principle of least privilege

---

### Q4. Which plugin provides RBAC in Jenkins?

**Answer:**

```text
Role-Based Authorization Strategy
```

---

### Q5. What authentication option is commonly used in Jenkins?

**Answer:**

```text
Jenkins' own user database
```

Other authentication providers include LDAP, Active Directory, GitHub OAuth, and SAML.

---

### Q6. What is the difference between Authentication and Authorization?

| Authentication | Authorization |
|---------------|---------------|
| Verifies user identity | Determines user permissions |
| "Who are you?" | "What can you do?" |

---

# 📌 Key Takeaways

- Jenkins Security protects the CI/CD environment from unauthorized access.
- RBAC allows permissions to be managed through roles instead of individual users.
- Authentication verifies user identity, while authorization controls access.
- Following the Principle of Least Privilege improves security and operational stability.
- RBAC is a standard practice in enterprise Jenkins deployments.

---

# 🎉 Conclusion

Today, I learned how to secure Jenkins using **Role-Based Access Control (RBAC)**. By enabling authentication, creating users, defining roles, and assigning permissions, Jenkins can provide secure access tailored to different team responsibilities.

Implementing RBAC is a fundamental DevOps security practice that helps protect CI/CD pipelines while ensuring users have only the permissions they need.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and following my DevOps learning journey.

Happy Learning! 🚀
