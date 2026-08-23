# Day 19: Execute Rolling Updates in Kubernetes

## Solution

### Objective

Learn how Kubernetes performs a **Rolling Update** to gradually replace old Pods with new Pods without stopping the entire application.

---

## Scenario

Yesterday, we learned how to update the image of a Deployment.

Today, we will specifically understand **Rolling Updates**.

Suppose your application is currently running:

```text
Deployment
    │
    ├── Pod 1 → nginx:1.26
    ├── Pod 2 → nginx:1.26
    └── Pod 3 → nginx:1.26
```

Now you want to deploy:

```text
nginx:1.27
```

Kubernetes doesn't normally delete all 3 old Pods at once.

Instead, it gradually replaces them:

```text
Old version → New version

Pod 1 → nginx:1.26
Pod 2 → nginx:1.26
Pod 3 → nginx:1.26

          ↓

Pod 1 → nginx:1.27
Pod 2 → nginx:1.26
Pod 3 → nginx:1.26

          ↓

Pod 1 → nginx:1.27
Pod 2 → nginx:1.27
Pod 3 → nginx:1.26

          ↓

Pod 1 → nginx:1.27
Pod 2 → nginx:1.27
Pod 3 → nginx:1.27
```

That gradual replacement is called a **Rolling Update**.

---

## Prerequisites

- Kubernetes cluster available
- `kubectl` installed
- `kubectl` connected to your cluster

Check the cluster nodes:

```bash
kubectl get nodes
```

---

## Step 1: Create a Directory

```bash
mkdir day19-rolling-update
cd day19-rolling-update
```

Create the Deployment file:

```bash
vi deployment.yaml
```

---

## Step 2: Create Deployment YAML

Add the following configuration:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: nginx-deployment

spec:
  replicas: 3

  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
      maxSurge: 1

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

Save the file.

---

## Step 3: Understand RollingUpdate

This part is important:

```yaml
strategy:
  type: RollingUpdate
```

It tells Kubernetes:

> Update the Pods gradually instead of replacing everything at once.

### 3.1 `maxUnavailable`

```yaml
maxUnavailable: 1
```

This means:

> During the update, at most **1 Pod can be unavailable** compared with the desired number.

We have:

```text
replicas: 3
```

So Kubernetes tries to keep at least:

```text
3 - 1 = 2 Pods
```

available during the rollout.

---

### 3.2 `maxSurge`

```yaml
maxSurge: 1
```

This means:

> Kubernetes can temporarily create **1 additional Pod** above the desired number during the update.

So with:

```text
replicas: 3
maxSurge: 1
```

Kubernetes can temporarily have:

```text
4 Pods
```

during the rollout.

---

## Step 4: Create the Deployment

Run:

```bash
kubectl apply -f deployment.yaml
```

Expected:

```text
deployment.apps/nginx-deployment created
```

---

## Step 5: Check the Deployment

Run:

```bash
kubectl get deployment
```

Expected:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

This means:

```text
READY       → 3/3
UP-TO-DATE  → 3
AVAILABLE   → 3
```

All 3 desired Pods are ready and available.

---

## Step 6: Check the Pods

Run:

```bash
kubectl get pods
```

You should have 3 Pods:

```text
NAME                              READY   STATUS    RESTARTS
nginx-deployment-xxxxx-aaaaa      1/1     Running   0
nginx-deployment-xxxxx-bbbbb      1/1     Running   0
nginx-deployment-xxxxx-ccccc      1/1     Running   0
```

All three Pods are running:

```text
nginx:1.26
```

---

## Step 7: Update the Application

Now change the Nginx version from:

```text
nginx:1.26
```

to:

```text
nginx:1.27
```

Edit the YAML file:

```bash
vi deployment.yaml
```

Change:

```yaml
image: nginx:1.26
```

to:

```yaml
image: nginx:1.27
```

Apply the updated configuration:

```bash
kubectl apply -f deployment.yaml
```

Expected:

```text
deployment.apps/nginx-deployment configured
```

---

## Step 8: Watch the Rolling Update

This is one of the most useful commands for today's task:

```bash
kubectl rollout status deployment/nginx-deployment
```

You may see:

```text
Waiting for deployment "nginx-deployment" rollout to finish...
```

Eventually:

```text
deployment "nginx-deployment" successfully rolled out
```

This confirms that the Rolling Update completed successfully.

---

## Step 9: Watch Pods During the Update

You can watch the Pods continuously:

```bash
kubectl get pods -w
```

The `-w` means:

```text
Watch for changes
```

You may see old Pods being terminated and new Pods being created.

For example:

```text
Old Pod → nginx:1.26
New Pod → nginx:1.27
```

Eventually, all Pods will run the new version.

Press:

```text
Ctrl + C
```

to stop watching.

---

## Step 10: Verify the New Image

Check the Deployment:

```bash
kubectl describe deployment nginx-deployment
```

Look for:

```text
Image: nginx:1.27
```

You can also check individual Pods:

```bash
kubectl describe pod <pod-name>
```

Look for:

```text
Image: nginx:1.27
```

---

## Step 11: Check ReplicaSets

Run:

```bash
kubectl get replicasets
```

You should notice that there are typically **two ReplicaSets** during or after a Deployment update:

```text
NAME                        DESIRED   CURRENT   READY
nginx-deployment-old        0         0         0
nginx-deployment-new        3         3         3
```

### Why?

Because Kubernetes uses a new ReplicaSet for the new version.

The structure is:

```text
Deployment
    │
    ├── Old ReplicaSet
    │       └── Old Pods
    │
    └── New ReplicaSet
            └── New Pods
```

During the rollout, Kubernetes gradually:

```text
Scales down old ReplicaSet
            ↓
Scales up new ReplicaSet
```

This is how Kubernetes manages the transition from the old version to the new version.

---

## Step 12: Check Rollout History

Run:

```bash
kubectl rollout history deployment/nginx-deployment
```

You may see:

```text
deployment.apps/nginx-deployment

REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```

Each Deployment revision represents a change to the Deployment's Pod template.

---

## Step 13: Roll Back the Update

Suppose `nginx:1.27` has a problem.

You can roll back to the previous version:

```bash
kubectl rollout undo deployment/nginx-deployment
```

Then check the rollout status:

```bash
kubectl rollout status deployment/nginx-deployment
```

Expected:

```text
deployment "nginx-deployment" successfully rolled out
```

Check the image again:

```bash
kubectl describe deployment nginx-deployment
```

You should now see the previous version again.

---

# Rolling Update Flow

```text
Current Deployment
        │
        ▼
3 Pods → nginx:1.26
        │
        │ Update image
        ▼
New ReplicaSet created
        │
        ▼
New Pod → nginx:1.27
        │
        ▼
Old Pod removed
        │
        ▼
New Pod → nginx:1.27
        │
        ▼
Old Pod removed
        │
        ▼
All Pods → nginx:1.27
```

---

# Rolling Update vs Recreate

Kubernetes supports different Deployment strategies.

## RollingUpdate

```text
Old Pods
    ↓
Gradually replaced
    ↓
New Pods
```

The application can remain available during the update.

---

## Recreate

```text
Delete old Pods
        ↓
Create new Pods
```

There can be downtime.

For most normal application deployments, **RollingUpdate is preferred**.

---

# Important Commands

| Command | Purpose |
|---|---|
| `kubectl apply -f deployment.yaml` | Create or update the Deployment |
| `kubectl get deployment` | Check Deployment status |
| `kubectl get pods` | Check Pods |
| `kubectl get pods -w` | Watch Pod changes |
| `kubectl rollout status deployment/nginx-deployment` | Monitor rollout |
| `kubectl rollout history deployment/nginx-deployment` | View rollout revisions |
| `kubectl rollout undo deployment/nginx-deployment` | Roll back to the previous version |
| `kubectl get replicasets` | View old and new ReplicaSets |

---

# Expected Outcome

- Deployment created with 3 replicas.
- Nginx `1.26` initially deployed.
- Image updated to Nginx `1.27`.
- Kubernetes performs a Rolling Update.
- New Pods are gradually created.
- Old Pods are gradually removed.
- Rollout status verified.
- Rollout history checked.
- Rollback tested successfully.

---

# Interview Questions

## Q1. What is a Rolling Update?

**Answer:**

A Rolling Update is a deployment strategy where Kubernetes gradually replaces old application Pods with new Pods instead of stopping all Pods at once.

---

## Q2. What is the default Deployment strategy?

**Answer:**

The default strategy for a Kubernetes Deployment is:

```text
RollingUpdate
```

---

## Q3. What is `maxUnavailable`?

**Answer:**

`maxUnavailable` specifies the maximum number of Pods that can be unavailable during a Rolling Update.

---

## Q4. What is `maxSurge`?

**Answer:**

`maxSurge` specifies how many additional Pods Kubernetes can temporarily create above the desired replica count during a Rolling Update.

---

## Q5. How do you check whether a rollout completed?

```bash
kubectl rollout status deployment/nginx-deployment
```

---

## Q6. How do you roll back a Deployment?

```bash
kubectl rollout undo deployment/nginx-deployment
```

---

## Q7. Why are ReplicaSets involved in a Rolling Update?

**Answer:**

The Deployment creates a new ReplicaSet for the new Pod version and gradually scales down the old ReplicaSet while scaling up the new one.

---

# Key Takeaways

- **Rolling Update = gradually replace old Pods with new Pods.**
- Kubernetes Deployments use **RollingUpdate by default**.
- `maxUnavailable` controls how many Pods can be unavailable.
- `maxSurge` controls temporary extra Pods.
- A new **ReplicaSet** is created for the new version.
- The old ReplicaSet is gradually scaled down.
- `kubectl rollout status` monitors the update.
- `kubectl rollout undo` rolls back the Deployment.
- This approach helps achieve **minimal or zero application downtime** during normal updates.

---

# Conclusion

Today, I learned how Kubernetes performs a **Rolling Update** to safely replace an old application version with a new version.

The key flow is:

```text
Deployment
    ↓
New ReplicaSet
    ↓
New Pods
    ↓
Gradually remove old Pods
    ↓
All Pods running new version
```

Understanding `maxUnavailable`, `maxSurge`, ReplicaSets, rollout history, and rollback is essential for managing application releases in Kubernetes.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

Happy Learning! 🚀
