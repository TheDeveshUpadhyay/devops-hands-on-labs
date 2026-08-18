# Day 18: Set Resource Limits in Kubernetes Pods

## Solution

### Objective

Learn how to set **CPU and memory requests and limits** for containers running inside Kubernetes Pods.

This is important because without resource limits, one application can consume too many resources on a Kubernetes node and affect other applications.

---

### Scenario

Suppose your Kubernetes node has:

```text
Node

CPU:    2 CPU
Memory: 4 GB
```

And you have multiple applications:

```text
Node
 │
 ├── Pod 1 → Application
 │
 ├── Pod 2 → Application
 │
 └── Pod 3 → Application
```

If Pod 1 consumes excessive CPU or memory, other Pods can be affected.

So we define:

```text
Requests → Minimum resources needed

Limits   → Maximum resources allowed
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
mkdir day18-resource-limits
cd day18-resource-limits
```

Create the YAML file:

```bash
vi pod.yaml
```

---

### Step 2: Create the Pod YAML

Create the following Pod manifest:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: resource-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"

        limits:
          cpu: "500m"
          memory: "256Mi"
```

Save the file.

---

### Step 3: Understand Requests and Limits

This is the most important part of today's task.

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "256Mi"
```

Think of it like this:

```text
Requests
   ↓
Resources Kubernetes should reserve/consider for scheduling

Limits
   ↓
Maximum resources the container can use
```

For our example:

```text
CPU Request     = 100m
CPU Limit       = 500m

Memory Request  = 128Mi
Memory Limit    = 256Mi
```

---

### Step 4: Create the Pod

Run:

```bash
kubectl apply -f pod.yaml
```

Expected:

```text
pod/resource-pod created
```

---

### Step 5: Check the Pod

Run:

```bash
kubectl get pods
```

Expected:

```text
NAME           READY   STATUS    RESTARTS
resource-pod   1/1     Running   0
```

---

### Step 6: Verify Resource Configuration

Run:

```bash
kubectl describe pod resource-pod
```

Look for the **Containers** section.

You should see something similar to:

```text
Containers:
  nginx-container:
    Image:          nginx:latest

    Limits:
      cpu:          500m
      memory:       256Mi

    Requests:
      cpu:          100m
      memory:       128Mi
```

This confirms that Kubernetes accepted your resource configuration.

---

### Step 7: Check the YAML Directly

You can also run:

```bash
kubectl get pod resource-pod -o yaml
```

Search for:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "256Mi"
```

This shows the resource configuration applied to the Pod.

---

### Step 8: Understand Scheduling

This is where **requests** become especially important.

Suppose your node has:

```text
CPU:    2 CPU
Memory: 4 GiB
```

Your Pod requests:

```text
CPU:    0.1 CPU
Memory: 128 MiB
```

Kubernetes uses these requested resources when deciding whether the Pod can fit on a node.

Simplified:

```text
Node
 │
 ├── Pod 1 → request: 0.1 CPU
 │
 ├── Pod 2 → request: 0.1 CPU
 │
 ├── Pod 3 → request: 0.1 CPU
 │
 └── ...
```

The scheduler considers the **requests** when deciding where to place the Pod.

For example:

```text
Node has:

CPU Available    = 2 CPU
Memory Available = 4 GiB

Pod requires:

CPU    = 0.1 CPU
Memory = 128 MiB
```

The Pod can fit on this node because sufficient requested resources are available.

---

### Step 9: Requests vs Limits

This distinction is extremely important.

| Resource | Request | Limit |
|----------|---------|-------|
| CPU | Resource needed/requested for scheduling | Maximum CPU allowed |
| Memory | Resource needed/requested for scheduling | Maximum memory allowed |

Think:

```text
REQUEST = "I need this much."

LIMIT = "Don't let me use more than this."
```

For our example:

```text
CPU

Request = 100m
Limit   = 500m
```

```text
Memory

Request = 128Mi
Limit   = 256Mi
```

---

### Memory Example

Suppose:

```yaml
limits:
  memory: "256Mi"
```

If the container tries to consume substantially more memory than allowed, it can be terminated with an **OOMKilled** result.

You may see:

```text
Reason: OOMKilled
```

OOM means:

```text
Out Of Memory
```

The basic flow is:

```text
Container
    │
    ▼
Memory usage increases
    │
    ▼
Memory Limit = 256Mi
    │
    ▼
Container exceeds memory limit
    │
    ▼
OOMKilled
```

---

### Important: Pod vs Container Resources

Resource settings are configured under the **container**:

```yaml
spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"

        limits:
          cpu: "500m"
          memory: "256Mi"
```

A Pod can contain multiple containers, and each container can have its own resource requests and limits.

For example:

```text
Pod
│
├── Container 1
│    ├── CPU request
│    ├── CPU limit
│    ├── Memory request
│    └── Memory limit
│
└── Container 2
     ├── CPU request
     ├── CPU limit
     ├── Memory request
     └── Memory limit
```

---

### Important Commands

| Command | Purpose |
|---------|---------|
| `kubectl apply -f pod.yaml` | Create the Pod |
| `kubectl get pods` | Check Pod status |
| `kubectl describe pod resource-pod` | View Pod details and resource configuration |
| `kubectl get pod resource-pod -o yaml` | View the complete Pod YAML |
| `kubectl delete pod resource-pod` | Delete the Pod |

---

### Expected Outcome

- Pod created with resource requests.
- CPU request configured.
- Memory request configured.
- CPU limit configured.
- Memory limit configured.
- Resource configuration verified using `kubectl describe`.
- Resource configuration verified using `kubectl get pod -o yaml`.
- Difference between requests and limits understood.
- Kubernetes scheduling behavior understood.
- Memory limit and `OOMKilled` behavior understood.
- Pod vs container resource configuration understood.

---

### Interview Questions

#### Q1. What are resource requests in Kubernetes?

**Answer:**

Resource requests specify the amount of CPU and memory a container needs. Kubernetes uses requests when scheduling the Pod onto a node.

---

#### Q2. What are resource limits in Kubernetes?

**Answer:**

Resource limits specify the maximum amount of CPU and memory that a container is allowed to consume.

---

#### Q3. What is the difference between requests and limits?

**Answer:**

**Requests** are used by Kubernetes during Pod scheduling to determine whether a node has enough resources.

**Limits** define the maximum CPU and memory that the container can consume.

---

#### Q4. What happens when a container exceeds its memory limit?

**Answer:**

If a container exceeds its memory limit, it can be terminated due to an **Out Of Memory (OOM)** condition. Kubernetes may show the container status as:

```text
OOMKilled
```

---

#### Q5. Where are resource requests and limits configured?

**Answer:**

Resource requests and limits are configured under the **container specification** inside the Pod:

```yaml
spec:
  containers:
    - name: nginx-container
      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"

        limits:
          cpu: "500m"
          memory: "256Mi"
```
