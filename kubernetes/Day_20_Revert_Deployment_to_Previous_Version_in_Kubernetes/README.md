# Day 20: Revert Deployment to Previous Version in Kubernetes

## Solution

### Objective

Learn how to **rollback a Kubernetes Deployment** to a previous version when a new deployment causes problems.

In real-world environments, deployments can fail because of:

- Application bugs
- Incorrect container images
- Configuration mistakes
- Failed application updates
- Unexpected behavior after release

Kubernetes keeps a **revision history** of Deployments, which allows us to roll back to a previous working version.

---

## Scenario

Suppose we have an Nginx Deployment running version:

```text
nginx:1.26
```

We update it to:

```text
nginx:1.27
```

But after the update, we discover a problem.

We want to return to the previous working version.

The process is:

```text
Version 1.26
     ↓
Update
     ↓
Version 1.27
     ↓
Problem detected ❌
     ↓
Rollback
     ↓
Version 1.26 ✅
```

---

## Step 1: Create the Deployment

Create a file:

```bash
vi deployment.yaml
```

Add the following configuration:

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
          image: nginx:1.26

          ports:
            - containerPort: 80
```

Apply the Deployment:

```bash
kubectl apply -f deployment.yaml
```

---

## Step 2: Verify the Deployment

Check the Deployment:

```bash
kubectl get deployment
```

Check the Pods:

```bash
kubectl get pods
```

Check the Deployment details:

```bash
kubectl describe deployment nginx-deployment
```

You should see:

```text
Image: nginx:1.26
```

---

## Step 3: Update the Deployment

Now suppose we want to upgrade Nginx:

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.27
```

Kubernetes will perform a **rolling update**.

Check the rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

Check the Deployment:

```bash
kubectl describe deployment nginx-deployment
```

The image should now be:

```text
nginx:1.27
```

---

## Step 4: Check Deployment History

Kubernetes maintains revision history for the Deployment.

Run:

```bash
kubectl rollout history deployment/nginx-deployment
```

Example:

```text
deployment.apps/nginx-deployment

REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

Here:

```text
Revision 1 → nginx:1.26
Revision 2 → nginx:1.27
```

---

## Step 5: Rollback to the Previous Version

If version `1.27` has a problem, we can undo the latest rollout:

```bash
kubectl rollout undo deployment/nginx-deployment
```

This tells Kubernetes:

> Go back to the previous Deployment revision.

Check the rollout:

```bash
kubectl rollout status deployment/nginx-deployment
```

---

## Step 6: Verify the Rollback

Check the Deployment:

```bash
kubectl describe deployment nginx-deployment
```

The image should now be:

```text
nginx:1.26
```

You can also check the Pods:

```bash
kubectl get pods
```

Kubernetes will gradually replace the newer Pods with Pods running the previous version.

---

## Rollback to a Specific Revision

Sometimes you don't want to go back only one version.

First check the history:

```bash
kubectl rollout history deployment/nginx-deployment
```

For example:

```text
REVISION
1
2
3
```

To inspect a particular revision:

```bash
kubectl rollout history deployment/nginx-deployment --revision=2
```

To roll back specifically to revision `2`:

```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

---

## Important Commands

| Command | Purpose |
|---|---|
| `kubectl rollout history deployment/nginx-deployment` | View Deployment revision history |
| `kubectl rollout history deployment/nginx-deployment --revision=2` | View details of a specific revision |
| `kubectl rollout undo deployment/nginx-deployment` | Roll back to the previous revision |
| `kubectl rollout undo deployment/nginx-deployment --to-revision=2` | Roll back to a specific revision |
| `kubectl rollout status deployment/nginx-deployment` | Monitor rollout progress |
| `kubectl get deployment` | Check Deployment status |
| `kubectl describe deployment nginx-deployment` | Inspect Deployment details |

---

## What Actually Happens During Rollback?

Suppose we have:

```text
Revision 1
nginx:1.26
```

Then we update:

```text
Revision 2
nginx:1.27
```

After detecting a problem:

```bash
kubectl rollout undo deployment/nginx-deployment
```

Kubernetes changes the Deployment back to the previous Pod template:

```text
nginx:1.27 ❌
      ↓
Rollback
      ↓
nginx:1.26 ✅
```

Kubernetes then performs the replacement according to the Deployment's **rolling-update strategy**.

### Important

Rollback does **not** simply mean:

> "Restore old Pods."

It changes the Deployment's **Pod template** back to the previous revision, and Kubernetes creates/replaces Pods to match that desired state.

---

## Interview Answer

### Q: How do you rollback a Kubernetes Deployment?

If a new Deployment version causes an issue, I first check the rollout history using:

```bash
kubectl rollout history deployment/<deployment-name>
```

Then I can use:

```bash
kubectl rollout undo deployment/<deployment-name>
```

to roll back to the previous revision.

If I need to roll back to a specific revision, I use:

```bash
kubectl rollout undo deployment/<deployment-name> --to-revision=<revision-number>
```

Finally, I verify the rollback using:

```bash
kubectl rollout status deployment/<deployment-name>
```

and check the Pods and container image to confirm that the expected version is running.

---

## Key Takeaway

The main commands to remember are:

### Check History

```bash
kubectl rollout history deployment/nginx-deployment
```

### Rollback Previous Version

```bash
kubectl rollout undo deployment/nginx-deployment
```

### Rollback to Specific Revision

```bash
kubectl rollout undo deployment/nginx-deployment --to-revision=2
```

### Verify

```bash
kubectl rollout status deployment/nginx-deployment
```

---

## Day 20 Concept

```text
Deploy
   ↓
New Version
   ↓
Problem ❌
   ↓
Check Revision History
   ↓
Rollback
   ↓
Previous Working Version ✅
```

### Final Takeaway

**Kubernetes Deployment rollback allows us to quickly return to a previous working version when a new application release causes problems.**
