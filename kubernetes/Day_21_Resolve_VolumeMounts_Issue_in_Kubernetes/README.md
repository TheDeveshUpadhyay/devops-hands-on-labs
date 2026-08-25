# Day 21: Resolve VolumeMounts Issue in Kubernetes

## Solution

### Objective

Learn how to identify and fix common **VolumeMounts issues** in Kubernetes Pods.

The main goal is to understand the relationship between:

```text
Volume
   ↓
VolumeMount
   ↓
Container
```

---

## Scenario

Suppose we have an Nginx container and we want to mount storage inside it.

A common mistake is to define a `volumeMount` but forget to define the corresponding `volume`.

For example:

```yaml
volumeMounts:
  - name: nginx-storage
    mountPath: /usr/share/nginx/html
```

But if `nginx-storage` is not defined under:

```yaml
volumes:
```

Kubernetes cannot mount it.

This causes a **VolumeMounts issue**.

---

## Step 1: Create a Directory

Create a directory:

```bash
mkdir day21-volumemount
```

Move into the directory:

```bash
cd day21-volumemount
```

Create the Pod YAML file:

```bash
vi pod.yaml
```

---

## Step 2: Create a Pod with a VolumeMount

Add the following configuration to `pod.yaml`:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-volume-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      volumeMounts:
        - name: nginx-storage
          mountPath: /usr/share/nginx/html

  volumes:
    - name: nginx-storage
      emptyDir: {}
```

Save the file.

---

## Step 3: Understand `volumeMounts`

This section:

```yaml
volumeMounts:
  - name: nginx-storage
    mountPath: /usr/share/nginx/html
```

means:

> Mount the volume named `nginx-storage` inside the container at `/usr/share/nginx/html`.

Think:

```text
Kubernetes Volume
       │
       │ nginx-storage
       ▼
   Container
       │
       ▼
/usr/share/nginx/html
```

---

## Step 4: Understand `volumes`

This part:

```yaml
volumes:
  - name: nginx-storage
    emptyDir: {}
```

defines the volume available to the Pod.

The names **must match**:

### `volumeMounts`

```yaml
volumeMounts:
  - name: nginx-storage
```

### `volumes`

```yaml
volumes:
  - name: nginx-storage
```

If they don't match, Kubernetes cannot connect the mount to the volume.

### What does `emptyDir: {}` mean?

```yaml
emptyDir: {}
```

creates an **empty directory** when the Pod starts.

The `{}` means **no additional configuration is needed**.

---

## Step 5: Create the Pod

Apply the configuration:

```bash
kubectl apply -f pod.yaml
```

Expected output:
OAOAOA
OAOAOA```text
OAOAOApod/nginx-volume-pod created
OAOAOA```

---

## Step 6: Check the Pod

Check the Pod:

```bash
OAOAOAkubectl get pods
```
OAOAOA
OAOAOAExpected output:
OAOAOA
```text
NAME               READY   STATUS    RESTARTS
nginx-volume-pod   1/1     Running   0
```

---

## Step 7: Verify the Mount
OAOAOA
OAOAOADescribe the Pod:

OAOAOA```bash
OAOAOAkubectl describe pod nginx-volume-pod
```

Look for:

```text
Mounts:
  /usr/share/nginx/html from nginx-storage
```
OAOAOAOAOAOA
You should also see:

```text
Volumes:
  nginx-storage:
    Type:       EmptyDir
```

This confirms that the volume has been mounted.

---

## Step 8: Enter the Container
OAOAOA
Enter the container:

```bash
kubectl exec -it nginx-volume-pod -- /bin/bash
OBOBOB```

OBOBOBInside the container:

```bash
OBOBOBdf -h
```

OBOBOBYou can also check:

OBOBOB```bash
mount
```

Then:

```bash
ls -la /usr/share/nginx/html
```

OBOBOBExit:

OBOBOB```bash
OBOBOBexit
```
OBOBOB
---

OBOBOB## Step 9: Understand a Common VolumeMount Error

Now let's intentionally create an error.

Change:

```yaml
OBOBOBvolumeMounts:
OBOBOB  - name: nginx-storage
OBOBOB```

OBOBOBto:
OBOBOB
```yaml
volumeMounts:
  - name: wrong-volume
```

But leave:

```yaml
volumes:
  - name: nginx-storage
    emptyDir: {}
```

Now the names don't match.

```text
volumeMounts
     │
     └── wrong-volume ❌

volumes
     │
     └── nginx-storage
```

Kubernetes cannot find `wrong-volume`.

---

## Step 10: Apply the Incorrect Configuration

Delete the existing Pod first:

```bash
kubectl delete pod nginx-volume-pod
```

Then:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

The Pod may remain in:

```text
Pending
```

---

## Step 11: Troubleshoot the Issue

Run:

```bash
kubectl describe pod nginx-volume-pod
```

Look at the **Events** section.

You may see an error similar to:

```text
Unable to mount volumes
```

or:

```text
MountVolume.SetUp failed
```

The exact error depends on the type of volume and configuration.

---

## Step 12: Fix the Problem

Change:

```yaml
volumeMounts:
  - name: wrong-volume
```

back to:

```yaml
volumeMounts:
  - name: nginx-storage
```

Now both names match:

```text
volumeMounts
      │
      └── nginx-storage
              │
              ▼
volumes
      │
      └── nginx-storage
```

---

## Step 13: Recreate the Pod

Delete the failed Pod:

```bash
kubectl delete pod nginx-volume-pod
```

Apply the corrected YAML:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

Expected:

```text
NAME               READY   STATUS
nginx-volume-pod   1/1     Running
```

---

# Important VolumeMount Concepts

## 1. `volume`

Defines the storage available to the Pod.

```yaml
volumes:
  - name: nginx-storage
    emptyDir: {}
```

Think:

> **What storage do I have?**

---

## 2. `volumeMount`

Defines where that storage appears inside the container.

```yaml
volumeMounts:
  - name: nginx-storage
    mountPath: /usr/share/nginx/html
```

Think:

> **Where do I want that storage inside the container?**

---

## Easy Way to Remember

```text
volumes
   ↓
WHAT storage?

volumeMounts
   ↓
WHERE inside container?
```

---

# Common VolumeMount Issues

## Issue 1: Volume Name Mismatch

### Incorrect

```yaml
volumeMounts:
  - name: app-data
```

```yaml
volumes:
  - name: application-data
```

The names are different.

### Correct

```yaml
volumeMounts:
  - name: app-data
```

```yaml
volumes:
  - name: app-data
```

The names must match.

---

## Issue 2: Missing `mountPath`

### Incorrect

```yaml
volumeMounts:
  - name: app-data
```

### Correct

```yaml
volumeMounts:
  - name: app-data
    mountPath: /data
```

---

## Issue 3: Volume Is Not Defined

### Incorrect

```yaml
volumeMounts:
  - name: app-data
    mountPath: /data
```

There is no corresponding `volumes` definition.

### Correct

```yaml
volumeMounts:
  - name: app-data
    mountPath: /data

volumes:
  - name: app-data
    emptyDir: {}
```

---

## Issue 4: Wrong Indentation

Kubernetes YAML is indentation-sensitive.

### Incorrect

```yaml
containers:
  - name: nginx
    image: nginx

volumeMounts:
- name: nginx-storage
  mountPath: /data
```

### Correct

```yaml
containers:
  - name: nginx
    image: nginx
    volumeMounts:
      - name: nginx-storage
        mountPath: /data
```

---

# `emptyDir` in This Lab

We used:

```yaml
emptyDir: {}
```

This creates temporary storage for the Pod.

The lifecycle is:

```text
Pod starts
   ↓
emptyDir created
   ↓
Container uses it
   ↓
Pod deleted
   ↓
emptyDir deleted
```

So `emptyDir` is useful for **temporary data**, but it is **not persistent storage**.

For persistent application data, you would normally learn:

```text
PersistentVolume
       ↓
PersistentVolumeClaim
       ↓
      Pod
       ↓
VolumeMount
```

---

# Troubleshooting Commands

These are the commands you should remember for today's task.

### Check Pod Status

```bash
kubectl get pods
```

### Detailed Information

```bash
kubectl describe pod nginx-volume-pod
```

### Check Pod YAML

```bash
kubectl get pod nginx-volume-pod -o yaml
```

### Check Logs

```bash
kubectl logs nginx-volume-pod
```

### Enter Container

```bash
kubectl exec -it nginx-volume-pod -- /bin/bash
```

### Check Mounted Filesystem

Inside the container:

```bash
df -h
```

You can also use:

```bash
mount
```

---

# Expected Outcome

After completing this lab:

- Pod created with a volume.
- Volume mounted into the Nginx container.
- `volume` and `volumeMount` relationship understood.
- Intentional VolumeMount error created.
- Error identified using `kubectl describe`.
- Volume name corrected.
- Pod successfully returned to `Running`.

---

# Interview Questions

## Q1. What is a `volumeMount`?

### Answer

`volumeMount` specifies where a Kubernetes volume should be mounted inside a container.

---

## Q2. What is the difference between `volume` and `volumeMount`?

### Answer

`volume` defines the storage available to the Pod, while `volumeMount` specifies the location where that storage is mounted inside the container.

---

## Q3. What must match between `volume` and `volumeMount`?

### Answer

The **name must match**.

```yaml
volumeMounts:
  - name: app-data
    mountPath: /data

volumes:
  - name: app-data
    emptyDir: {}
```

---

## Q4. How do you troubleshoot a VolumeMount issue?

### Answer

First check:

```bash
kubectl get pods
```

Then:

```bash
kubectl describe pod <pod-name>
```

Especially inspect the **Events** section for mount-related errors.

---

## Q5. Does `emptyDir` provide persistent storage?

### Answer

No.

`emptyDir` exists for the lifetime of the Pod. When the Pod is deleted, its `emptyDir` data is deleted.

---

# Key Takeaway

The main concepts to remember are:

```text
Volume
   ↓
Defines the storage

VolumeMount
   ↓
Defines where the storage is mounted

Container
   ↓
Uses the mounted storage
```

The **volume name in `volumes` and `volumeMounts` must match**.

---

## Important Commands

### Check Pod Status

```bash
kubectl get pods
```

### Describe Pod

```bash
kubectl describe pod nginx-volume-pod
```

### Check Pod YAML

```bash
kubectl get pod nginx-volume-pod -o yaml
```

### Check Logs

```bash
kubectl logs nginx-volume-pod
```

### Enter Container

```bash
kubectl exec -it nginx-volume-pod -- /bin/bash
```

---

# Day 53 Concept

```text
Define Volume
      ↓
Define VolumeMount
      ↓
Volume Name Must Match
      ↓
Mount Storage Inside Container
      ↓
Verify with kubectl describe
      ↓
Troubleshoot Mount Errors
      ↓
Fix Configuration
      ↓
Pod Running Successfully ✅
```
