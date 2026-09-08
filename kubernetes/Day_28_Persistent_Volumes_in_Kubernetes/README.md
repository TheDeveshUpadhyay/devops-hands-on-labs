# Day 28: Persistent Volumes in Kubernetes

## Solution

## Objective

Learn how **Persistent Volumes (PV)** and **Persistent Volume Claims (PVC)** work in Kubernetes and why they are needed for storing data permanently.

The main concept is:

```text
Pod
 |
 v
PVC
 |
 v
PV
 |
 v
Storage
```

---

# Why Do We Need Persistent Storage?

Normally, data stored inside a container is temporary.

If a Pod is **deleted, crashes, and is recreated**, data stored only in the container filesystem can be lost.

For example:

```text
Pod
 |
 v
Container
 |
 v
/data/file.txt
```

If the Pod disappears:

```text
Pod Deleted
     |
     v
Container Deleted
     |
     v
Data May Be Lost
```

For databases, application uploads, logs, and other important files, we need storage that can survive Pod recreation.

That is where **Persistent Volumes** are used.

---

# Important Concepts

## Persistent Volume (PV)

A **Persistent Volume (PV)** is storage available to the Kubernetes cluster.

Think of it as:

```text
Actual Storage
      |
      v
PersistentVolume
```

The storage may come from:

* AWS EBS
* Azure Disk
* GCP Persistent Disk
* NFS
* Local storage
* Other CSI-backed storage

---

## Persistent Volume Claim (PVC)

A **Persistent Volume Claim (PVC)** is a request for storage made by an application.

Think of it like:

```text
Pod/Application
      |
      | "I need 1Gi storage"
      v
     PVC
      |
      v
Find Suitable PV
```

The application doesn't need to directly manage the underlying physical storage.

It requests storage through a PVC.

---

# Architecture

The basic relationship is:

```text
              Kubernetes Cluster

                     Pod
                      |
                      v
                     PVC
                      |
               Requests Storage
                      |
                      v
                      PV
                      |
                      v
               Physical Storage
```

Or simply:

```text
Pod -> PVC -> PV -> Storage
```

---

# Scenario

In this hands-on lab, we will create:

```text
1 PersistentVolume
        |
        v
1 PersistentVolumeClaim
        |
        v
1 Nginx Pod
```

The Nginx Pod will store a file in persistent storage.

Then we will:

1. Create the PV.
2. Create the PVC.
3. Create the Nginx Pod.
4. Mount the persistent storage.
5. Create a file.
6. Delete the Pod.
7. Recreate the Pod.
8. Verify whether the file still exists.

This demonstrates how persistent storage can survive the lifecycle of an individual Pod.

---

# Step 1: Create a Directory

Create the working directory:

```bash
mkdir day28-persistent-volume
cd day28-persistent-volume
```

We will create three YAML files:

```bash
vi pv.yaml
vi pvc.yaml
vi pod.yaml
```

The final directory will contain:

```text
day28-persistent-volume/
├── pv.yaml
├── pvc.yaml
└── pod.yaml
```

---

# Step 2: Create the Persistent Volume

Open:

```bash
vi pv.yaml
```

Add:

```yaml
apiVersion: v1
kind: PersistentVolume

metadata:
  name: nginx-pv

spec:
  capacity:
    storage: 1Gi

  accessModes:
    - ReadWriteOnce

  persistentVolumeReclaimPolicy: Retain

  hostPath:
    path: /mnt/nginx-data
```

Save the file.

---

# Step 3: Understand the PV YAML

Let's understand the important fields.

## Capacity

```yaml
capacity:
  storage: 1Gi
```

This means the Persistent Volume provides:

```text
1 GiB
```

of storage capacity.

---

## Access Modes

Our PV contains:

```yaml
accessModes:
  - ReadWriteOnce
```

Access modes define how the storage can be mounted.

Common Kubernetes access modes include:

| Access Mode      | Abbreviation |
| ---------------- | ------------ |
| ReadWriteOnce    | RWO          |
| ReadOnlyMany     | ROX          |
| ReadWriteMany    | RWX          |
| ReadWriteOncePod | RWOP         |

For this lab:

```text
ReadWriteOnce
```

means the volume can be mounted read-write by workloads on a **single node at a time**, depending on the storage provider.

---

## Persistent Volume Reclaim Policy

Our PV contains:

```yaml
persistentVolumeReclaimPolicy: Retain
```

This tells Kubernetes what should happen to the PV when its PVC is deleted.

With:

```text
Retain
```

the underlying data is not automatically deleted.

The storage requires manual handling before it can normally be reused.

---

## `hostPath`

Our PV contains:

```yaml
hostPath:
  path: /mnt/nginx-data
```

This tells Kubernetes to use a directory on the node:

```text
/mnt/nginx-data
```

### Important

`hostPath` is useful for learning in a local or controlled lab environment.

It is generally **not the preferred storage solution for production workloads**.

In production Kubernetes environments, persistent storage is normally provided through storage systems and CSI drivers.

For example, on AWS EKS you can use:

```text
PVC
 |
 v
StorageClass
 |
 v
AWS EBS CSI Driver
 |
 v
AWS EBS
```

---

# Step 4: Create the Persistent Volume

Apply the PV manifest:

```bash
kubectl apply -f pv.yaml
```

Expected output:

```text
persistentvolume/nginx-pv created
```

Check the PV:

```bash
kubectl get pv
```

You may see:

```text
NAME       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS
nginx-pv   1Gi        RWO            Retain           Available
```

The important status is:

```text
Available
```

This means:

```text
PV Exists
   |
   v
No PVC Is Using It Yet
```

---

# Step 5: Create a Persistent Volume Claim

Create:

```bash
vi pvc.yaml
```

Add:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: nginx-pvc

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 1Gi
```

Save the file.

---

# Step 6: Understand the PVC

The important part is:

```yaml
resources:
  requests:
    storage: 1Gi
```

This means:

```text
"I need 1Gi of storage."
```

Notice that the PVC does **not** directly say:

```text
Use nginx-pv
```

Instead, Kubernetes tries to find a compatible PV based on requirements such as:

* Capacity
* Access mode
* StorageClass
* Volume mode
* Selectors, if used

The basic process is:

```text
PVC
 |
 | Requests 1Gi
 v
Kubernetes
 |
 | Finds Compatible Storage
 v
PV
```

---

# Step 7: Create the PVC

Apply the PVC:

```bash
kubectl apply -f pvc.yaml
```

Expected output:

```text
persistentvolumeclaim/nginx-pvc created
```

Check it:

```bash
kubectl get pvc
```

You may see:

```text
NAME        STATUS   VOLUME     CAPACITY   ACCESS MODES
nginx-pvc   Bound    nginx-pv   1Gi        RWO
```

The important status is:

```text
Bound
```

Meaning:

```text
PVC
 |
 v
Successfully Bound To
 |
 v
PV
```

---

# Step 8: Check the PV Again

Run:

```bash
kubectl get pv
```

Now you may see:

```text
NAME       CAPACITY   ACCESS MODES   STATUS   CLAIM
nginx-pv   1Gi        RWO            Bound    default/nginx-pvc
```

Earlier:

```text
STATUS = Available
```

Now:

```text
STATUS = Bound
```

because the PVC is using the PV.

The relationship is now:

```text
nginx-pvc
    |
    | Bound
    v
nginx-pv
```

---

# Step 9: Create the Nginx Pod

Create:

```bash
vi pod.yaml
```

Add:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: nginx-pv-pod

spec:
  containers:
    - name: nginx-container
      image: nginx:latest

      volumeMounts:
        - name: nginx-storage
          mountPath: /usr/share/nginx/html

  volumes:
    - name: nginx-storage
      persistentVolumeClaim:
        claimName: nginx-pvc
```

Save the file.

---

# Step 10: Understand the Pod Configuration

The Pod does **not** directly reference:

```text
nginx-pv
```

Instead, it references:

```yaml
claimName: nginx-pvc
```

The relationship is:

```text
Pod
 |
 v
nginx-pvc
 |
 v
nginx-pv
 |
 v
Storage
```

This separation is important.

The application asks for storage through the PVC instead of directly depending on a particular PV.

---

# Step 11: Understand `volumeMounts`

Inside the container configuration, we have:

```yaml
volumeMounts:
  - name: nginx-storage
    mountPath: /usr/share/nginx/html
```

This means:

> Mount the volume named `nginx-storage` inside the Nginx container at `/usr/share/nginx/html`.

Nginx serves its default web content from:

```text
/usr/share/nginx/html
```

Therefore, files written to this directory will be stored in the mounted volume.

---

# Step 12: Understand `volumes`

The Pod also contains:

```yaml
volumes:
  - name: nginx-storage
    persistentVolumeClaim:
      claimName: nginx-pvc
```

This means:

```text
Volume Name
     |
     v
nginx-storage
     |
     v
Storage Source
     |
     v
PVC
     |
     v
nginx-pvc
```

The names connect the pieces together:

```text
volumeMount
     |
     | name: nginx-storage
     v
volume
     |
     | claimName: nginx-pvc
     v
PVC
```

---

# Step 13: Create the Pod

Apply the Pod manifest:

```bash
kubectl apply -f pod.yaml
```

Expected output:

```text
pod/nginx-pv-pod created
```

Check the Pod:

```bash
kubectl get pods
```

Expected:

```text
NAME           READY   STATUS
nginx-pv-pod   1/1     Running
```

Wait until the Pod shows:

```text
1/1 Running
```

---

# Step 14: Check the Mounted Volume

Enter the container:

```bash
kubectl exec -it nginx-pv-pod -- /bin/bash
```

Inside the container, check mounted filesystems:

```bash
df -h
```

You can also run:

```bash
mount
```

Move to the Nginx web directory:

```bash
cd /usr/share/nginx/html
```

Check the files:

```bash
ls
```

This directory is backed by the volume mounted through the PVC.

---

# Step 15: Create Persistent Data

Inside the container, create an `index.html` file:

```bash
echo "Hello from Kubernetes Persistent Volume" > index.html
```

Verify the file:

```bash
cat index.html
```

Expected output:

```text
Hello from Kubernetes Persistent Volume
```

Exit the container:

```bash
exit
```

At this point:

```text
Pod
 |
 v
/usr/share/nginx/html/index.html
 |
 v
PVC
 |
 v
PV
 |
 v
Persistent Storage
```

---

# Step 16: Delete the Pod

Now delete the Pod:

```bash
kubectl delete pod nginx-pv-pod
```

Check:

```bash
kubectl get pods
```

The Pod should be gone.

Now check the PVC:

```bash
kubectl get pvc
```

It should still show:

```text
Bound
```

Check the PV:

```bash
kubectl get pv
```

The PV should also still exist.

This demonstrates the main concept:

```text
Pod Deleted
     |
     v
PVC Still Exists
     |
     v
PV Still Exists
     |
     v
Data Remains
```

The Pod lifecycle and persistent storage lifecycle are separate.

---

# Step 17: Recreate the Pod

Create the Pod again using the same YAML file:

```bash
kubectl apply -f pod.yaml
```

Check:

```bash
kubectl get pods
```

Wait until:

```text
NAME           READY   STATUS
nginx-pv-pod   1/1     Running
```

The recreated Pod uses the same PVC:

```text
New Pod
   |
   v
nginx-pvc
   |
   v
nginx-pv
   |
   v
Existing Data
```

---

# Step 18: Verify the Data

Run:

```bash
kubectl exec nginx-pv-pod -- cat /usr/share/nginx/html/index.html
```

Expected output:

```text
Hello from Kubernetes Persistent Volume
```

The file still exists.

This demonstrates that the data survived the deletion and recreation of the Pod.

---

# Important Difference: `emptyDir` vs Persistent Volume

An `emptyDir` volume and PV/PVC storage solve different problems.

| `emptyDir`                              | PV/PVC                                              |
| --------------------------------------- | --------------------------------------------------- |
| Temporary Pod storage                   | Persistent storage                                  |
| Created for the Pod                     | Storage exists independently from an individual Pod |
| Deleted when the Pod is removed         | Can survive Pod deletion                            |
| Good for temporary shared data          | Good for important application data                 |
| Common for sidecars and temporary files | Common for databases and application uploads        |

The main difference is:

### `emptyDir`

```text
Pod
 |
 v
emptyDir
 |
 v
Pod Deleted
 |
 v
Data Deleted
```

### PV/PVC

```text
Pod
 |
 v
PVC
 |
 v
PV
 |
 v
Pod Deleted
 |
 v
Storage Can Remain
```

---

# What Is the Difference Between PV and PVC?

This is one of the most common Kubernetes storage interview questions.

## PV

**PersistentVolume**

represents storage available to the cluster.

Example:

```text
PV = 100Gi storage
```

## PVC

**PersistentVolumeClaim**

represents the application's request for storage.

Example:

```text
PVC = "I need 20Gi storage"
```

Kubernetes can then bind a compatible PV to the PVC.

```text
        PV
   100Gi Storage
        ^
        |
      Binding
        |
        v
       PVC
  20Gi Requested
```

A simple way to remember it:

```text
PV  = Storage Resource
PVC = Request for Storage
```

---

# PV Lifecycle

A Persistent Volume can have different statuses.

## Available

```text
PV Exists
   |
   v
No PVC Is Using It
```

The PV is available for binding.

---

## Bound

```text
PV
 |
 | Bound To
 v
PVC
```

The PV is connected to a PVC.

---

## Released

The PVC was deleted, but the PV has not yet been made available for another claim.

```text
PVC Deleted
     |
     v
PV Released
```

---

## Failed

The volume has encountered a problem.

---

# Reclaim Policies

Persistent Volumes can have different reclaim policies.

## Retain

Example:

```yaml
persistentVolumeReclaimPolicy: Retain
```

Meaning:

```text
PVC Deleted
     |
     v
PV/Data Retained
```

Manual cleanup is normally required before the retained volume can be reused.

---

## Delete

With the `Delete` reclaim policy:

```text
PVC Deleted
     |
     v
Backing Storage May Also Be Deleted
```

This is common with dynamically provisioned cloud storage, depending on the StorageClass configuration.

---

# Static Provisioning

What we did in this lab is an example of:

**Static Provisioning**

The flow is:

```text
Administrator
     |
     v
Creates PV Manually
     |
     v
Developer Creates PVC
     |
     v
PVC Finds Compatible PV
     |
     v
PVC Binds To PV
```

In static provisioning, the storage resource exists before the application requests it.

---

# Dynamic Provisioning

In production clusters, administrators often do not manually create every Persistent Volume.

Instead, Kubernetes can dynamically provision storage.

The flow is:

```text
PVC
 |
 v
StorageClass
 |
 v
CSI Driver
 |
 v
Storage Provider
 |
 v
New Storage Created
 |
 v
PV Automatically Created
 |
 v
PVC Bound
```

This is called:

**Dynamic Provisioning**

---

# Dynamic Provisioning on AWS EKS

On AWS EKS, the flow can look like:

```text
PVC
 |
 v
StorageClass
 |
 v
EBS CSI Driver
 |
 v
AWS EBS Volume
```

Instead of manually creating every PV, the EBS CSI driver can provision EBS storage based on a PVC request.

---

# Real AWS EKS Example

Suppose an application requests:

```yaml
resources:
  requests:
    storage: 20Gi
```

The storage flow can be:

```text
Application Pod
      |
      v
PVC Requests 20Gi
      |
      v
StorageClass
      |
      v
AWS EBS CSI Driver
      |
      v
AWS API
      |
      v
20Gi EBS Volume Created
      |
      v
PV Automatically Created
      |
      v
PVC Becomes Bound
      |
      v
Volume Attached to Worker Node
      |
      v
Volume Mounted into Pod
```

This is much closer to how persistent storage is normally used in AWS EKS environments.

---

# Useful Commands

| Command                                      | Purpose                                  |
| -------------------------------------------- | ---------------------------------------- |
| `kubectl get pv`                             | Check Persistent Volumes                 |
| `kubectl get pvc`                            | Check Persistent Volume Claims           |
| `kubectl describe pv nginx-pv`               | View detailed PV information             |
| `kubectl describe pvc nginx-pvc`             | View detailed PVC information and events |
| `kubectl apply -f pv.yaml`                   | Create the PV                            |
| `kubectl apply -f pvc.yaml`                  | Create the PVC                           |
| `kubectl apply -f pod.yaml`                  | Create the Pod                           |
| `kubectl exec -it nginx-pv-pod -- /bin/bash` | Enter the Nginx container                |
| `kubectl delete pod nginx-pv-pod`            | Delete the Pod                           |
| `kubectl get storageclass`                   | Check available StorageClasses           |

---

# Troubleshooting PVC Issues

Suppose:

```bash
kubectl get pvc
```

shows:

```text
NAME        STATUS
nginx-pvc   Pending
```

`Pending` means Kubernetes has not successfully bound the PVC to storage.

Start troubleshooting with:

```bash
kubectl describe pvc <pvc-name>
```

For this lab:

```bash
kubectl describe pvc nginx-pvc
```

Check the **Events** section.

Common reasons for a PVC remaining `Pending` include:

* No suitable PV is available
* Capacity mismatch
* Access mode mismatch
* StorageClass mismatch
* CSI driver issue
* Cloud volume provisioning issue

---

# Troubleshooting Flow

A useful investigation flow is:

```text
Pod Pending
    |
    v
kubectl get pods
    |
    v
kubectl describe pod <pod-name>
    |
    v
Volume/PVC Issue Found
    |
    v
kubectl get pvc
    |
    v
PVC Pending?
    |
    v
kubectl describe pvc <pvc-name>
    |
    v
Check PV
    |
    v
Check StorageClass
    |
    v
Check CSI Driver
```

This is better than randomly modifying the YAML.

Follow the problem from:

```text
Pod -> PVC -> PV -> StorageClass/CSI -> Storage
```

---

# Expected Outcome

By completing Day 28, you should understand:

* Why Kubernetes needs persistent storage.
* The difference between temporary and persistent storage.
* What a Persistent Volume is.
* What a Persistent Volume Claim is.
* How a PVC binds to a PV.
* How Pods consume storage through PVCs.
* How persistent storage can survive Pod recreation.
* What `ReadWriteOnce` means.
* What the `Retain` reclaim policy means.
* The difference between static and dynamic provisioning.
* How dynamic storage provisioning works with CSI drivers.
* How persistent storage commonly works on AWS EKS.
* How to troubleshoot a PVC stuck in `Pending`.

---

# Interview Questions

## Q1. What is a Persistent Volume in Kubernetes?

### Answer

A **Persistent Volume (PV)** is a cluster storage resource that provides persistent storage independent of the lifecycle of an individual Pod.

Simple answer:

```text
PV = Storage Resource
```

---

## Q2. What is a PVC?

### Answer

A **Persistent Volume Claim (PVC)** is a request for storage by an application.

Kubernetes binds the claim to a compatible Persistent Volume.

Simple flow:

```text
Pod
 |
 v
PVC
 |
 v
PV
```

---

## Q3. What is the difference between PV and PVC?

### Answer

A PV represents the storage resource, while a PVC represents the application's request for storage.

Simple answer:

```text
PV  = Storage
PVC = Request for Storage
```

For example:

```text
PV  -> 100Gi storage available
PVC -> Application requests 20Gi
```

---

## Q4. Can data survive if a Pod is deleted?

### Answer

Yes, if the Pod uses appropriately configured persistent storage through a PVC/PV.

The storage lifecycle is separate from the lifecycle of an individual Pod.

```text
Pod Deleted
     |
     v
PVC Still Exists
     |
     v
PV Still Exists
     |
     v
Data Can Remain
```

---

## Q5. What does `ReadWriteOnce` mean?

### Answer

`ReadWriteOnce` means the volume can be mounted read-write by workloads on a **single node at a time**, subject to the storage provider's capabilities.

```text
ReadWriteOnce = RWO
```

---

## Q6. What happens if a PVC remains in `Pending` state?

### Answer

It means Kubernetes cannot currently find or provision suitable storage for the claim.

I would first run:

```bash
kubectl describe pvc <pvc-name>
```

Then check:

* PVC events
* PV availability
* Requested capacity
* Access modes
* StorageClass
* CSI driver
* Cloud storage provisioning

---

## Q7. What is static provisioning?

### Answer

Static provisioning is when an administrator manually creates Persistent Volumes before applications create PVCs.

```text
Admin Creates PV
      |
      v
Developer Creates PVC
      |
      v
PVC Binds to PV
```

---

## Q8. What is dynamic provisioning?

### Answer

Dynamic provisioning automatically creates storage when a PVC requests it, typically using a **StorageClass** and **CSI driver**.

```text
PVC
 |
 v
StorageClass
 |
 v
CSI Driver
 |
 v
Storage Created Automatically
```

---

## Q9. How does persistent storage work in AWS EKS?

### Answer

A PVC can use a StorageClass backed by the **AWS EBS CSI driver**.

The flow is:

```text
Application
     |
     v
PVC
     |
     v
StorageClass
     |
     v
AWS EBS CSI Driver
     |
     v
AWS EBS Volume
     |
     v
PV
     |
     v
PVC Bound
     |
     v
Volume Mounted to Pod
```

The CSI driver provisions the EBS volume, Kubernetes creates or binds the PV, and the volume is attached and mounted for the Pod.

---

# Key Takeaways

1. **Containers are temporary, but application data may need to persist.**
2. **PV represents storage available to Kubernetes.**
3. **PVC represents an application's request for storage.**
4. **Pods normally consume persistent storage through PVCs.**
5. **Persistent storage can survive Pod deletion and recreation.**
6. **`emptyDir` and PV/PVC solve different storage requirements.**
7. **Static provisioning requires PVs to be created beforehand.**
8. **Dynamic provisioning uses StorageClass and CSI drivers to create storage automatically.**
9. **AWS EKS commonly uses the AWS EBS CSI driver for EBS-backed persistent storage.**
10. **A `Pending` PVC should be investigated through events, PVs, StorageClasses, and CSI components.**

---

# Final Architecture Summary

```text
                     Kubernetes Cluster
                            |
                            v
                     Application Pod
                            |
                            |
                   volumeMounts
                            |
                            v
                           PVC
                            |
                       Binding
                            |
                            v
                           PV
                            |
                            v
                    Persistent Storage
```

For this lab:

```text
nginx-pv-pod
     |
     | claimName: nginx-pvc
     v
 nginx-pvc
     |
     | Bound
     v
  nginx-pv
     |
     v
/mnt/nginx-data
```

The most important concept to remember is:

```text
Pod
 |
 v
PVC
 |
 v
PV
 |
 v
Persistent Storage
```

