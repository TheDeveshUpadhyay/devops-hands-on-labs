# Day 73: Jenkins Scheduled Jobs

## 📌 Solution

## 🎯 Objective

Learn how to schedule Jenkins jobs to run automatically at specific times using **Build Triggers** and **Cron syntax**.

---

## 📖 Scenario

In real-world DevOps environments, many tasks need to run automatically without manual intervention. Examples include:

- Nightly builds
- Scheduled backups
- Automated testing
- Log cleanup
- Health checks
- Report generation

Jenkins Scheduled Jobs help automate these recurring tasks using **Cron expressions**.

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
Scheduled-Job
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

## Step 3: Configure Build Triggers

Navigate to:

```text
Build Triggers
```

Enable:

```text
☑ Build periodically
```

---

## Step 4: Configure Schedule

In the **Schedule** box, enter the following Cron expression:

```cron
*/5 * * * *
```

This means the job will execute **every 5 minutes**.

### Common Jenkins Cron Examples

| Cron Expression | Schedule |
|-----------------|----------|
| `*/5 * * * *` | Every 5 minutes |
| `0 * * * *` | Every hour |
| `0 0 * * *` | Every day at midnight |
| `0 9 * * 1-5` | Monday to Friday at 9:00 AM |
| `H/5 * * * *` | Every 5 minutes (Jenkins recommended) |

Click **Apply** → **Save**.

---

## Step 5: Wait for Scheduled Execution

Jenkins will automatically trigger the job according to the configured schedule.

> No manual **Build Now** action is required.

---

## Step 6: View Console Output

Open the latest build.

Click:

```text
Console Output
```

### Expected Output

```text
Scheduled Job Executed Successfully
Execution Time:
Tue Jul 22 10:35:01 UTC 2026
Finished: SUCCESS
```

---

# 🔄 Workflow

```text
Developer
     │
     ▼
Configure Build Trigger
     │
     ▼
Enter Cron Expression
     │
     ▼
Jenkins Scheduler
     │
     ▼
Execute Job Automatically
     │
     ▼
Build Completed
```

---

# ❓ Interview Questions

## Q1. What are Scheduled Jobs in Jenkins?

**Answer:**

Scheduled Jobs are Jenkins jobs configured to execute automatically at predefined times using Cron expressions, eliminating the need for manual execution.

---

## Q2. What is Cron syntax in Jenkins?

**Answer:**

Cron syntax is a scheduling format consisting of five fields that define when a Jenkins job should run.

```text
MINUTE  HOUR  DAY_OF_MONTH  MONTH  DAY_OF_WEEK
```

Example:

```cron
*/5 * * * *
```

Runs the job every five minutes.

---

## Q3. What is the difference between `*/5 * * * *` and `H/5 * * * *`?

**Answer:**

- `*/5 * * * *` runs every 5 minutes starting exactly at minute **0**.
- `H/5 * * * *` uses Jenkins' hash function to distribute job execution across different minutes, reducing server load when multiple jobs are scheduled.

---

## Q4. Why are Scheduled Jobs used in DevOps?

**Answer:**

Scheduled Jobs automate recurring tasks such as:

- Nightly builds
- Automated testing
- Backups
- Log cleanup
- Health checks
- Report generation

This improves efficiency and reduces manual effort.

---

## Q5. Where do you configure Scheduled Jobs in Jenkins?

**Answer:**

Navigate to:

```text
Job
 └── Configure
      └── Build Triggers
             └── Build periodically
```

Then provide the required Cron expression and save the job.

---

# 📝 Jenkins Cron Practice

## Q1. Create a Jenkins cron expression to run a job every 10 minutes.

**Answer**

```cron
*/10 * * * *
```

---

## Q2. Create a Jenkins cron expression to run a job daily at 2:30 AM.

**Answer**

```cron
30 2 * * *
```

---

## Q3. Create a Jenkins cron expression to run a job every Monday at 9:00 AM.

**Answer**

```cron
0 9 * * 1
```

---

## Q4. Create a Jenkins cron expression to run a job every 5 minutes using Jenkins hashing (`H`).

**Answer**

```cron
H/5 * * * *
```

---

## Q5. Create a Jenkins cron expression to run a job Monday through Friday at 6:15 PM.

**Answer**

```cron
15 18 * * 1-5
```

---

## Q6. Every Sunday at 11:30 PM

**Answer**

```cron
30 23 * * 7
```

or

```cron
30 23 * * 0
```

> Sunday can be represented as **0** or **7**.

---

## Q7. Every 2 hours

**Answer**

```cron
0 */2 * * *
```

---

## Q8. Every 15 minutes

**Answer**

```cron
*/15 * * * *
```

---

## Q9. Every 1st day of the month at 12:00 AM

**Answer**

```cron
0 0 1 * *
```

---

## Q10. Every Saturday and Sunday at 8:00 AM

**Answer**

```cron
0 8 * * 0,6
```

or

```cron
0 8 * * 6,0
```

---

## Q11. Every 30 seconds *(Trick Question)*

**Answer**

❌ **Not possible in Jenkins Cron.**

Jenkins cron supports a **minimum interval of one minute**.

Cron format:

```text
MINUTE  HOUR  DAY_OF_MONTH  MONTH  DAY_OF_WEEK
```

There is **no seconds field** in Jenkins cron syntax.

Therefore:

> **Every 30 seconds → Not possible using Jenkins Cron scheduling.**

For sub-minute scheduling, use an external scheduler or a script with `sleep`, as Jenkins itself cannot trigger jobs every 30 seconds.

---

# 💡 Key Learning

- Jenkins Scheduled Jobs automate repetitive tasks.
- Scheduling is configured using **Build periodically**.
- Jenkins uses a **5-field Cron syntax**.
- Prefer using **`H`** instead of fixed intervals when multiple jobs are scheduled to distribute workload.
- Scheduled jobs are commonly used for backups, testing, deployments, monitoring, and maintenance tasks.
