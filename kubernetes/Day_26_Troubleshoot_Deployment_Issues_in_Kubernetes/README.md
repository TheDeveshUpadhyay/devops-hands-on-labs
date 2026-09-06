# Day 26: Troubleshoot Deployment Issues in Kubernetes

## Solution

### Objective

Learn how to identify and troubleshoot common **Kubernetes Deployment issues** using commands such as:

```bash
kubectl get
kubectl describe
kubectl logs
kubectl rollout status
kubectl get events
```

The goal is to understand **where the problem is happening** and fix it systematically.

---

## Scenario

Suppose you created a Deployment with 3 replicas:

```text
Deployment
    │
    ├── Pod 1
    ├── Pod 2
    └── Pod 3
```

But instead of all Pods running successfully, you see issues such as:

```text
Pending
ImagePullBackOff
CrashLoopBackOff
0/1 Ready
Unavailable replicas
```

As a DevOps Engineer, you should not randomly change YAML.

You should troubleshoot in this order:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod
    ↓
Container
    ↓
Events / Logs
```

---

## Step 1: Create a Directory

Create a directory:

```bash
mkdir day26-deployment-troubleshooting
```

Move into the directory:

```bash
cd day26-deployment-troubleshooting
```

Create the Deployment YAML file:

```bash
vi deployment.yaml
```

---

## Step 2: Create a Deployment with an Intentional Error

Create the following `deployment.yaml` file:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: nginx

  template:
    metadata:
      labels:
        app: nginx

    spec:
      containers:
        - name: nginx-container
          image: nginx-wrong-image:latest

          ports:
            - containerPort: 80
```

### Notice the Image

```text
nginx-wrong-image:latest
```

This image does not exist.

We are intentionally creating an issue so that we can learn how to troubleshoot it.

---

## Step 3: Apply the Deployment

Run:

```bash
kubectl apply -f deployment.yaml
```

Expected output:

```text
deployment.apps/nginx-deployment created
```

---

## Step 4: Check the Deployment

Run:

```bash
kubectl get deployment
```

You may see:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   0/3     3            0
```

This immediately tells you:

```text
Desired Pods = 3
Ready Pods   = 0
Available    = 0
```

Something is wrong.

The Deployment is reporting the symptom, but it does not yet tell us the exact root cause.

---

## Step 5: Check the Pods

Run:

```bash
kubectl get pods
```

You may see:

```text
NAME                                READY   STATUS             RESTARTS
nginx-deployment-xxxxx-aaaaa        0/1     ImagePullBackOff   0
nginx-deployment-xxxxx-bbbbb        0/1     ImagePullBackOff   0
nginx-deployment-xxxxx-ccccc        0/1     ImagePullBackOff   0
```

Now you know the problem is related to the container image.

The important status is:

```text
ImagePullBackOff
```

This means Kubernetes cannot successfully pull the image.

---

## Step 6: Describe the Pod

Copy one Pod name.

Run:

```bash
kubectl describe pod <pod-name>
```

Example:

```bash
kubectl describe pod nginx-deployment-xxxxx-aaaaa
```

Scroll to the bottom and look at the:

```text
Events:
```

section.

You may see messages such as:

```text
Failed to pull image "nginx-wrong-image:latest"

pull access denied

Back-off pulling image
```

This confirms the root cause.

The image name is incorrect.

---

## Step 7: Fix the Image

Open the Deployment YAML:

```bash
vi deployment.yaml
```

Change:

```yaml
image: nginx-wrong-image:latest
```

to:

```yaml
image: nginx:latest
```

Apply the updated Deployment:

```bash
kubectl apply -f deployment.yaml
```

Expected output:

```text
deployment.apps/nginx-deployment configured
```

---

## Step 8: Watch the Deployment Recover

Run:

```bash
kubectl get pods -w
```

The `-w` option means:

```text
-w = watch
```

It continuously watches Pod status changes.

You may see the Pods move through states such as:

```text
ImagePullBackOff
      ↓
ContainerCreating
      ↓
Running
```

Eventually, each Pod should show:

```text
READY   STATUS
1/1     Running
```

For all 3 replicas, you should eventually have:

```text
Pod 1 → 1/1 Running
Pod 2 → 1/1 Running
Pod 3 → 1/1 Running
```

Press:

```text
Ctrl + C
```

to stop the watch command.

---

## Step 9: Verify the Deployment

Run:

```bash
kubectl get deployment
```

Expected output:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

This means all three Pods are ready and available.

You can also verify the rollout status:

```bash
kubectl rollout status deployment/nginx-deployment
```

Expected output:

```text
deployment "nginx-deployment" successfully rolled out
```

---

# Common Deployment Issues

## 1. `ImagePullBackOff`

Example:

```text
STATUS: ImagePullBackOff
```

### Meaning

Kubernetes cannot pull the container image successfully.

### Common Causes

- Wrong image name
- Wrong image tag
- Private registry authentication issue
- Registry unavailable
- Node has no internet access
- Missing `imagePullSecrets`

### Troubleshoot

Run:

```bash
kubectl describe pod <pod-name>
```

Look at the Events section.

You may see errors such as:

```text
Failed to pull image
pull access denied
Unauthorized
NotFound
```

---

## 2. `CrashLoopBackOff`

Example:

```text
STATUS: CrashLoopBackOff
```

### Meaning

The container starts, crashes, and Kubernetes keeps restarting it.

The flow looks like:

```text
Container Starts
      ↓
Application Crashes
      ↓
Kubernetes Restarts Container
      ↓
Container Crashes Again
      ↓
Backoff Delay
      ↓
Retry
```

### Check Logs

Run:

```bash
kubectl logs <pod-name>
```

If needed, check logs from the previous crashed container:

```bash
kubectl logs <pod-name> --previous
```

### Common Causes

- Application error
- Wrong command
- Missing environment variable
- Wrong configuration
- Database connection failure
- Missing dependency
- Permission issue
- Missing file

---

## 3. Pod `Pending`

Example:

```text
STATUS: Pending
```

### Meaning

The Pod has been accepted by Kubernetes but has not been successfully scheduled or started.

### Check

Run:

```bash
kubectl describe pod <pod-name>
```

Look at the Events section.

### Common Causes

- Insufficient CPU
- Insufficient memory
- PVC not bound
- Node selector mismatch
- Node affinity mismatch
- Taints and tolerations issue
- No suitable node available

For example:

```text
0/3 nodes are available:
3 Insufficient memory
```

This means the cluster does not have enough memory to schedule the Pod.

---

## 4. Deployment Shows `0/3 Ready`

Example:

```text
READY
0/3
```

Check the Pods:

```bash
kubectl get pods
```

Then inspect a failing Pod:

```bash
kubectl describe pod <pod-name>
```

If the container starts, also check:

```bash
kubectl logs <pod-name>
```

The Deployment is only reporting the symptom.

The real problem is often inside the Pod or container.

Think of it like:

```text
Deployment
    ↓
Reports Problem
    ↓
Pod
    ↓
Contains Root Cause
```

---

## 5. Container Running but Pod Not Ready

You may see:

```text
READY   STATUS
0/1     Running
```

This means the container process is running, but the Pod is not considered Ready.

A common cause is a failing **readiness probe**.

Check:

```bash
kubectl describe pod <pod-name>
```

Look for:

```text
Readiness probe failed
```

For example:

```text
Readiness probe failed:
HTTP probe failed with statuscode: 500
```

This means the application process is running, but Kubernetes does not consider it ready to receive traffic.

---

# Troubleshooting Flow

Use this order:

```text
1. Check Deployment
        ↓
kubectl get deployment

2. Check ReplicaSet
        ↓
kubectl get replicasets

3. Check Pods
        ↓
kubectl get pods

4. Describe Pod
        ↓
kubectl describe pod <pod-name>

5. Check Logs
        ↓
kubectl logs <pod-name>

6. Check Previous Logs
        ↓
kubectl logs <pod-name> --previous

7. Check Events
        ↓
kubectl get events

8. Check Rollout
        ↓
kubectl rollout status deployment/<name>

9. Fix Configuration
        ↓
kubectl apply -f deployment.yaml

10. Verify Recovery
        ↓
kubectl get pods
```

The important lesson is:

> **Do not randomly edit the Deployment. First identify which layer is failing.**

---

# Important Troubleshooting Commands

| Command | Purpose |
|---|---|
| `kubectl get deployment` | Check Deployment status |
| `kubectl describe deployment nginx-deployment` | View detailed Deployment information |
| `kubectl get replicasets` | Check ReplicaSets |
| `kubectl get pods` | Check Pod status |
| `kubectl get pods -o wide` | Check Pod IP, Node, and additional details |
| `kubectl describe pod <pod-name>` | Check detailed Pod information and Events |
| `kubectl logs <pod-name>` | Check application logs |
| `kubectl logs <pod-name> --previous` | Check logs from the previous crashed container |
| `kubectl get events --sort-by=.metadata.creationTimestamp` | Check recent cluster events |
| `kubectl rollout status deployment/nginx-deployment` | Check rollout status |
| `kubectl rollout history deployment/nginx-deployment` | Check Deployment revisions |
| `kubectl rollout undo deployment/nginx-deployment` | Roll back a bad Deployment |
| `kubectl get pods -w` | Watch Pod status changes continuously |

---

# Understanding `kubectl get pods -w`

The command:

```bash
kubectl get pods -w
```

means:

```text
kubectl get pods
      +
Watch continuously
```

Normally:

```bash
kubectl get pods
```

shows the Pod status once.

For example:

```text
NAME        READY   STATUS
nginx-pod   0/1     ContainerCreating
```

But:

```bash
kubectl get pods -w
```

keeps watching for changes.

You may see:

```text
nginx-pod   0/1   ImagePullBackOff
nginx-pod   0/1   ContainerCreating
nginx-pod   1/1   Running
```

This is useful when you have just fixed a Deployment and want to watch the Pods recover.

Press:

```text
Ctrl + C
```

when you want to stop watching.

---

# Real Project Troubleshooting Example

Suppose a production Deployment shows:

```text
READY
2/5
```

Do not immediately restart the Deployment.

First run:

```bash
kubectl get pods
```

Suppose you see:

```text
Pod 1 → Running
Pod 2 → Running
Pod 3 → CrashLoopBackOff
Pod 4 → CrashLoopBackOff
Pod 5 → Running
```

Now the problem is narrowed down to Pods 3 and 4.

Check a failing Pod:

```bash
kubectl logs <failing-pod>
```

You may find:

```text
Database connection refused
```

Now the real problem is probably:

```text
Application
    ↓
Database Connectivity
```

not necessarily Kubernetes itself.

The next checks may include:

```text
Database availability
        ↓
Database hostname
        ↓
Port connectivity
        ↓
Network policy
        ↓
Security Group
        ↓
Secret / Environment variable
        ↓
DNS
```

That is the troubleshooting mindset expected from a DevOps Engineer.

---

# Deployment Troubleshooting Mindset

Instead of thinking:

```text
Application Broken
      ↓
Restart Everything
```

Think:

```text
Application Broken
      ↓
Check Deployment
      ↓
Check ReplicaSet
      ↓
Check Pods
      ↓
Check Events
      ↓
Check Logs
      ↓
Identify Root Cause
      ↓
Fix
      ↓
Verify
```

This approach is safer and more effective in production.

---

# Expected Outcome

After completing this task, you should be able to:

- Create a Deployment with an intentional issue.
- Identify `ImagePullBackOff`.
- Find the root cause using `kubectl describe`.
- Correct the image configuration.
- Watch Pods recover using `kubectl get pods -w`.
- Verify Deployment rollout status.
- Understand common Deployment failures.
- Troubleshoot `CrashLoopBackOff`.
- Troubleshoot `Pending` Pods.
- Understand why a Pod can be `Running` but not `Ready`.
- Use Kubernetes Events and logs to identify root causes.
- Follow a structured Deployment troubleshooting approach.

---

# Interview Questions

## Q1. How do you troubleshoot a failed Kubernetes Deployment?

### Answer

I first check the Deployment status using:

```bash
kubectl get deployment
```

Then I inspect the Pods using:

```bash
kubectl get pods
```

For failing Pods, I use:

```bash
kubectl describe pod <pod-name>
```

to check Events and configuration.

Then I check application logs:

```bash
kubectl logs <pod-name>
```

If the container has already crashed and restarted, I use:

```bash
kubectl logs <pod-name> --previous
```

I also verify rollout status:

```bash
kubectl rollout status deployment/<deployment-name>
```

and recent cluster events:

```bash
kubectl get events --sort-by=.metadata.creationTimestamp
```

Then I identify the root cause, fix the configuration, and verify that the Deployment recovers successfully.

---

## Q2. What is `ImagePullBackOff`?

### Answer

`ImagePullBackOff` means Kubernetes failed to pull the container image and is waiting before retrying.

Common causes include:

- Wrong image name
- Wrong tag
- Private registry authentication issue
- Missing registry credentials
- Registry unavailable

To troubleshoot:

```bash
kubectl describe pod <pod-name>
```

---

## Q3. What is `CrashLoopBackOff`?

### Answer

`CrashLoopBackOff` means the container repeatedly starts, crashes, and Kubernetes keeps restarting it with increasing retry delays.

Check:

```bash
kubectl logs <pod-name>
```

and:

```bash
kubectl logs <pod-name> --previous
```

---

## Q4. What does `Pending` mean?

### Answer

`Pending` means the Pod has been accepted by Kubernetes but cannot yet be scheduled or started.

Common reasons include:

- Insufficient CPU
- Insufficient memory
- PVC not bound
- Node selector mismatch
- Taints and tolerations
- Node affinity rules

Check:

```bash
kubectl describe pod <pod-name>
```

---

## Q5. What command do you use first for Pod troubleshooting?

### Answer

A strong starting point is:

```bash
kubectl get pods
```

This shows which Pods are healthy and which are failing.

Then:

```bash
kubectl describe pod <pod-name>
```

and:

```bash
kubectl logs <pod-name>
```

---

## Q6. How do you check logs from a container that already crashed?

### Answer

Use:

```bash
kubectl logs <pod-name> --previous
```

This shows logs from the previous instance of the container before it restarted.

---

## Q7. How do you roll back a bad Deployment?

### Answer

Use:

```bash
kubectl rollout undo deployment/<deployment-name>
```

Example:

```bash
kubectl rollout undo deployment/nginx-deployment
```

You can check revision history using:

```bash
kubectl rollout history deployment/nginx-deployment
```

---

# Key Takeaway

The most important lesson from this lab is:

```text
Deployment Issue
      ↓
Do Not Guess
      ↓
Check Deployment
      ↓
Check ReplicaSet
      ↓
Check Pods
      ↓
Describe Pod
      ↓
Check Events
      ↓
Check Logs
      ↓
Find Root Cause
      ↓
Fix
      ↓
Verify
```

A Kubernetes Deployment may show:

```text
0/3 Ready
```

but that is only the symptom.

The actual root cause may be:

```text
Wrong Image
     ↓
ImagePullBackOff
```

or:

```text
Application Crash
     ↓
CrashLoopBackOff
```

or:

```text
Insufficient Resources
     ↓
Pending
```

or:

```text
Readiness Probe Failure
     ↓
Running but 0/1 Ready
```

The correct troubleshooting approach is to move from the higher-level Kubernetes object down to the actual container:

```text
Deployment
    ↓
ReplicaSet
    ↓
Pod
    ↓
Container
    ↓
Events
    ↓
Logs
    ↓
Root Cause
```

---

# Day 26 Concept

```text
Create Deployment
       ↓
Introduce an Error
       ↓
Check Deployment Status
       ↓
Check Pod Status
       ↓
Describe Failing Pod
       ↓
Read Events
       ↓
Identify Root Cause
       ↓
Fix Configuration
       ↓
Apply Updated YAML
       ↓
Watch Pods Recover
       ↓
Verify Rollout
       ↓
Deployment Healthy ✅
```

**Day 26 Completed: Troubleshoot Deployment Issues in Kubernetes ✅**
