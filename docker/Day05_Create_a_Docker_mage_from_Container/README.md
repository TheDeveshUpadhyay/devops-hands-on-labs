# 🐳 Day 05: Create a Docker Image from a Container

## 📖 Overview

This project demonstrates how to create a custom Docker image from an existing container using the `docker commit` command. It explains the complete workflow of modifying a running container, saving its state as a new image, and launching new containers from that image.

> **Note:** While `docker commit` is useful for learning and debugging, production environments should use **Dockerfiles** to build reproducible and version-controlled images.

---

## 🎯 Objective

Learn how to:

- Create a Docker container
- Modify the container by installing software
- Save the container as a new Docker image
- Launch a new container from the custom image

---

## 🛠️ Prerequisites

- Docker installed
- Basic understanding of Docker Images and Containers
- Internet connectivity (to pull the Ubuntu image)

---

# 🚀 Implementation

## Step 1: Pull the Ubuntu Image

Download the latest Ubuntu image from Docker Hub.

```bash
docker pull ubuntu
```

Verify the downloaded image:

```bash
docker images
```

Example Output:

```text
REPOSITORY   TAG      IMAGE ID
ubuntu       latest   xxxxxxxxx
```

---

## Step 2: Create a Container

Launch a new interactive Ubuntu container.

```bash
docker run -it --name ubuntu-container ubuntu
```

You will enter the container shell.

```text
root@xxxxxxxx:/#
```

---

## Step 3: Install Nginx

Update package information.

```bash
apt update
```

Install Nginx.

```bash
apt install -y nginx
```

Verify the installation.

```bash
nginx -v
```

Example Output:

```text
nginx version: nginx/1.24.x
```

---

## Step 4: Exit the Container

Exit the running container.

```bash
exit
```

---

## Step 5: Verify the Container

Display all containers, including stopped ones.

```bash
docker ps -a
```

Example Output:

```text
CONTAINER ID   IMAGE    NAMES
abcd1234       ubuntu   ubuntu-container
```

---

## Step 6: Create a New Docker Image

Save the current state of the container as a new image.

```bash
docker commit ubuntu-container ubuntu-nginx:v1
```

Syntax:

```bash
docker commit <container-name> <image-name>:<tag>
```

Example Output:

```text
sha256:xxxxxxxxxxxxxxxxxxxxxxxx
```

---

## Step 7: Verify the New Image

List all available Docker images.

```bash
docker images
```

Expected Output:

```text
REPOSITORY      TAG
ubuntu          latest
ubuntu-nginx    v1
```

---

## Step 8: Launch a Container from the New Image

Create a new container using the custom image.

```bash
docker run -it ubuntu-nginx:v1
```

Verify that Nginx is installed.

```bash
nginx -v
```

Output:

```text
nginx version: nginx/1.24.x
```

This confirms that the new image contains all changes made in the original container.

---

# 🔄 Workflow

```text
Ubuntu Image
      │
      ▼
Create Container
      │
      ▼
Install Nginx
      │
      ▼
docker commit
      │
      ▼
Custom Docker Image
      │
      ▼
Launch New Container
```

---

# 🌍 Real-World Use Case

Suppose a developer installs additional troubleshooting tools inside a running application container.

Examples:

- vim
- curl
- net-tools

Instead of reinstalling these tools every time, the developer creates a temporary image.

```bash
docker commit app-container app-debug:v1
```

This approach is useful for:

- Debugging
- Testing
- Temporary environments

> **Best Practice:** In production, implement these changes in a **Dockerfile** instead of using `docker commit`.

---

# 📊 Docker Commit vs Dockerfile

| Docker Commit | Dockerfile |
|--------------|------------|
| Creates an image from a running container | Builds an image from a set of instructions |
| Manual process | Automated and repeatable |
| Not version-controlled | Stored in Git |
| Useful for testing and debugging | Recommended for production |

---

# 💼 DevOps Perspective

Although `docker commit` helps capture the current state of a container, it is rarely used in production.

Production teams typically use **Dockerfiles** because they provide:

- Version control
- Reproducible builds
- Automated image creation
- Easier collaboration
- CI/CD integration

Example Dockerfile:

```dockerfile
FROM ubuntu:latest

RUN apt update && \
    apt install -y nginx

CMD ["nginx", "-g", "daemon off;"]
```

Build the image:

```bash
docker build -t ubuntu-nginx:v1 .
```

---

# 📌 Key Commands

Pull an image:

```bash
docker pull ubuntu
```

Create a container:

```bash
docker run -it --name ubuntu-container ubuntu
```

Commit the container:

```bash
docker commit ubuntu-container ubuntu-nginx:v1
```

List images:

```bash
docker images
```

Run the custom image:

```bash
docker run -it ubuntu-nginx:v1
```

---

# 🎯 Interview Questions

### Q1. What is `docker commit`?

**Answer:**

`docker commit` creates a new Docker image from the current state of an existing container.

---

### Q2. What is the syntax of `docker commit`?

**Answer:**

```bash
docker commit <container-name> <image-name>:<tag>
```

---

### Q3. Is `docker commit` recommended for production?

**Answer:**

No. It is mainly used for testing and debugging. Production environments should use Dockerfiles because they are reproducible, automated, and version-controlled.

---

### Q4. What is the difference between `docker build` and `docker commit`?

| docker build | docker commit |
|--------------|---------------|
| Builds an image using a Dockerfile | Creates an image from a running container |
| Reproducible | Manual snapshot |
| Production-ready | Suitable for testing and debugging |

---

### Q5. What happens to the original container after running `docker commit`?

**Answer:**

The original container remains unchanged. Docker creates a new image that captures the container's current state.

---

# 📌 Key Takeaways

- `docker commit` saves the current state of a container as a new image.
- It is useful for learning, testing, and debugging.
- The original container is not modified during the commit process.
- Dockerfiles are the preferred approach for production because they are reproducible and version-controlled.
- Understanding both methods helps build a solid foundation in Docker image creation.

---

# 🎉 Conclusion

In this exercise, I learned how to create a custom Docker image from a running container using the `docker commit` command. I installed software inside a container, captured its state as a new image, and verified that the changes persisted when launching a new container.

While `docker commit` is valuable for experimentation and troubleshooting, modern DevOps practices rely on **Dockerfiles** to build consistent, maintainable, and production-ready container images.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

Happy Learning! 🚀
