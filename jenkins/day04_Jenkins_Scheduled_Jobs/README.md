# 🚀 Day 04: Jenkins Scheduled Jobs

## 📖 Overview

This project demonstrates how to schedule Jenkins jobs to run automatically at predefined times using **Build Triggers** and **Cron expressions**. Scheduled jobs are commonly used in CI/CD pipelines for automating recurring tasks such as nightly builds, backups, testing, and maintenance.

---

## 🎯 Objective

Learn how to configure Jenkins jobs to execute automatically using **Cron syntax**.

---

## 🌍 Real-World Scenario

In production environments, many repetitive tasks must run without manual intervention, including:

- 🌙 Nightly Builds
- 🧪 Automated Testing
- 💾 Database Backups
- 📝 Log Cleanup
- 📊 Report Generation
- ❤️ Health Checks

Jenkins automates these recurring tasks through scheduled jobs.

---

## 🛠 Prerequisites

- Jenkins Installed
- Git Installed
- Jenkins Freestyle Project
- Access to Jenkins Web UI

---

# 🚀 Implementation

## Step 1: Access Jenkins

Open Jenkins in your browser.

```text
http://<EC2-Public-IP>:8080
```

---

## Step 2: Create a Freestyle Project

Navigate to:

```text
Dashboard
    ↓
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

Click **OK**.

---

## Step 3: Configure Build Trigger

Navigate to:

```text
Build Triggers
```

Enable:

```text
☑ Build periodically
```

---

## Step 4: Configure the Schedule

Enter the following Cron expression:

```text
*/5 * * * *
```

This schedules the job to run **every 5 minutes**.

---

## Common Jenkins Cron Expressions

| Cron Expression | Schedule |
|----------------|----------|
| `*/5 * * * *` | Every 5 minutes |
| `H/5 * * * *` | Every 5 minutes (Jenkins recommended) |
| `0 * * * *` | Every hour |
| `0 0 * * *` | Every day at midnight |
| `0 9 * * 1-5` | Monday–Friday at 9:00 AM |

Save the job configuration.

---

## Step 5: Automatic Execution

Once the schedule is configured, Jenkins automatically executes the job according to the Cron expression.

No manual **Build Now** action is required.

---

## Step 6: Verify Execution

Navigate to:

```text
Build History
      ↓
Latest Build
      ↓
Console Output
```

Expected output:

```text
Scheduled Job Executed Successfully

Execution Time:
Tue Jul 22 10:35:01 UTC 2026

Finished: SUCCESS
```

---

# 🔄 Workflow

```text
Cron Schedule
      │
      ▼
Jenkins Scheduler
      │
      ▼
Scheduled Job
      │
      ▼
Build Execution
      │
      ▼
Console Output
```

---

# ⏰ Understanding Jenkins Cron Syntax

Jenkins uses a five-field Cron format:

```text
MINUTE   HOUR   DAY_OF_MONTH   MONTH   DAY_OF_WEEK
```

Example:

```text
*/5 * * * *
```

Meaning:

- Every 5 minutes
- Every hour
- Every day
- Every month
- Every day of the week

---

# 💼 Real Project Examples

Scheduled jobs are commonly used to automate:

- Nightly Application Builds
- Automated Regression Testing
- Database Backups
- Log File Cleanup
- Security Scans
- Health Checks
- Disk Usage Monitoring
- Report Generation

---

# 📅 Jenkins Cron Practice

| Requirement | Cron Expression |
|------------|-----------------|
| Every 10 minutes | `*/10 * * * *` |
| Daily at 2:30 AM | `30 2 * * *` |
| Every Monday at 9:00 AM | `0 9 * * 1` |
| Every 5 minutes (Jenkins Hash) | `H/5 * * * *` |
| Monday–Friday at 6:15 PM | `15 18 * * 1-5` |
| Every Sunday at 11:30 PM | `30 23 * * 0` |
| Every 2 hours | `0 */2 * * *` |
| Every 15 minutes | `*/15 * * * *` |
| First day of every month at 12:00 AM | `0 0 1 * *` |
| Every Saturday and Sunday at 8:00 AM | `0 8 * * 0,6` |

---

## ❓ Can Jenkins Run Every 30 Seconds?

**Answer:** No.

Jenkins Cron scheduling supports a **minimum interval of one minute**.

There is **no seconds field** in the Cron format.

Supported format:

```text
Minute
Hour
Day of Month
Month
Day of Week
```

Therefore, scheduling a job every **30 seconds is not possible** using Jenkins Cron.

---

# ✅ Expected Outcome

- Jenkins Scheduled Job created successfully.
- Job executes automatically.
- No manual execution required.
- Console output verifies successful execution.
- Cron-based automation configured successfully.

---

# 🎯 Interview Questions

### Q1. What are Scheduled Jobs in Jenkins?

**Answer:**

Scheduled Jobs automatically execute Jenkins jobs at predefined times using Cron expressions.

---

### Q2. What is Cron syntax?

**Answer:**

Cron syntax defines when a Jenkins job should run using five scheduling fields.

Example:

```text
*/5 * * * *
```

Runs the job every five minutes.

---

### Q3. Difference between `*/5 * * * *` and `H/5 * * * *`?

**Answer:**

- `*/5` runs every 5 minutes starting from minute 0.
- `H/5` distributes executions across different minutes using Jenkins hashing to reduce server load.

---

### Q4. Why are Scheduled Jobs important?

**Answer:**

They automate repetitive tasks such as:

- Nightly Builds
- Automated Testing
- Backups
- Log Cleanup
- Health Checks
- Report Generation

---

### Q5. Where do you configure Scheduled Jobs?

**Answer:**

```text
Job
   ↓
Configure
   ↓
Build Triggers
   ↓
Build Periodically
```

---

# 📌 Key Takeaways

- Jenkins Scheduled Jobs automate repetitive tasks.
- Cron expressions define execution schedules.
- Jenkins recommends using `H` notation to balance workload.
- Scheduled Jobs improve reliability and reduce manual effort.
- They are widely used in enterprise CI/CD environments.

---

# 🎉 Conclusion

Today, I learned how to configure **Jenkins Scheduled Jobs** using **Build Triggers** and **Cron expressions**.

This automation eliminates manual execution of recurring tasks such as builds, testing, backups, and maintenance. Understanding Cron syntax is an essential DevOps skill, as scheduled automation plays a significant role in building reliable and efficient CI/CD pipelines.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and following my DevOps learning journey.

Happy Learning! 🚀
