# 🚀 Day 6: Jenkins Conditional Pipeline

## 📌 Objective

Learn how to use **Conditional Execution** in a Jenkins Pipeline so that specific stages execute only when predefined conditions are satisfied.

---

# 📖 Scenario

In real-world DevOps projects, not every stage should execute for every build.

For example:

- Developers push code to multiple branches.
- Build and Test should run for every branch.
- Deploy should execute only from the **main** branch.
- Production deployment should happen only when the environment is **Production**.

Jenkins provides the **`when`** directive to achieve this.

---

# 🛠️ Prerequisites

- Jenkins Installed
- Jenkins Running Successfully
- Basic Knowledge of Jenkins Pipeline
- Pipeline Plugin Installed

---

# 📁 Step 1: Create a Pipeline Job

Navigate to:

```
Dashboard
   ↓
New Item
```

Enter:

```
Conditional-Pipeline
```

Select:

```
Pipeline
```

Click:

```
OK
```

---

# ⚙️ Step 2: Configure the Pipeline

Navigate to:

```
Pipeline
```

Definition:

```
Pipeline Script
```

Paste the following Jenkins Pipeline.

```groovy
pipeline {
    agent any

    environment {
        ENVIRONMENT = "dev"
    }

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

            when {
                environment name: 'ENVIRONMENT', value: 'prod'
            }

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

Click **Save**.

---

# ▶️ Step 3: Build the Pipeline

Click

```
Build Now
```

---

# 📊 Step 4: View Stage View

Current Environment

```groovy
ENVIRONMENT = "dev"
```

Jenkins checks

```
Is ENVIRONMENT == prod ?
```

Result

```
No
```

Deploy stage is skipped.

### Stage View

```
✔ Build
✔ Test
⏭ Deploy (Skipped)
```

---

# 🖥️ Step 5: View Console Output

Navigate to

```
Build History
      ↓
Latest Build
      ↓
Console Output
```

Expected Output

```
Building Application...
Running Tests...
Stage "Deploy" skipped due to when conditional
Pipeline Completed Successfully
Finished: SUCCESS
```

---

# 🚀 Step 6: Execute the Deploy Stage

Update the environment variable.

```groovy
environment {
    ENVIRONMENT = "prod"
}
```

Run the pipeline again.

Jenkins checks

```
Is ENVIRONMENT == prod ?
```

Result

```
Yes
```

Deploy stage executes successfully.

### Stage View

```
✔ Build
✔ Test
✔ Deploy
```

Console Output

```
Building Application...
Running Tests...
Deploying Application...
Pipeline Completed Successfully
Finished: SUCCESS
```

---

# 📚 Understanding the `when` Directive

The **`when`** directive tells Jenkins:

> Execute a stage only when the specified condition evaluates to **true**.

Syntax

```groovy
stage('Deploy') {

    when {
        condition
    }

    steps {

    }
}
```

If the condition evaluates to **false**, Jenkins skips the stage.

---

# 🔥 Common `when` Conditions

## 1️⃣ Environment

```groovy
when {
    environment name: 'ENVIRONMENT', value: 'prod'
}
```

Deploy executes only when

```
ENVIRONMENT = prod
```

---

## 2️⃣ Branch

```groovy
when {
    branch 'main'
}
```

Deploy executes only from the **main** branch.

---

## 3️⃣ Expression

```groovy
when {
    expression {
        return true
    }
}
```

Allows any custom Groovy expression.

---

## 4️⃣ Boolean Parameter

```groovy
parameters {
    booleanParam(
        name: 'DEPLOY',
        defaultValue: false
    )
}

stage('Deploy') {

    when {
        expression {
            return params.DEPLOY
        }
    }

    steps {
        echo "Deploying..."
    }
}
```

Deploy executes only when

```
DEPLOY = true
```

---

## 5️⃣ Git Tag

```groovy
when {
    buildingTag()
}
```

Runs only when the pipeline is triggered by a Git Tag.

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
Build
    │
    ▼
Test
    │
    ▼
Evaluate Condition
      │
      ├──────────────► False
      │                     │
      │                     ▼
      │             Skip Deploy
      │
      ▼
     True
      │
      ▼
Deploy
```

---

# 🌍 Real Project Example 1

Developers work on multiple branches.

```
feature/login
feature/payment
develop
main
```

Pipeline

```groovy
stage('Deploy') {

    when {
        branch 'main'
    }

    steps {
        sh 'kubectl apply -f deployment.yaml'
    }
}
```

### Result

| Branch | Deploy |
|---------|--------|
| feature/login | ❌ |
| feature/payment | ❌ |
| develop | ❌ |
| main | ✅ |

This prevents accidental production deployments.

---

# 🌍 Real Project Example 2

Deploy only in the Production environment.

```groovy
environment {
    ENVIRONMENT = "prod"
}

when {
    environment name: 'ENVIRONMENT', value: 'prod'
}
```

### Result

| Environment | Deploy |
|-------------|--------|
| DEV | ❌ |
| QA | ❌ |
| PROD | ✅ |

---

# ✅ Expected Outcome

- Jenkins Pipeline created successfully.
- Build stage executed successfully.
- Test stage executed successfully.
- Deploy stage executed only when the condition was satisfied.
- Pipeline completed successfully.

---

# 🎯 Interview Questions

### Q1. What is a Conditional Pipeline?

**Answer:**

A Conditional Pipeline executes specific stages only when predefined conditions are satisfied using the **`when`** directive.

---

### Q2. What is the purpose of the `when` directive?

**Answer:**

The `when` directive controls whether a stage should execute or be skipped based on conditions such as branch, environment, parameters, expressions, or Git tags.

---

### Q3. What happens when a `when` condition evaluates to false?

**Answer:**

Jenkins skips that stage and continues executing the remaining stages.

---

### Q4. What are the commonly used `when` conditions?

- Branch
- Environment
- Expression
- Parameters
- Building Tag

---

### Q5. What is the difference between Stage and `when`?

| Stage | `when` |
|--------|---------|
| Defines a logical phase of the pipeline | Determines whether the stage should execute |

---

### Q6. Why are Conditional Pipelines used in real projects?

**Answer:**

Conditional Pipelines:

- Prevent unnecessary deployments.
- Improve security.
- Reduce pipeline execution time.
- Ensure deployments occur only under predefined conditions.

---

# 🎉 Conclusion

Today, I learned how to use **Conditional Pipelines** in Jenkins with the powerful **`when`** directive. This feature enables smarter CI/CD workflows by executing stages only when specific conditions are met, making deployments safer, faster, and more efficient.

---

## ⭐ If you found this repository helpful

Give it a ⭐ on GitHub and follow my DevOps learning journey!

Happy Learning! 🚀
