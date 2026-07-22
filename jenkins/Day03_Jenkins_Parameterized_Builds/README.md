# Day 03: Jenkins Parameterized Builds

## 📌 Solution

### 🎯 Objective

Learn how to create a **Parameterized Jenkins Job** that accepts user input during build execution.

---

## 📖 Scenario

In real-world DevOps projects, the same Jenkins job is often used for multiple environments such as **Development**, **Testing**, **Staging**, and **Production**.

Instead of creating separate jobs for each environment, **Jenkins Parameterized Builds** allow users to provide input values when triggering a build, making jobs more flexible and reusable.

---

## Step 1: Login to Jenkins

Open Jenkins in your browser:

```text
http://<EC2-Public-IP>:8080
```

---

## Step 2: Create a New Freestyle Project

Click:

```text
New Item
```

Enter the job name:

```text
Parameterized-Build
```

Select:

```text
Freestyle Project
```

Click:

```text
OK
```

---

## Step 3: Enable Parameterized Build

Navigate to:

```text
General
```

Enable:

```text
☑ This project is parameterized
```

---

## Step 4: Add a String Parameter

Click:

```text
Add Parameter
```

Choose:

```text
String Parameter
```

Configure the parameter as follows:

| Field | Value |
|--------|-------|
| **Name** | `ENVIRONMENT` |
| **Default Value** | `dev` |
| **Description** | Enter deployment environment |

Click **Apply** → **Save**.

---

## Step 5: Configure the Build Step

Navigate to:

```text
Build Steps
```

Click:

```text
Add Build Step
```

Choose:

```text
Execute Shell
```

Paste the following script:

```bash
#!/bin/bash

echo "Deployment Environment: $ENVIRONMENT"

if [ "$ENVIRONMENT" = "dev" ]; then
    echo "Deploying to Development Environment"
elif [ "$ENVIRONMENT" = "test" ]; then
    echo "Deploying to Testing Environment"
elif [ "$ENVIRONMENT" = "stage" ]; then
    echo "Deploying to Staging Environment"
elif [ "$ENVIRONMENT" = "prod" ]; then
    echo "Deploying to Production Environment"
else
    echo "Invalid Environment"
fi
```

Click **Save**.

---

## Step 6: Build with Parameters

Instead of clicking **Build Now**, select:

```text
Build with Parameters
```

Enter:

```text
ENVIRONMENT = dev
```

Click:

```text
Build
```

---

## Step 7: View Console Output

Open the build.

Click:

```text
Console Output
```

### Expected Output

```text
Deployment Environment: dev
Deploying to Development Environment
Finished: SUCCESS
```

Run the job again with:

```text
ENVIRONMENT = test
```

### Expected Output

```text
Deployment Environment: test
Deploying to Testing Environment
Finished: SUCCESS
```

---

## 🔄 Workflow

```text
Developer
     │
     ▼
Build with Parameters
     │
     ▼
ENVIRONMENT = dev/test/stage/prod
     │
     ▼
Execute Shell Script
     │
     ▼
Deploy to Selected Environment
```

---

## 💡 Key Learning

- Parameterized Builds make Jenkins jobs dynamic and reusable.
- The same job can be used for multiple environments by passing different input values.
- Parameters are exposed as environment variables inside the build.
- This approach reduces duplicate Jenkins jobs and simplifies CI/CD pipelines.

---

## ❓ Interview Questions

### Q1. What is a Parameterized Build in Jenkins?

**Answer:**

A Parameterized Build allows users to provide input values when triggering a Jenkins job. These values are used during the build process, making the job dynamic and reusable.

---

### Q2. Why do we use Parameterized Builds?

**Answer:**

Parameterized Builds eliminate the need to create separate jobs for different environments or configurations. A single Jenkins job can perform different actions based on the parameter values supplied during execution.

---

### Q3. What are the commonly used parameter types in Jenkins?

**Answer:**

- String Parameter
- Choice Parameter
- Boolean Parameter
- Password Parameter
- File Parameter
- Text Parameter

---

### Q4. How do you access parameter values inside a Jenkins job?

**Answer:**

Parameter values are available as environment variables and can be accessed in shell scripts using the `$` syntax.

Example:

```bash
echo $ENVIRONMENT
```
