# 🚀 Day 06: Docker EXEC Operations

## 📖 Overview

This project demonstrates how to use the `docker exec` command to execute commands inside a running Docker container without stopping or restarting it. It covers common administration, troubleshooting, and debugging tasks performed in real-world DevOps environments.

---

## 🎯 Objective

Learn how to use `docker exec` to:

- Execute commands inside a running container
- Open an interactive shell
- Inspect the container environment
- Perform basic administration tasks
- Troubleshoot running applications

---

## 🌍 Real-World Scenario

In production environments, containers are already running and hosting applications. Instead of creating a new container, DevOps engineers use `docker exec` to:

- Check application logs
- Verify installed software
- Modify configuration files
- Troubleshoot issues
- Execute administrative commands

This approach allows engineers to inspect and manage containers without interrupting the running application.

---

## 🛠️ Prerequisites

- Docker Installed
- Basic understanding of Docker Images and Containers
- Ubuntu image available locally

---

# 🚀 Implementation

## Step 1: Pull the Ubuntu Image

Pull the latest Ubuntu image from Docker Hub.

```bash
docker pull ubuntu
```

Verify the downloaded image:

```bash
docker images
```

**Expected Output**

```text
REPOSITORY    TAG       IMAGE ID
ubuntu        latest    xxxxxxxxx
```

---

## Step 2: Start a Container in Detached Mode

Run an Ubuntu container in detached mode.

```bash
docker run -dit --name ubuntu-container ubuntu
```

Verify the running container:

```bash
docker ps
```

**Expected Output**

```text
CONTAINER ID    IMAGE     STATUS      NAMES
abcd1234        ubuntu    Up 2 mins   ubuntu-container
```

---

## Step 3: Execute a Command Inside the Running Container

Run the `ls` command inside the container.

```bash
docker exec ubuntu-container ls
```

**Expected Output**

```text
bin
boot
dev
etc
home
lib
proc
root
tmp
usr
var
```

**Command Breakdown**

- `docker exec` → Execute a command inside a running container
- `ubuntu-container` → Container name
- `ls` → Command executed inside the container

---

## Step 4: Check the Current User

```bash
docker exec ubuntu-container whoami
```

**Expected Output**

```text
root
```

---

## Step 5: Check Operating System Information

```bash
docker exec ubuntu-container cat /etc/os-release
```

**Expected Output**

```text
NAME="Ubuntu"
VERSION="24.04 LTS"
```

---

## Step 6: Open an Interactive Shell

```bash
docker exec -it ubuntu-container bash
```

Inside the container:

```bash
pwd
```

**Expected Output**

```text
/
```

Exit the container:

```bash
exit
```

---

## Step 7: Create a File Inside the Container

```bash
docker exec ubuntu-container touch demo.txt
```

Verify:

```bash
docker exec ubuntu-container ls
```

**Expected Output**

```text
bin
boot
demo.txt
dev
etc
home
```

---

## Step 8: Execute Multiple Commands

```bash
docker exec ubuntu-container bash -c "mkdir test && touch test/file1.txt && ls test"
```

**Expected Output**

```text
file1.txt
```

---

## Step 9: Stop the Container

```bash
docker stop ubuntu-container
```

Verify:

```bash
docker ps
```

The container should no longer appear in the list of running containers.

---

# 🔄 Workflow

```text
Docker Image
      │
      ▼
Running Container
      │
      ▼
docker exec
      │
      ▼
Execute Commands
      │
      ▼
Troubleshooting & Administration
```

---

# 🌍 Real Project Example

Suppose an **Nginx** container is running in production and you want to verify its configuration without stopping the application.

Connect to the running container:

```bash
docker exec -it nginx-container bash
```

Verify the Nginx configuration:

```bash
nginx -t
```

**Expected Output**

```text
syntax is ok
test is successful
```

This enables troubleshooting while keeping the application online.

---

# ⚖️ docker exec vs docker run

| docker exec | docker run |
|-------------|------------|
| Executes commands inside an existing running container | Creates and starts a new container |
| Requires a running container | Creates a new container from an image |
| Used for troubleshooting and administration | Used to launch applications |

---

# 📋 Common docker exec Commands

| Command | Description |
|----------|-------------|
| `docker exec <container> ls` | Execute a single command |
| `docker exec -it <container> bash` | Open an interactive shell |
| `docker exec <container> whoami` | Display the current user |
| `docker exec <container> pwd` | Show the current working directory |
| `docker exec <container> env` | Display environment variables |

---

# ✅ Expected Outcome

- Ubuntu container created successfully.
- Commands executed inside the running container.
- Interactive shell accessed successfully.
- File created inside the container.
- Multiple commands executed using `docker exec`.
- Container stopped successfully.

---

# 🎯 Interview Questions

### Q1. What is `docker exec`?

**Answer:**

`docker exec` is used to execute commands inside an existing running Docker container without stopping or restarting it.

---

### Q2. What is the syntax of `docker exec`?

**Answer:**

```bash
docker exec [OPTIONS] <container-name> <command>
```

---

### Q3. What is the difference between `docker exec` and `docker run`?

| docker exec | docker run |
|-------------|------------|
| Executes commands in a running container | Creates and starts a new container |
| Used for administration and troubleshooting | Used to launch applications |

---

### Q4. How do you open an interactive shell inside a container?

**Answer:**

```bash
docker exec -it <container-name> bash
```

---

### Q5. Why is `docker exec` commonly used in DevOps?

**Answer:**

It allows engineers to troubleshoot, inspect, and administer running containers without interrupting the application.

---

### Q6. Can `docker exec` be used on a stopped container?

**Answer:**

No. The target container must be in the **running** state before `docker exec` can execute commands inside it.

---

# 📚 Key Takeaways

- `docker exec` executes commands inside running containers.
- It is widely used for troubleshooting and administration.
- Interactive shells can be opened without restarting containers.
- Multiple commands can be executed using `bash -c`.
- It is one of the most commonly used Docker commands in production environments.

---

# 🎉 Conclusion

Today, I learned how to use the `docker exec` command to inspect, troubleshoot, and administer running Docker containers. This command is essential for day-to-day DevOps operations, enabling engineers to interact with live containers efficiently without disrupting running applications.

---

## ⭐ Support

If you found this project helpful, consider giving this repository a **⭐ Star** and follow my **DevOps Hands-on Labs** journey.

Happy Learning! 🚀
