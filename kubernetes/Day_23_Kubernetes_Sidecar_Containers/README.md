# Day 23: Kubernetes Sidecar Containers

## Solution

### Objective

Learn what **Sidecar Containers** are in Kubernetes, why they are used, and how they work alongside the main application container inside the same Pod.

---

## What is a Sidecar Container?

A **Sidecar Container** is a secondary container that runs inside the **same Pod** as the main application container.

The main container runs the application, while the sidecar container provides an additional supporting function.

Think of it like this:

```text
Pod
│
├── Main Container
│   └── Application
│
└── Sidecar Container
    └── Supporting task
```

Both containers share the same Pod environment and can communicate with each other.

---

## Why Do We Need Sidecar Containers?

Sometimes an application needs additional functionality, but we don't want to add that functionality directly into the application.

For example, suppose your application generates logs:

```text
Application
    ↓
application.log
```

We could use a sidecar container to read those logs and send them somewhere else:

```text
                    Same Pod
┌────────────────────────────────────┐
│                                    │
│  Main Container                    │
│  ┌──────────────────────────────┐  │
│  │ Application                  │  │
│  │                              │  │
│  │ writes application.log       │  │
│  └───────────────┬──────────────┘  │
│                  │                 │
│                  ↓                 │
│             Shared Volume          │
│                  │                 │
│                  ↓                 │
│  Sidecar Container                 │
│  ┌──────────────────────────────┐  │
│  │ Log Collector                │  │
│  │                              │  │
│  │ reads application.log        │  │
│  └───────────────┬──────────────┘  │
│                  │                 │
└──────────────────┼─────────────────┘
                   ↓
             Logging System
```

The application doesn't need to know how logs are collected.

---

## Example: Sidecar Container for Log Collection

Suppose we have an Nginx application.

The main container writes logs to:

```text
/var/log/app/app.log
```

A sidecar container can read the same file and process or forward the logs.

### Kubernetes YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: sidecar-demo

spec:
  containers:

    # Main Application Container
    - name: app
      image: nginx:latest

      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/app

    # Sidecar Container
    - name: log-sidecar
      image: busybox:latest

      command:
        - sh
        - -c
        - |
          while true; do
            if [ -f /var/log/app/app.log ]; then
              cat /var/log/app/app.log
            fi
            sleep 10
          done

      volumeMounts:
        - name: shared-logs
          mountPath: /var/log/app

  volumes:
    - name: shared-logs
      emptyDir: {}
```

---

## Understanding the YAML

There are **two containers** inside the same Pod:

```yaml
containers:
  - name: app
    image: nginx:latest

  - name: log-sidecar
    image: busybox:latest
```

The first container is the **main application**.

The second container is the **sidecar**.

---

## Shared Volume

Both containers mount:

```yaml
volumeMounts:
  - name: shared-logs
    mountPath: /var/log/app
```

And the Pod defines:

```yaml
volumes:
  - name: shared-logs
    emptyDir: {}
```

This means Kubernetes creates a shared directory for the Pod.

Conceptually:

```text
emptyDir
    │
    ├── Main Container
    │      ↓
    │   writes logs
    │
    └── Sidecar Container
           ↓
        reads logs
```

So the two containers can exchange files through the shared volume.

---

## Important Point: Same Pod

A sidecar container is **not normally a separate Pod**.

It runs inside the same Pod:

```text
Pod
│
├── Container 1 → Application
│
└── Container 2 → Sidecar
```

Because they are in the same Pod, containers can share:

* Network namespace
* IP address
* Volumes
* Some process-related resources, when configured appropriately

For networking, both containers use the **same Pod IP**.

For example:

```text
Pod IP: 10.244.1.20

Application Container
        │
        │ localhost
        ↓
Sidecar Container
```

They can communicate using:

```text
localhost
```

because they share the same network namespace.

---

## Common Sidecar Use Cases

Sidecar containers are commonly used for:

### 1. Log Collection

```text
Application
    ↓
Log File
    ↓
Sidecar
    ↓
Logging System
```

Examples:

* Fluent Bit
* Fluentd
* Logstash

---

### 2. Proxy

A sidecar can act as a proxy for the application.

For example:

```text
Application
     ↓
Sidecar Proxy
     ↓
Other Service
```

This approach is used in service-mesh architectures.

Examples include:

* Envoy
* Istio sidecar proxy

---

### 3. Monitoring

A sidecar can collect application metrics or expose additional monitoring information.

```text
Application
     ↓
Sidecar
     ↓
Monitoring System
```

---

### 4. Configuration Management

A sidecar can periodically retrieve or update configuration files that the main application consumes.

```text
Configuration Source
        ↓
      Sidecar
        ↓
  Shared Volume
        ↓
    Application
```

---

## Sidecar vs Main Container

| Main Container               | Sidecar Container                   |
| ---------------------------- | ----------------------------------- |
| Runs the primary application | Supports the application            |
| Business logic               | Auxiliary functionality             |
| Usually essential            | Usually supporting                  |
| Serves user requests         | Logging, proxying, monitoring, etc. |
| Application-specific         | Often reusable                      |

---

## How to Check Multiple Containers in a Pod

After creating the Pod:

```bash
kubectl apply -f sidecar-pod.yaml
```

Check the Pod:

```bash
kubectl get pods
```

You may see:

```text
NAME           READY   STATUS
sidecar-demo   2/2     Running
```

### What does `2/2` mean?

It means:

```text
2 containers are running
2 containers are ready
```

You can see the individual containers with:

```bash
kubectl describe pod sidecar-demo
```

---

## How to Check Logs

Because the Pod has two containers, Kubernetes needs to know **which container's logs** you want.

### Main Container

```bash
kubectl logs sidecar-demo -c app
```

### Sidecar

```bash
kubectl logs sidecar-demo -c log-sidecar
```

Here:

```text
-c app
```

means:

> Show logs from the `app` container.

And:

```text
-c log-sidecar
```

means:

> Show logs from the `log-sidecar` container.

---

## Important Interview Point

If an interviewer asks:

### What is a Sidecar Container in Kubernetes?

You can answer:

A sidecar container is a secondary container running in the same Pod as the main application container. It provides supporting functionality such as log collection, monitoring, proxying, or configuration management. Since both containers share the same Pod network and can share volumes, the sidecar can work closely with the main application without modifying the application itself.

---

## Simple Real-World Example

Imagine a house:

```text
House
│
├── Main Person
│   └── Does the main work
│
└── Assistant
    └── Provides support
```

In Kubernetes:

```text
Pod
│
├── Main Container
│   └── Runs application
│
└── Sidecar Container
    └── Provides support
```

So remember:

**Main container = does the primary job.**

**Sidecar container = helps the main container do its job.**

---

## Key Takeaway

```text
                    POD
                     │
       ┌─────────────┴─────────────┐
       │                           │
 Main Container              Sidecar Container
       │                           │
   Application               Supporting task
       │                           │
       └──────── Shared ───────────┘
                 Volume
```

**One Pod → Multiple containers → One main application + supporting sidecar(s).**

