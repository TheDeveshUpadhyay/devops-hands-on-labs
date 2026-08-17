# Day 17: Deploy Applications with Kubernetes Deployments

## Solution

### Objective

Learn how to create a **Kubernetes Deployment** to manage application Pods, configure replicas, and understand how Kubernetes automatically maintains the desired number of Pods.

---

### Scenario

If we delete a Pod:

```bash
kubectl delete pod nginx-pod
```

It **does not come back**.

In a real project, we don't want to manually recreate failed Pods.

That's where a **Deployment** comes in.

A Deployment maintains the desired number of Pods.

```text
Deployment
     │
     ├── Pod
     │     └── Nginx
     │
     ├── Pod
     │     └── Nginx
     │
     └── Pod
           └── Nginx
```

---

### Prerequisites

- Kubernetes cluster available
- `kubectl` installed
- `kubectl` connected to the cluster

Check:

```bash
kubectl get nodes
```

Expected:

```text
NAME          STATUS   ROLES
controlplane  Ready    control-plane
node01        Ready    <none>
```

---

### Step 1: Create a Directory

```bash
mkdir day17-deployment
cd day17-deployment
```

---

### Step 2: Create Deployment YAML

Create the YAML file:

```bash
vi deployment.yaml
```

Write the following Deployment manifest:

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
          image: nginx:latest
          ports:
            - containerPort: 80
```

Save the file.

---

### Step 3: Create the Deployment

Run:

```bash
kubectl apply -f deployment.yaml
```

Expected:

```text
deployment.apps/nginx-deployment created
```

---

### Step 4: Check the Pods and Deployment

Check the Pods:

```bash
kubectl get pods
```

Expected:

```text
NAME                               READY   STATUS    RESTARTS
nginx-deployment-xxxxxxxxxx-xxxxx  1/1     Running   0
nginx-deployment-xxxxxxxxxx-yyyyy  1/1     Running   0
nginx-deployment-xxxxxxxxxx-zzzzz  1/1     Running   0
```

Check the Deployment:

```bash
kubectl get deployment
```

Expected:

```text
NAME               READY   UP-TO-DATE   AVAILABLE
nginx-deployment   3/3     3            3
```

The important part is:

```text
3/3
```

It means:

```text
3 desired Pods
3 Pods are ready
```

---

### Step 5: Understand the Deployment → ReplicaSet → Pod Relationship

The actual hierarchy is:

```text
Deployment
     │
     ▼
ReplicaSet
     │
     ├── Pod
     ├── Pod
     └── Pod
```

The Deployment doesn't directly manage the Pods.

The Deployment manages a **ReplicaSet**, and the ReplicaSet manages the Pods.

So:

```text
Deployment
     │
     │ manages
     ▼
ReplicaSet
     │
     │ manages
     ▼
Pods
```

---

### Step 6: Check the ReplicaSet

Run:

```bash
kubectl get replicasets
```

Expected:

```text
NAME                      DESIRED   CURRENT   READY
nginx-deployment-xxxxxxxx 3         3         3
```

The ReplicaSet is responsible for maintaining **3 Pods**.

---

### Step 7: Delete One Pod

Check the Pods:

```bash
kubectl get pods
```

Delete any one Pod:

```bash
kubectl delete pod nginx-deployment-c6bb9648b-m6dhm
```

Now immediately run:

```bash
kubectl get pods
```

You should still have:

```text
3 Pods
```

### Why?

Because the ReplicaSet noticed:

```text
Desired = 3
Current = 2
```

The ReplicaSet automatically creates a replacement Pod to maintain the desired number of replicas.

---

### Step 8: Scale the Deployment

Suppose your application suddenly receives more traffic.

You currently have:

```text
3 Pods
```

Scale to 5:

```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Check:

```bash
kubectl get pods
```

You should eventually see:

```text
5 Pods
```

Check the Deployment:

```bash
kubectl get deployment nginx-deployment
```

Expected:

```text
READY
5/5
```

---

### Step 9: Scale Down

Scale back to 2:

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

Check:

```bash
kubectl get pods
```

You should eventually have:

```text
2 Pods
```

---

### Step 10: Update the Image

Suppose you want to change the Nginx image.

Update the Deployment:

```bash
kubectl set image deployment/nginx-deployment nginx-container=nginx:1.27
```

Check:

```bash
kubectl rollout status deployment/nginx-deployment
```

Expected:

```text
deployment "nginx-deployment" successfully rolled out
```

---

### Step 11: Check Rollout History

Run:

```bash
kubectl rollout history deployment/nginx-deployment
```

You can see previous Deployment revisions.

---

### Step 12: Roll Back the Deployment

If the new version causes a problem:

```bash
kubectl rollout undo deployment/nginx-deployment
```

Check:

```bash
kubectl rollout status deployment/nginx-deployment
```

This is one of the major reasons Deployments are used in real applications.

---

### Step 13: View Deployment Details

Run:

```bash
kubectl describe deployment nginx-deployment
```

This shows:

- Number of replicas
- Selector
- Pod template
- Image
- Events
- ReplicaSet information

---

### Step 14: Delete the Deployment

Run:

```bash
kubectl delete deployment nginx-deployment
```

Check:

```bash
kubectl get pods
```

The Pods created by that Deployment should also be removed.

---

### Deployment Flow

```text
Developer
    │
    ▼
Deployment YAML
    │
    ▼
Deployment
    │
    ▼
ReplicaSet
    │
    ├──────────────┐
    ▼              ▼
  Pod 1          Pod 2
    │
    ▼
Nginx Container
```

---

### Expected Outcome

- Deployment YAML created successfully.
- Nginx Deployment created.
- 3 replicas running.
- ReplicaSet created automatically.
- Deleted Pod automatically recreated.
- Deployment scaled up and down.
- Application image updated.
- Rollout status checked.
- Deployment rollback performed successfully.
