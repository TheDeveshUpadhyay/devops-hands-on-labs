# Day 22: Kubernetes Shared Volumes

## Solution

### Objective

Learn how **multiple containers inside the same Kubernetes Pod can share the same volume**.

This is an important concept because containers in one Pod can communicate through a shared filesystem.

---

## Scenario

Suppose we have two containers inside one Pod:

```text
Pod
 │
 ├── Container 1 → nginx
 │
 └── Container 2 → sidecar
```

We want both containers to access the same directory:

```text
/data
```

We can use an `emptyDir` volume:

```text
                 Pod
                  │
          ┌───────┴───────┐
          │               │
       Nginx           Sidecar
          │               │
          └───────┬───────┘
                  │
           shared-volume
                  │
               emptyDir
```

Both containers see the same files.

---

## Step 1: Create Directory

Create a directory:

```bash
mkdir day22-shared-volume
```

Move into the directory:

```bash
cd day22-shared-volume
```

Create the YAML file:

```bash
vi pod.yaml
```

---

## Step 2: Create the Pod

Create the following `pod.yaml` file:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: shared-volume-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      volumeMounts:
        - name: shared-storage
          mountPath: /data

    - name: sidecar-container
      image: busybox:latest
      command: ["/bin/sh", "-c"]
      args:
        - |
          while true; do
            date >> /data/log.txt
            sleep 5
          done

      volumeMounts:
        - name: shared-storage
          mountPath: /data

  volumes:
    - name: shared-storage
      emptyDir: {}
```

Save the file.

---

## Step 3: Understand the YAML

We have **two containers**:

```yaml
containers:
  - name: nginx-container
    image: nginx:latest

  - name: sidecar-container
    image: busybox:latest
```

Both containers are inside the **same Pod**.

### Container 1

Nginx mounts:

```yaml
volumeMounts:
  - name: shared-storage
    mountPath: /data
```

So Nginx sees:

```text
/data
```

### Container 2

The BusyBox container also mounts:

```yaml
volumeMounts:
  - name: shared-storage
    mountPath: /data
```

So BusyBox also sees:

```text
/data
```

The important part is that both containers use the same volume name:

```text
shared-storage
```

---

## Step 4: Understand `emptyDir`

At the bottom:

```yaml
volumes:
  - name: shared-storage
    emptyDir: {}
```

Kubernetes creates one temporary volume for the Pod.

```text
Pod
 │
 └── shared-storage
       │
       └── emptyDir
```

Both containers mount this same volume.

---

## Step 5: Create the Pod

Apply the configuration:

```bash
kubectl apply -f pod.yaml
```

Expected output:

```text
pod/shared-volume-pod created
```

---

## Step 6: Check the Pod

Run:

```bash
kubectl get pods
```

Expected output:

```text
NAME                READY   STATUS
shared-volume-pod   2/2     Running
```

Notice:

```text
2/2
```

This means both containers are ready.

---

## Step 7: Check the Containers

Run:

```bash
kubectl get pod shared-volume-pod
```

You can also run:

```bash
kubectl describe pod shared-volume-pod
```

You should see both containers:

```text
nginx-container
sidecar-container
```

---

## Step 8: Check the Shared File

The BusyBox container is continuously executing:

```bash
date >> /data/log.txt
```

every 5 seconds.

So the sidecar container is writing:

```text
/data/log.txt
```

Now read that file from the **Nginx container**:

```bash
kubectl exec -it shared-volume-pod -c nginx-container -- cat /data/log.txt
```

You should see something similar to:

```text
Wed Aug 26 12:45:01 UTC 2026
Wed Aug 26 12:45:06 UTC 2026
Wed Aug 26 12:45:11 UTC 2026
```

This proves that the Nginx container can access a file created by the BusyBox container.

---

## Step 9: Check the Same File from Sidecar

Run:

```bash
kubectl exec -it shared-volume-pod -c sidecar-container -- cat /data/log.txt
```

You will see the same content.

So:

```text
Sidecar
   │
   │ writes
   ▼
/data/log.txt
   ▲
   │ reads
   │
Nginx
```

That's a **shared volume**.

---

## Step 10: Create a File from Nginx

Now let's test the opposite direction.

Enter the Nginx container:

```bash
kubectl exec -it shared-volume-pod -c nginx-container -- sh
```

Inside the Nginx container:

```bash
echo "Hello from Nginx" > /data/nginx.txt
```

Then:

```bash
exit
```

Now check the file from the sidecar:

```bash
kubectl exec -it shared-volume-pod -c sidecar-container -- cat /data/nginx.txt
```

Expected output:

```text
Hello from Nginx
```

This proves that **both containers can read and write the same volume**.

---

# Important Concept

The volume is attached to the **Pod**, not separately created for each container.

```text
                 Pod
                  │
           shared-storage
                  │
          ┌───────┴───────┐
          │               │
       Nginx           BusyBox
          │               │
        /data           /data
          │               │
          └───────┬───────┘
                  │
             Same files
```

Both containers see the same underlying storage.

---

# Why Use Shared Volumes?

A common pattern is the **sidecar container**.

For example:

```text
Pod
 │
 ├── Main Application
 │        │
 │        └── writes logs
 │
 └── Sidecar
          │
          └── reads/processes logs
```

The shared volume allows the sidecar to access files produced by the main application.

---

# Real-World Example

Imagine a web application writes:

```text
/app/logs/application.log
```

You could have:

```text
Pod
 │
 ├── Application Container
 │        │
 │        └── writes application.log
 │
 └── Logging Container
          │
          └── reads application.log
```

Both containers mount:

```text
/app/logs
```

through the same volume.

This is a classic **sidecar pattern**.

---

# Important Difference: Same Pod vs Different Pods

This is where beginners often get confused.

## Containers in the Same Pod

They **can share** an `emptyDir` volume:

```text
Pod
 ├── Container A
 └── Container B
        │
     emptyDir
```

## Containers in Different Pods

They **cannot share the same `emptyDir`**.

```text
Pod A                         Pod B
  │                             │
emptyDir                     emptyDir
  │                             │
Different storage          Different storage
```

For storage shared between different Pods, you generally need something like:

```text
PersistentVolume
       ↓
PersistentVolumeClaim
       ↓
Multiple Pods
```

This depends on the storage backend and access mode.

---

# Why Do We Use `-c` in `kubectl exec`?

Our Pod contains two containers:

```text
nginx-container
sidecar-container
```

Therefore, Kubernetes needs to know **which container** you want to enter or execute a command in.

For example:

```bash
kubectl exec -it shared-volume-pod -c nginx-container -- /bin/bash
```

This means:

```text
Pod name
   ↓
shared-volume-pod

Container
   ↓
nginx-container

Command
   ↓
/bin/bash
```

For the sidecar:

```bash
kubectl exec -it shared-volume-pod -c sidecar-container -- /bin/sh
```

The `-c` option specifies the container.

---

# Useful Commands

| Command | Purpose |
|---|---|
| `kubectl apply -f pod.yaml` | Create the Pod |
| `kubectl get pods` | Check Pod status |
| `kubectl describe pod shared-volume-pod` | Inspect containers, volumes, and events |
| `kubectl exec -it shared-volume-pod -c nginx-container -- /bin/bash` | Enter Nginx container |
| `kubectl exec -it shared-volume-pod -c sidecar-container -- /bin/sh` | Enter sidecar container |
| `kubectl exec -it shared-volume-pod -c nginx-container -- cat /data/log.txt` | Read the shared file from Nginx |
| `kubectl exec -it shared-volume-pod -c sidecar-container -- cat /data/log.txt` | Read the shared file from sidecar |
| `kubectl delete pod shared-volume-pod` | Delete the Pod |

---

# Expected Outcome

You should be able to demonstrate:

```text
1. Create one Pod
        ↓
2. Run two containers
        ↓
3. Create one emptyDir volume
        ↓
4. Mount it into both containers
        ↓
5. Container A writes a file
        ↓
6. Container B reads the same file
```

The key result is that both containers can access the same underlying volume.

---

# Interview Questions

## Q1. Can two containers in the same Pod share a volume?

### Answer

Yes. Multiple containers in the same Pod can mount and access the same volume.

---

## Q2. Why would you use a shared volume?

### Answer

To allow containers in the same Pod to share files or data.

A common use case is the **sidecar pattern**, such as an application container writing logs and a sidecar processing those logs.

---

## Q3. Can two different Pods share an `emptyDir`?

### Answer

No. `emptyDir` belongs to a Pod and its data is available only to containers within that Pod.

---

## Q4. What happens to `emptyDir` when the Pod is deleted?

### Answer

The `emptyDir` storage and its data are deleted with the Pod.

---

## Q5. What is the difference between a volume and a volumeMount?

### Answer

`volumes` defines the storage available to the Pod, while `volumeMounts` defines where that storage is mounted inside a container.

---

# Key Takeaway

The most important concept from this lab is:

```text
                 Pod
                  │
          ┌───────┴───────┐
          │               │
       Nginx           Sidecar
          │               │
        /data           /data
          │               │
          └───────┬───────┘
                  │
           Shared Volume
                  │
              emptyDir
```

**Multiple containers inside the same Pod can mount the same volume and access the same files.**

Remember:

```text
Same Pod
   ↓
Shared Volume
   ↓
Multiple Containers
   ↓
Same Files
```

For different Pods, `emptyDir` is not sufficient. Persistent storage mechanisms such as **PersistentVolume (PV)** and **PersistentVolumeClaim (PVC)** may be required depending on the use case and storage backend.

---

## Day 22 Concept

```text
Create Pod
    ↓
Run Multiple Containers
    ↓
Create emptyDir Volume
    ↓
Mount Same Volume into Both Containers
    ↓
Container A Writes File
    ↓
Container B Reads Same File
    ↓
Shared Volume Successfully Demonstrated ✅
```
