# Day 16: Deploy Pods in Kubernetes Cluster

## Solution

### Objective

Learn how to create and deploy a **Pod** in a Kubernetes cluster using a YAML file and verify that the Pod is running successfully.

---

## Scenario

A **Pod** is the smallest deployable unit in Kubernetes.

For this task, we will create a Pod containing an **Nginx container**.

### Architecture

```text
Kubernetes Cluster
        │
        ▼
       Pod
        │
        ▼
Nginx Container
```

---

## Prerequisites

Before starting this lab, make sure you have:

- Kubernetes cluster available
- `kubectl` installed
- `kubectl` configured to communicate with the cluster

### Verify kubectl

```bash
kubectl version
```

### Check Kubernetes Cluster

```bash
kubectl cluster-info
```

### Check Cluster Nodes

```bash
kubectl get nodes
```

### Expected Output

```text
NAME            STATUS   ROLES           AGE   VERSION
control-plane   Ready    control-plane   ...   ...
worker-node     Ready    <none>          ...   ...
```

> The node names and other values may be different in your Kubernetes cluster.

---

## Step 1: Create a Pod YAML File

Create a directory for the Day 16 lab:

```bash
mkdir day16-pod
cd day16-pod
```

Create the YAML file:

```bash
vi pod.yaml
```

---

## Step 2: Write the Pod Manifest

Add the following configuration to `pod.yaml`:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pod

spec:
  containers:
    - name: nginx-container
      image: nginx
      ports:
        - containerPort: 80
```

### Pod Manifest Explanation

| Field | Description |
|---|---|
| `apiVersion: v1` | Specifies the Kubernetes API version |
| `kind: Pod` | Defines the resource as a Pod |
| `metadata.name` | Specifies the Pod name |
| `spec` | Defines the desired Pod configuration |
| `containers` | Defines the containers inside the Pod |
| `name` | Specifies the container name |
| `image: nginx` | Uses the Nginx container image |
| `containerPort: 80` | Specifies the port used by the Nginx container |

---

## Step 3: Create the Pod

Apply the Pod manifest:

```bash
kubectl apply -f pod.yaml
```

### Expected Output

```text
pod/nginx-pod created
```

---

## Step 4: Check the Pod

Check the Pod status:

```bash
kubectl get pods
```

Initially, you may see:

```text
NAME        READY   STATUS              RESTARTS   AGE
nginx-pod   0/1     ContainerCreating   0          ...
```

Wait a few seconds and run the command again:

```bash
kubectl get pods
```

### Expected Output

```text
NAME        READY   STATUS    RESTARTS   AGE
nginx-pod   1/1     Running   0          ...
```

### What does `1/1` mean?

`1/1` means:

```text
1 container is ready
out of
1 container in the Pod
```

Therefore:

```text
READY = Ready Containers / Total Containers
      = 1 / 1
```

---

## Step 5: Get Detailed Pod Information

Run:

```bash
kubectl describe pod nginx-pod
```

This command displays detailed information about the Pod, including:

- Pod IP
- Node where the Pod is running
- Container image
- Container status
- Events
- Restart count

`kubectl describe` is especially useful when troubleshooting Pod-related issues.

---

## Step 6: Check Pod Logs

To view logs from the Nginx container:

```bash
kubectl logs nginx-pod
```

This displays logs from the Nginx container.

If there are multiple containers in the Pod, specify the container name:

```bash
kubectl logs nginx-pod -c nginx-container
```

---

## Step 7: Enter the Pod

Open an interactive shell inside the Nginx container:

```bash
kubectl exec -it nginx-pod -- bash
```

You should get a shell similar to:

```text
root@nginx-pod:/#
```

Now you are inside the container.

Check the Nginx version:

```bash
nginx -v
```

You should see the installed Nginx version.

Exit the container:

```bash
exit
```

---

## Step 8: Check Pod IP and Node

Run:

```bash
kubectl get pod nginx-pod -o wide
```

### Example Output

```text
NAME        READY   STATUS    IP            NODE
nginx-pod   1/1     Running   10.244.0.10   worker-node
```

The actual Pod IP and node name will be different in your Kubernetes cluster.

---

## Step 9: Access Nginx Using Port Forwarding

The following configuration:

```yaml
containerPort: 80
```

does **not** expose the Pod outside the Kubernetes cluster.

For this lab, we will use **port forwarding** to access Nginx from the local machine.

Run:

```bash
kubectl port-forward pod/nginx-pod 8080:80
```

### Expected Output

```text
Forwarding from 127.0.0.1:8080 -> 80
```

Now open the following URL in your browser:

```text
http://localhost:8080
```

You should see the **Nginx welcome page**.

Stop port forwarding with:

```text
Ctrl + C
```

---

## Step 10: Delete the Pod

Delete the Pod:

```bash
kubectl delete pod nginx-pod
```

### Expected Output

```text
pod "nginx-pod" deleted
```

Check the Pods:

```bash
kubectl get pods
```

The `nginx-pod` will no longer exist.

---

## Kubernetes Pod Workflow

```text
                    Kubernetes Cluster
                            │
                            ▼
                           Pod
                            │
                            ▼
                    Nginx Container
                            │
                            ▼
                         Port 80
                            │
                            ▼
                    Port Forwarding
                            │
                            ▼
                    localhost:8080
```

---

## Important kubectl Commands

| Command | Purpose |
|---|---|
| `kubectl version` | Check kubectl version |
| `kubectl cluster-info` | Display Kubernetes cluster information |
| `kubectl get nodes` | List cluster nodes |
| `kubectl apply -f pod.yaml` | Create the Pod from the YAML manifest |
| `kubectl get pods` | List Pods |
| `kubectl describe pod nginx-pod` | Display detailed Pod information |
| `kubectl logs nginx-pod` | View Pod logs |
| `kubectl exec -it nginx-pod -- bash` | Open a shell inside the Pod |
| `kubectl get pod nginx-pod -o wide` | Display Pod IP and node information |
| `kubectl port-forward pod/nginx-pod 8080:80` | Forward local port 8080 to Pod port 80 |
| `kubectl delete pod nginx-pod` | Delete the Pod |

---

## Expected Outcome

After completing this lab, you should be able to:

- Create a Kubernetes Pod using a YAML manifest
- Deploy an Nginx container inside a Pod
- Verify Pod status using `kubectl get pods`
- Inspect Pod details using `kubectl describe`
- View container logs using `kubectl logs`
- Access a container using `kubectl exec`
- Check the Pod IP and node
- Access Nginx using port forwarding
- Delete a Kubernetes Pod

---

## Key Takeaways

- A **Pod** is the smallest deployable unit in Kubernetes.
- A Pod can contain one or more containers.
- Kubernetes resources can be defined using YAML manifests.
- `kubectl apply -f` is used to create or update resources from a YAML file.
- `kubectl get pods` is used to check Pod status.
- `kubectl describe` provides detailed information useful for troubleshooting.
- `kubectl logs` helps inspect application/container logs.
- `kubectl exec` allows you to execute commands inside a running container.
- `containerPort: 80` specifies the container port but does not expose the Pod externally.
- `kubectl port-forward` provides temporary local access to a Pod.

---

## Lab Status

**Day 16 Completed: Deploy Pods in Kubernetes Cluster**

```text
Kubernetes Pod
      │
      ├── Created
      ├── Verified
      ├── Inspected
      ├── Logs Checked
      ├── Container Accessed
      ├── Nginx Accessed
      └── Pod Deleted
```
