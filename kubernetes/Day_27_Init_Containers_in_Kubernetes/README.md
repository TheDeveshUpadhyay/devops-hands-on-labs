# Day 27: Init Containers in Kubernetes

## Solution

## Objective

Learn what an **Init Container** is, why we use it, and how it works before the main application container starts.

An **Init Container** is a special container that:

- Runs **before the main application container**.
- Performs initialization or preparation work.
- Must complete successfully before the main application container starts.

The basic flow is:

```text
Init Container
      ↓
Performs initialization
      ↓
Completes successfully
      ↓
Main Container starts
      ↓
Application runs
```

---

## Scenario

Suppose your application should start only after some preparation is completed.

For example:

```text
                    Pod
                     │
            ┌────────┴────────┐
            │                 │
            ▼                 ▼
     Init Container      Main Container
            │                 │
            ▼                 ▼
   Prepare files /        Start
   check dependency      application
```

However, they do **not** start at the same time.

The execution order is:

```text
Init Container starts
        ↓
Performs preparation
        ↓
Init Container completes successfully
        ↓
Main Container starts
        ↓
Application runs
```

---

# Why Do We Need Init Containers?

Sometimes the main application cannot start immediately.

Before starting the application, we may need to:

- Wait for a database
- Wait for another service
- Download configuration
- Create files or directories
- Set permissions
- Perform initialization work

Instead of putting all this initialization logic inside the main application container, Kubernetes allows us to separate it using an **Init Container**.

```text
Preparation Logic
       ↓
Init Container
       ↓
Completes
       ↓
Application Container
       ↓
Starts Application
```

---

# Step 1: Create a Directory

Create a directory for today's lab:

```bash
mkdir day27-init-container
cd day27-init-container
vi pod.yaml
```

---

# Step 2: Create a Pod with an Init Container

Add the following configuration to `pod.yaml`:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: init-demo-pod

spec:
  initContainers:
    - name: init-container
      image: busybox:latest
      command:
        - /bin/sh
        - -c
        - echo "Initialization completed" > /data/message.txt
      volumeMounts:
        - name: shared-data
          mountPath: /data

  containers:
    - name: nginx-container
      image: nginx:latest
      volumeMounts:
        - name: shared-data
          mountPath: /usr/share/nginx/html

  volumes:
    - name: shared-data
      emptyDir: {}
```

---

# Step 3: Understand the Architecture

In this example, we have:

- One Pod
- One Init Container
- One main application container
- One shared `emptyDir` volume

The architecture looks like this:

```text
                         Pod
                          │
                ┌─────────┴─────────┐
                │                   │
                ▼                   ▼
        Init Container        Main Container
           BusyBox                Nginx
                │                   │
                └─────────┬─────────┘
                          │
                          ▼
                    shared-data
                      emptyDir
```

The Init Container and the Nginx container share the same volume.

However, they **do not initially run at the same time**.

The actual sequence is:

```text
Step 1

Init Container starts
        ↓
Creates message.txt
        ↓
Init Container completes

Step 2

Nginx Container starts
        ↓
Reads the same shared volume
        ↓
Application runs
```

---

# Step 4: Understand `initContainers`

The important section is:

```yaml
initContainers:
  - name: init-container
    image: busybox:latest
```

This tells Kubernetes:

> Create and run this container before starting the normal application containers.

This is different from:

```yaml
containers:
```

The `containers` section contains the normal application containers.

Think of it like this:

```text
initContainers
      ↓
Preparation Work
      ↓
Complete Successfully
      ↓
containers
      ↓
Main Application
```

---

# Step 5: Understand the Init Container Command

Our Init Container contains:

```yaml
command:
  - /bin/sh
  - -c
  - echo "Initialization completed" > /data/message.txt
```

This effectively runs:

```bash
/bin/sh -c 'echo "Initialization completed" > /data/message.txt'
```

The command creates:

```text
/data/message.txt
```

with the following content:

```text
Initialization completed
```

After the command completes, the Init Container exits successfully.

That is the expected behavior.

---

# Step 6: Why Does the Init Container Exit?

This is an important concept.

A normal application container usually keeps running.

For example:

```text
Nginx
  ↓
Web Server Starts
  ↓
Keeps Running Continuously
```

An Init Container usually performs a specific task and then exits.

For example:

```text
Init Container
      ↓
Create File
      ↓
Task Completed
      ↓
Exit Successfully
```

That successful exit tells Kubernetes:

> **Initialization is complete. You may now start the main application container.**

Therefore:

```text
Init Container exits successfully
            ≠
          Error
```

Instead:

```text
Exit Code 0
     ↓
Successful Initialization
```

---

# Step 7: Understand the Shared Volume

We define the following volume:

```yaml
volumes:
  - name: shared-data
    emptyDir: {}
```

The Init Container mounts it at:

```yaml
volumeMounts:
  - name: shared-data
    mountPath: /data
```

The Nginx container mounts the same volume at:

```yaml
volumeMounts:
  - name: shared-data
    mountPath: /usr/share/nginx/html
```

Notice that the mount paths are different.

### Init Container

```text
/data
```

### Nginx Container

```text
/usr/share/nginx/html
```

However, underneath they are accessing the **same `shared-data` volume**.

```text
                       shared-data
                        emptyDir
                           │
                 ┌─────────┴─────────┐
                 │                   │
                 ▼                   ▼
              /data        /usr/share/nginx/html
                 │                   │
                 ▼                   ▼
         Init Container       Nginx Container
```

Therefore, if the Init Container creates:

```text
/data/message.txt
```

the Nginx container sees the same file as:

```text
/usr/share/nginx/html/message.txt
```

---

# Step 8: Create the Pod

Apply the YAML:

```bash
kubectl apply -f pod.yaml
```

Expected output:

```text
pod/init-demo-pod created
```

---

# Step 9: Watch the Pod Start

Run:

```bash
kubectl get pods -w
```

You may see something similar to:

```text
NAME            READY   STATUS
init-demo-pod   0/1     Init:0/1
init-demo-pod   0/1     PodInitializing
init-demo-pod   1/1     Running
```

Let's understand these states.

## `Init:0/1`

```text
Init:0/1
```

means the Pod has one Init Container and it has not completed yet.

```text
0 completed
     ↓
out of
     ↓
1 Init Container
```

The Init Container is still running or waiting to complete.

## `PodInitializing`

```text
PodInitializing
```

means Kubernetes is preparing the Pod after initialization.

## `1/1 Running`

```text
READY   STATUS
1/1     Running
```

means the main Nginx container is now running and ready.

Press:

```text
Ctrl + C
```

to stop watching.

---

# Step 10: Check Pod Details

Run:

```bash
kubectl describe pod init-demo-pod
```
[O
You should find an Init Containers section similar to:

```text
Init Containers:

  init-container:

    State:
      Terminated

    Reason:
      Completed

    Exit Code:
      0
```

This is a **successful result**.

Do not assume:

```text
Terminated = Error
```

For an Init Container:

```text
State:     Terminated
Reason:    Completed
Exit Code: 0
```

means:

> **The Init Container completed successfully.**

---

# Step 11: Check the Init Container Logs

Run:

```bash
kubectl logs init-demo-pod -c init-container
```

In our current example, there may be no output because the `echo` output was redirected to a file.

Our command is:

```bash
echo "Initialization completed" > /data/message.txt
```

The output goes into:

```text
/data/message.txt
```

instead of standard output.

If you want output in the logs as well, you could use:

```yaml
command:
  - /bin/sh
  - -c
  - echo "Init container is running"; echo "Initialization completed" > /data/message.txt
```

Then:

```bash
kubectl logs init-demo-pod -c init-container
```

would show:

```text
Init container is running
```

---

# Step 12: Verify the File from Nginx

Enter the main Nginx container:

```bash
kubectl exec -it init-demo-pod -- /bin/bash
```

Inside the container, run:

```bash
cat /usr/share/nginx/html/message.txt
```

Expected output:

```text
Initialization completed
```

This proves the following:

```text
Init Container
      ↓
Created file in shared volume
      ↓
Init Container completed
      ↓
Main Container started
      ↓
Mounted the same shared volume
      ↓
Can access message.txt
```

Exit the container:

```bash
exit
```

---

# Step 13: Test Through Nginx

Because the file is available under:

```text
/usr/share/nginx/html
```

Nginx can serve it.

You could test:

```bash
kubectl exec init-demo-pod -- curl localhost/message.txt
```

However, depending on the Nginx image, `curl` may not be installed.

Another option is to create a temporary BusyBox Pod:

```bash
kubectl run testpod \
  --image=busybox:latest \
  -it \
  --rm \
  --restart=Never \
  -- sh
```

For today's Init Container concept, simply verifying the file using `cat` is enough.

---

# What Happens if the Init Container Fails?

Suppose we change the Init Container command to:

```yaml
command:
  - /bin/sh
  - -c
  - invalid-command
```

The command does not exist, so the Init Container fails.

The main Nginx container will **not start**.

You may see an Init-related failure state such as:

```text
Init:CrashLoopBackOff
```

The flow becomes:

```text
Init Container
      ↓
     Fails ❌
      ↓
Kubernetes retries it
      ↓
Main Container waits
      ↓
Nginx does not start
```

This is one of the most important behaviors of Init Containers:

> **The main application containers wait until all Init Containers complete successfully.**

---

# Multiple Init Containers

A Pod can contain more than one Init Container.

Example:

```yaml
initContainers:
  - name: init-one
    image: busybox
    command: ["sh", "-c", "echo first"]

  - name: init-two
    image: busybox
    command: ["sh", "-c", "echo second"]
```

Init Containers run **one by one**, not in parallel.

The order is:

```text
Init Container 1
       ↓
   Completes
       ↓
Init Container 2
       ↓
   Completes
       ↓
Main Application Container
```

If you have three Init Containers:

```text
Init 1 → Init 2 → Init 3 → Main Container
```

If `Init 2` fails:

```text
Init 1 ✅
   ↓
Init 2 ❌
   ↓
Init 3 does not start
   ↓
Main Container does not start
```

Therefore, every Init Container must complete successfully before Kubernetes moves to the next one.

---

# Real-World Example: Wait for a Database

Suppose your application requires MySQL.

You do not want the application to start until the database is reachable.

The architecture could look like:

```text
               Application Pod
                     │
             ┌───────┴────────┐
             │                │
             ▼                ▼
       Init Container   Application Container
             │                │
             ▼                │
        Check MySQL            │
             ↓                │
      Database available?      │
         │         │           │
        No        Yes          │
         │         │           │
      Wait 5s      │           │
         │         │           │
         └──→ Check Again      │
                   │           │
                   ▼           │
             Init Completes    │
                   └───────────┤
                               ▼
                        Start Application
```

Example configuration:

```yaml
initContainers:
  - name: wait-for-database
    image: busybox:latest
    command:
      - /bin/sh
      - -c
      - |
        until nc -z mysql-service 3306;
        do
          echo "Waiting for MySQL...";
          sleep 5;
        done
```

This means:

```text
Check mysql-service:3306
          ↓
Is it available?
     ↙          ↘
   No            Yes
   ↓              ↓
Wait 5 sec    Init completes
   ↓              ↓
Check again   Application starts
```

---

# What Does `nc -z mysql-service 3306` Mean?

Let's break it down.

## `nc`

```text
nc
↓
netcat
```

`nc` stands for **netcat**.

It can be used for network connectivity testing.

## `-z`

```text
-z
↓
Check whether a port is open
without sending application data
```

## `mysql-service`

```text
mysql-service
↓
Kubernetes Service name
```

This is the service name used to reach MySQL.

## `3306`

```text
3306
↓
Default MySQL port
```

Therefore:

```bash
nc -z mysql-service 3306
```

means:

> Check whether `mysql-service` is reachable on TCP port `3306`.

The complete logic is:

```text
MySQL available?
      ↓
     No
      ↓
Wait 5 seconds
      ↓
Check again
      ↓
MySQL becomes available
      ↓
Init Container completes
      ↓
Application starts
```

---

# Init Container vs Normal Container

| Feature | Init Container | Normal Container |
|---|---|---|
| **Execution** | Runs before application containers | Runs after Init Containers complete |
| **Purpose** | Initialization/setup work | Runs the actual application |
| **Lifecycle** | Expected to finish | Usually keeps running |
| **Multiple containers** | Run sequentially | Application containers can run together |
| **Failure behavior** | Failure blocks main container startup | Failure may restart that container |
| **YAML section** | `initContainers` | `containers` |
| **Example** | Wait for database | Run Nginx application |

---

# Init Container vs Sidecar Container

Init Containers and Sidecar containers serve different purposes.

## Init Container

An Init Container runs **before** the application and normally exits after completing its task.

```text
Init Container
      ↓
Preparation
      ↓
Completed
      ↓
Main Container
      ↓
Running
```

Example:

```text
Init Container
      ↓
Prepare configuration
      ↓
Exit successfully
```

## Sidecar Container

A Sidecar runs alongside the main application.

```text
                 Pod
                  │
          ┌───────┴───────┐
          │               │
          ▼               ▼
    Main Container     Sidecar
          │               │
       Running          Running
```

Example:

```text
Main Container
      ↓
Runs Application

Sidecar
      ↓
Continuously Ships Logs
```

### Simple Difference

```text
Init Container
      ↓
Runs BEFORE application
      ↓
Completes and exits


Sidecar Container
      ↓
Runs ALONGSIDE application
      ↓
Usually continues running
```

---

# Useful Commands

| Command | Purpose |
|---|---|
| `kubectl apply -f pod.yaml` | Create the Pod |
| `kubectl get pods` | Check Pod status |
| `kubectl get pods -w` | Watch initialization live |
| `kubectl describe pod init-demo-pod` | Inspect Init Container status and events |
| `kubectl logs init-demo-pod -c init-container` | Check Init Container logs |
| `kubectl exec -it init-demo-pod -- /bin/bash` | Enter the main container |
| `kubectl delete pod init-demo-pod` | Delete the Pod |

---

# Troubleshooting Init Containers

If your Pod is stuck in:

```text
Init:0/1
```

or:

```text
Init:CrashLoopBackOff
```

start by checking the Pod:

```bash
kubectl get pods
```

Then describe it:

```bash
kubectl describe pod <pod-name>
```

Next, check the logs of the specific Init Container:

```bash
kubectl logs <pod-name> -c <init-container-name>
```

Example:

```bash
kubectl logs init-demo-pod -c init-container
```

Look for errors such as:

- Wrong command
- Permission denied
- DNS failure
- Service unavailable
- File or directory issue
- Network connection failure

---

# Troubleshooting Flow

Follow this sequence:

```text
Pod stuck in Init state
        ↓
kubectl get pods
        ↓
kubectl describe pod <pod-name>
        ↓
Identify failing Init Container
        ↓
kubectl logs <pod-name> -c <init-container-name>
        ↓
Find root cause
        ↓
Fix YAML / Configuration
        ↓
kubectl apply -f pod.yaml
        ↓
Verify Pod
```

The important point is:

> **Do not troubleshoot the main application container first if the Pod is still stuck in the Init phase.**

The main container may not have started yet.

---

# Expected Outcome

By completing **Day 27**, you should understand:

- [x] What Init Containers are.
- [x] Init Containers execute before application containers.
- [x] Init Containers must complete successfully before the main container starts.
- [x] Multiple Init Containers run sequentially.
- [x] Init Containers can share volumes with application containers.
- [x] A failed Init Container blocks application startup.
- [x] How Init Containers can wait for dependencies.
- [x] How to troubleshoot Init Containers.
- [x] Difference between Init Containers and normal containers.
- [x] Difference between Init Containers and Sidecars.

---

# Interview Questions

## Q1. What Is an Init Container in Kubernetes?

### Answer

An **Init Container** is a special container that runs and completes before the main application containers start.

It is generally used for initialization or dependency checks.

Examples include:

- Waiting for a database
- Creating files
- Downloading configuration
- Setting permissions
- Checking another service

### Short Interview Answer

> An Init Container runs before the main application containers and must complete successfully before the application containers start. I can use it for initialization tasks such as waiting for a database, preparing configuration, or creating required files.

---

## Q2. Give a Real-World Example of an Init Container

### Answer

A common real-world example is waiting for a database.

Suppose an application depends on MySQL.

```text
Application Pod
      ↓
Init Container
      ↓
Check MySQL
      ↓
Wait Until Available
      ↓
Init Completes
      ↓
Application Container Starts
```

### Short Interview Answer

> I can use an Init Container to wait for a database or another dependent service to become available before starting the main application container.

---

## Q3. What Happens if an Init Container Fails?

### Answer

If an Init Container fails, the main application containers will not start.

The Pod remains in the initialization phase while Kubernetes handles the failed Init Container according to the Pod's restart behavior.

```text
Init Container
      ↓
    Fails ❌
      ↓
Main Container waits
      ↓
Application does not start
```

### Short Interview Answer

> If an Init Container fails, the main application containers do not start. Kubernetes continues handling the failed Init Container according to the Pod restart behavior until initialization succeeds or the Pod is removed.

---

## Q4. Can We Have Multiple Init Containers?

### Answer

Yes.

A Pod can contain multiple Init Containers.

Kubernetes executes them **sequentially in the order they are defined**.

```text
Init 1
  ↓
Init 2
  ↓
Init 3
  ↓
Main Container
```

Each Init Container must complete successfully before the next one starts.

---

## Q5. Do Multiple Init Containers Run in Parallel?

### Answer

No.

Multiple Init Containers run sequentially.

```text
Init 1
  ↓
Completed
  ↓
Init 2
  ↓
Completed
  ↓
Init 3
  ↓
Completed
  ↓
Main Containers
```

If one Init Container fails, Kubernetes does not continue to the next Init Container until the failed initialization is successfully completed.

---

## Q6. What Is the Difference Between an Init Container and a Sidecar?

### Answer

An Init Container runs before the main application and normally exits after completing its task.

A Sidecar runs alongside the main application and usually continues running for the life of the Pod.

| Init Container | Sidecar Container |
|---|---|
| Runs before main application | Runs alongside main application |
| Performs initialization | Provides supporting functionality |
| Expected to complete | Usually keeps running |
| Failure blocks application startup | Runs with the application |
| Example: Wait for database | Example: Log shipping |

### Short Interview Answer

> An Init Container runs before the application and completes its initialization task before the main container starts. A Sidecar runs alongside the main application and usually continues running, for example to collect or ship logs.

---

## Q7. How Do You Check Init Container Logs?

Use:

```bash
kubectl logs <pod-name> -c <init-container-name>
```

Example:

```bash
kubectl logs init-demo-pod -c init-container
```

The `-c` option specifies the container whose logs you want to view.

---

## Q8. Why Might a Pod Show `Init:0/1`?

### Answer

```text
Init:0/1
```

means the Pod has one Init Container and **zero Init Containers have completed successfully yet**.

You should investigate using:

```bash
kubectl describe pod <pod-name>
```

and:

```bash
kubectl logs <pod-name> -c <init-container-name>
```

### Troubleshooting Flow

```text
Init:0/1
   ↓
Check Pod details
   ↓
Check Init Container
   ↓
Check Events
   ↓
Check Init Container Logs
   ↓
Find Root Cause
```

---

# Key Takeaways

```text
                     Kubernetes Pod
                           │
                           ▼
                    Init Container
                           │
                 Performs Setup Work
                           │
                    Completes ✅
                           │
                           ▼
                 Application Container
                           │
                           ▼
                    Application Runs
```

Remember these important points:

1. **Init Containers run before application containers.**
2. **They are designed to complete their task and exit.**
3. **The main application waits for successful initialization.**
4. **Multiple Init Containers execute sequentially.**
5. **Init Containers can share volumes with application containers.**
6. **They are useful for dependency checks and preparation tasks.**
7. **If initialization fails, troubleshoot the Init Container before the main application.**

---

# Cleanup

After completing the lab, delete the Pod:

```bash
kubectl delete pod init-demo-pod
```

Verify:

```bash
kubectl get pods
```

---

# Final Learning

The main concept to remember is:

```text
Init Container
      ↓
Prepare
      ↓
Complete
      ↓
Main Container
      ↓
Run Application
```

An Init Container allows us to **separate initialization logic from the main application container**.

This makes the Pod design easier to understand because initialization tasks and application runtime responsibilities are handled separately.

---

## Day 27 Completed

**Topic:** Kubernetes Init Containers  
**Focus:** Initialization, Dependencies, Shared Volumes, Troubleshooting  
**Status:** Completed ✅
