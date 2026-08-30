# Day 25: Print Environment Variables

## Solution

### Objective

Learn how to **create environment variables inside a Kubernetes Pod** and print them from inside the container.

Environment variables are commonly used to provide configuration to applications without hardcoding values inside the application code.

---

## Scenario

Suppose we have an application that needs configuration like:

```text
APP_ENV = production

APP_VERSION = 1.0
```

Instead of hardcoding these values in the application, Kubernetes can provide them as **environment variables**.

The flow is:

```text
Kubernetes Pod
      ↓
Environment Variables
      ↓
Container
      ↓
Application
```

---

## Step 1: Create a Directory

Create a directory:

```bash
mkdir day25-environment-variables
```

Move into the directory:

```bash
cd day25-environment-variables
```

Create the YAML file:

```bash
vi pod.yaml
```

---

## Step 2: Create the Pod YAML

Create the following `pod.yaml` file:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: env-pod

spec:
  containers:
    - name: env-container
      image: busybox:latest

      command: ["/bin/sh", "-c"]

      args:
        - sleep 3600

      env:
        - name: APP_ENV
          value: "production"

        - name: APP_VERSION
          value: "1.0"

        - name: APP_NAME
          value: "my-application"
```

Save the file.

---

## Step 3: Understand `env`

This section is the main focus of today's task:

```yaml
env:
  - name: APP_ENV
    value: "production"

  - name: APP_VERSION
    value: "1.0"

  - name: APP_NAME
    value: "my-application"
```

It means Kubernetes creates these environment variables inside the container:

```text
APP_ENV=production

APP_VERSION=1.0

APP_NAME=my-application
```

Think of it as:

```text
Kubernetes
     ↓
Creates environment variables
     ↓
Container
     ↓
Application can read them
```

---

## Step 4: Why Are We Using BusyBox?

We are using:

```yaml
image: busybox:latest
```

**BusyBox** is a small Linux image that contains basic commands such as:

```text
sh
echo
env
printenv
ls
cat
```

It is useful for this lab because we only need a simple container to test environment variables.

---

## Step 5: Why Are We Using `sleep 3600`?

We have:

```yaml
command: ["/bin/sh", "-c"]

args:
  - sleep 3600
```

Let's understand this:

```text
/bin/sh
   ↓
Starts the Linux shell
   ↓
-c
   ↓
Execute the provided command
   ↓
sleep 3600
   ↓
Keep the container running
```

`3600` means **3600 seconds**, which is:

```text
3600 seconds = 60 minutes = 1 hour
```

So:

```bash
sleep 3600
```

keeps the container running for one hour.

Without this command, the container would finish its command and stop.

We want the container to remain running so we can enter it and inspect the environment variables.

---

## Step 6: Create the Pod

Apply the configuration:

```bash
kubectl apply -f pod.yaml
```

Expected output:

```text
pod/env-pod created
```

---

## Step 7: Check the Pod

Run:

```bash
kubectl get pods
```

Expected output:

```text
NAME      READY   STATUS
env-pod   1/1     Running
```

The `1/1` means:

```text
1 container exists
1 container is ready
```

---

## Step 8: Enter the Container

Run:

```bash
kubectl exec -it env-pod -- /bin/sh
```

You are now inside the BusyBox container.

You may see:

```text
/ #
```

This means you are now working inside the container's shell.

---

## Step 9: Print a Specific Environment Variable

Print `APP_ENV`:

```bash
echo $APP_ENV
```

Expected output:

```text
production
```

Print `APP_VERSION`:

```bash
echo $APP_VERSION
```

Expected output:

```text
1.0
```

Print `APP_NAME`:

```bash
echo $APP_NAME
```

Expected output:

```text
my-application
```

---

## Step 10: Use `printenv`

You can also use:

```bash
printenv APP_ENV
```

Expected output:

```text
production
```

Similarly:

```bash
printenv APP_VERSION
```

Expected output:

```text
1.0
```

And:

```bash
printenv APP_NAME
```

Expected output:

```text
my-application
```

---

## Step 11: Print All Environment Variables

You can print all environment variables using:

```bash
printenv
```

or:

```bash
env
```

You will see many environment variables, including:

```text
APP_ENV=production

APP_VERSION=1.0

APP_NAME=my-application
```

You may also see other environment variables automatically available inside the container.

To filter only application-related variables, run:

```bash
printenv | grep APP
```

Example output:

```text
APP_ENV=production
APP_VERSION=1.0
APP_NAME=my-application
```

---

## Step 12: Exit the Container

Exit the container:

```bash
exit
```

---

## Step 13: Verify Environment Variables Without Entering the Container

You can also run the command directly from your terminal:

```bash
kubectl exec env-pod -- printenv APP_ENV
```

Expected output:

```text
production
```

This is useful because you don't need to manually enter the container.

You can also run:

```bash
kubectl exec env-pod -- printenv APP_VERSION
```

Expected output:

```text
1.0
```

Check `APP_NAME`:

```bash
kubectl exec env-pod -- printenv APP_NAME
```

Expected output:

```text
my-application
```

---

# Important Concept

There are two different things here.

## Kubernetes YAML

```yaml
env:
  - name: APP_ENV
    value: "production"
```

This **creates** the environment variable.

## Linux Command

```bash
echo $APP_ENV
```

This **reads and prints** the environment variable.

So the complete flow is:

```text
YAML
  ↓
Kubernetes creates APP_ENV
  ↓
Container receives APP_ENV
  ↓
echo $APP_ENV
  ↓
production
```

---

# Environment Variable vs Command

Don't confuse these two commands:

```bash
echo $APP_ENV
```

and:

```bash
printenv APP_ENV
```

Both can display the value of the environment variable.

## `echo $APP_ENV`

```text
echo
  ↓
Shell expands the variable
  ↓
Displays its value
```

## `printenv APP_ENV`

```text
printenv
  ↓
Looks up the environment variable
  ↓
Displays its value
```

---

# Real Project Example

Suppose you have a Spring Boot application.

Instead of hardcoding:

```text
database host = 10.0.1.50
```

you can provide the database configuration using environment variables:

```yaml
env:
  - name: DB_HOST
    value: "10.0.1.50"

  - name: DB_PORT
    value: "5432"

  - name: DB_NAME
    value: "mydb"
```

The application can then read:

```text
DB_HOST

DB_PORT

DB_NAME
```

This makes the same application image usable in different environments.

For example:

### Development

```text
DB_HOST=dev-db
```

### Testing

```text
DB_HOST=test-db
```

### Production

```text
DB_HOST=prod-db
```

The application image does not need to change.

This provides a clean separation between:

```text
Application Code
        +
Environment Configuration
```

---

# Important: Don't Put Passwords Like This

You should **not normally put sensitive information** such as passwords directly into your Pod YAML.

For example:

```yaml
env:
  - name: DB_PASSWORD
    value: "mypassword"
```

This is not a good practice for sensitive data.

For sensitive information, Kubernetes provides **Secrets**.

The general approach is:

```text
Normal Configuration
        ↓
    ConfigMap
```

```text
Sensitive Configuration
        ↓
      Secret
```

---

# Environment Variables from ConfigMap

In real projects, environment variables are often loaded from a ConfigMap instead of writing every value directly into the Pod YAML.

For example:

```yaml
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
```

The flow becomes:

```text
ConfigMap
    ↓
Environment Variable
    ↓
Container
    ↓
Application
```

This is more maintainable for application configuration.

---

# Useful Commands

| Command | Purpose |
|---|---|
| `kubectl apply -f pod.yaml` | Create or update the Pod |
| `kubectl get pods` | Check Pod status |
| `kubectl describe pod env-pod` | View Pod details and events |
| `kubectl exec -it env-pod -- /bin/sh` | Enter the container |
| `echo $APP_ENV` | Print a specific environment variable |
| `printenv APP_ENV` | Print a specific environment variable |
| `printenv` | Print all environment variables |
| `env` | Print all environment variables |
| `printenv \| grep APP` | Filter application environment variables |
| `kubectl exec env-pod -- printenv APP_ENV` | Print a variable without entering the container |
| `kubectl delete pod env-pod` | Delete the Pod |

---

# Expected Outcome

After completing this task, you should be able to:

- Create environment variables in a Kubernetes Pod.
- Understand `env`, `name`, and `value`.
- Keep a container running using `sleep`.
- Enter a container using `kubectl exec`.
- Print environment variables using `echo`.
- Print environment variables using `printenv`.
- Print all environment variables using `env`.
- Verify environment variables without entering the container.
- Understand how applications consume environment variables.
- Understand why ConfigMaps and Secrets are used in real projects.

---

# Interview Questions

## Q1. What is an environment variable in Kubernetes?

### Answer

An environment variable is a **key-value configuration** provided to a container and accessible to the application running inside that container.

For example:

```text
APP_ENV=production
```

The application can read this value and use it for configuration.

---

## Q2. How do you define an environment variable in a Pod?

Use the `env` section:

```yaml
env:
  - name: APP_ENV
    value: "production"
```

Here:

```text
APP_ENV
    ↓
Environment variable name

production
    ↓
Environment variable value
```

---

## Q3. How do you check an environment variable inside a container?

You can use:

```bash
echo $APP_ENV
```

or:

```bash
printenv APP_ENV
```

Both commands display:

```text
production
```

---

## Q4. How do you print all environment variables?

Use:

```bash
printenv
```

or:

```bash
env
```

---

## Q5. How can you provide configuration from a ConfigMap?

You can use `valueFrom` with `configMapKeyRef`:

```yaml
env:
  - name: APP_ENV
    valueFrom:
      configMapKeyRef:
        name: app-config
        key: APP_ENV
```

The value is retrieved from the ConfigMap:

```text
ConfigMap
    ↓
APP_ENV
    ↓
Container Environment Variable
    ↓
Application
```

---

## Q6. Where should sensitive values such as passwords be stored?

### Answer

Sensitive configuration should generally be stored in a **Kubernetes Secret**, rather than hardcoded directly in the Pod specification.

For example:

```text
Application Configuration
        ↓
     ConfigMap
```

```text
Sensitive Configuration
        ↓
       Secret
```

---

## Q7. Why are environment variables useful?

### Answer

Environment variables allow applications to receive configuration without modifying the application code.

For example:

```text
Same Application Image
        │
        ├── Development
        │      ↓
        │   APP_ENV=dev
        │
        ├── Testing
        │      ↓
        │   APP_ENV=test
        │
        └── Production
               ↓
           APP_ENV=production
```

The application image remains the same while the configuration changes based on the environment.

---

## Q8. What is the difference between `echo $APP_ENV` and `printenv APP_ENV`?

### Answer

Both commands can display the value of an environment variable.

```bash
echo $APP_ENV
```

The shell expands `$APP_ENV` and passes the value to `echo`.

```bash
printenv APP_ENV
```

The `printenv` command directly retrieves and prints the value of `APP_ENV`.

---

# Key Takeaway

The most important concept from today's lab is:

```text
Kubernetes YAML
      ↓
Environment Variable
      ↓
Container
      ↓
Application
      ↓
Reads Configuration
```

For example:

```text
APP_ENV=production

APP_VERSION=1.0

APP_NAME=my-application
```

The application can consume these values without hardcoding them into the application image.

This gives us a clean separation:

```text
Application Code
      +
Environment Configuration
```

Instead of building a different application image for every environment, the same image can receive different configuration values.

```text
                    Same Application Image
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ↓              ↓              ↓
        Development       Testing       Production
              │              │              │
              ↓              ↓              ↓
           dev-db         test-db        prod-db
```

---

# Day 25 Concept

```text
Create Pod
    ↓
Define Environment Variables
    ↓
Start Container
    ↓
Enter Container
    ↓
Use echo / printenv
    ↓
Verify Environment Variables
    ↓
Understand ConfigMap & Secret
    ↓
Application Consumes Configuration
```

## Final Takeaway

**Environment variables allow Kubernetes to provide configuration to containers without hardcoding environment-specific values into the application code.**

The basic flow is:

```text
Kubernetes
    ↓
env
    ↓
Container
    ↓
Environment Variables
    ↓
Application
```

For production environments:

```text
Non-sensitive configuration
        ↓
    ConfigMap
```

```text
Sensitive configuration
        ↓
      Secret
```

**Day 25 Completed: Print Environment Variables in Kubernetes ✅**
