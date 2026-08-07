# 🚀 Day 07: Write a Dockerfile

Learn how to create a Docker image using a **Dockerfile**, the standard and recommended approach for building container images in modern DevOps workflows.

---

# 📖 Objective

Understand how to write a **Dockerfile** to automate Docker image creation instead of manually modifying containers and using `docker commit`.

By the end of this lab, you will be able to:

- Create a Dockerfile
- Build a custom Docker image
- Run a container from the image
- Verify installed software
- Understand common Dockerfile instructions

---

# 🌍 Scenario

In real-world DevOps projects, Docker images are **not created manually**. Instead, developers define image-building instructions in a **Dockerfile**, allowing anyone to recreate the same image consistently.

Using Dockerfiles provides:

- ✅ Consistent image builds
- ✅ Reproducible environments
- ✅ Version-controlled infrastructure
- ✅ Easy maintenance and collaboration

---

# 🛠️ Prerequisites

- Docker installed
- Basic understanding of Docker Images and Containers
- Ubuntu image available locally (or Docker can pull it automatically)

---

# 📁 Step 1: Create a Project Directory

Create a new directory:

```bash
mkdir dockerfile-demo
```

Navigate into it:

```bash
cd dockerfile-demo
```

Verify your current directory:

```bash
pwd
```

---

# 📄 Step 2: Create a Dockerfile

Create a file named **Dockerfile** (without any extension):

```bash
touch Dockerfile
```

Verify:

```bash
ls
```

Expected Output:

```text
Dockerfile
```

---

# ✍️ Step 3: Write the Dockerfile

Open the Dockerfile:

```bash
vi Dockerfile
```

Add the following content:

```dockerfile
FROM ubuntu:latest

RUN apt update && \
    apt install -y nginx

CMD ["bash"]
```

Save and exit the editor.

### Explanation

| Instruction | Purpose |
|------------|---------|
| `FROM` | Specifies the base image |
| `RUN` | Executes commands while building the image |
| `CMD` | Defines the default command executed when the container starts |

---

# 🏗️ Step 4: Build the Docker Image

Run:

```bash
docker build -t ubuntu-nginx:v1 .
```

### Syntax

```bash
docker build -t <image-name>:<tag> .
```

### Explanation

- `-t` → Assigns a name and tag to the image
- `ubuntu-nginx` → Image name
- `v1` → Image version (tag)
- `.` → Current directory containing the Dockerfile

---

# 🔍 Step 5: Verify the Image

Run:

```bash
docker images
```

Expected Output:

```text
REPOSITORY        TAG
ubuntu-nginx      v1
ubuntu            latest
```

---

# ▶️ Step 6: Create a Container

Run:

```bash
docker run -it --name nginx-container ubuntu-nginx:v1
```

This starts a container using the custom Docker image.

---

# ✅ Step 7: Verify Nginx Installation

Inside the container, execute:

```bash
nginx -v
```

Expected Output:

```text
nginx version: nginx/1.24.x
```

Exit the container:

```bash
exit
```

---

# 🔄 Workflow

```text
Dockerfile
      │
      ▼
docker build
      │
      ▼
Docker Image
      │
      ▼
docker run
      │
      ▼
Container
```

---

# 📚 Common Dockerfile Instructions

| Instruction | Purpose |
|-------------|---------|
| `FROM` | Specifies the base image |
| `RUN` | Executes commands during image build |
| `CMD` | Defines the default command when a container starts |
| `COPY` | Copies files from the host into the image |
| `ADD` | Copies files or downloads remote URLs |
| `WORKDIR` | Sets the working directory inside the container |
| `ENV` | Defines environment variables |
| `EXPOSE` | Documents the network port used by the application |
| `ENTRYPOINT` | Specifies the main executable for the container |

---

# 🌍 Real Project Example

Suppose your team develops a **Spring Boot** application.

Instead of manually installing Java and copying application files every time, you define everything in a Dockerfile.

```dockerfile
FROM openjdk:21-jdk

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

Build the image:

```bash
docker build -t springboot-app:v1 .
```

Run the container:

```bash
docker run -d -p 8080:8080 springboot-app:v1
```

This ensures every developer and deployment environment uses the same application image.

---

# ⚖️ Dockerfile vs Docker Commit

| Dockerfile | Docker Commit |
|------------|---------------|
| Builds images from code | Creates images from running containers |
| Reproducible | Manual snapshot |
| Version-controlled | Not version-controlled |
| Easy to maintain | Difficult to reproduce |
| Preferred for production | Mainly used for testing and debugging |

---

# 🎯 Expected Outcome

- ✅ Dockerfile created successfully
- ✅ Docker image built using `docker build`
- ✅ Container created from the custom image
- ✅ Nginx installed automatically during image build
- ✅ No manual installation inside the container

---

# 💼 Interview Questions

## Q1. What is a Dockerfile?

**Answer:**

A Dockerfile is a text file containing a sequence of instructions used to automatically build Docker images.

---

## Q2. Why is Dockerfile preferred over `docker commit`?

**Answer:**

Dockerfiles are reproducible, version-controlled, easy to maintain, and can be stored in Git, making them the preferred approach for production environments.

---

## Q3. What is the purpose of the `FROM` instruction?

**Answer:**

`FROM` specifies the base image on which the new Docker image is built.

Example:

```dockerfile
FROM ubuntu:latest
```

---

## Q4. What does the `RUN` instruction do?

**Answer:**

`RUN` executes commands during the image build process.

Example:

```dockerfile
RUN apt update && apt install -y nginx
```

---

## Q5. What is the difference between `RUN` and `CMD`?

| RUN | CMD |
|------|-----|
| Executes during image build | Executes when the container starts |
| Creates image layers | Defines the default container command |

---

## Q6. What does the `docker build` command do?

**Answer:**

`docker build` reads the Dockerfile, executes each instruction, and creates a Docker image.

Example:

```bash
docker build -t ubuntu-nginx:v1 .
```

---

# 📌 Key Takeaways

- Dockerfiles automate image creation.
- Images become reproducible and version-controlled.
- `docker build` creates images from Dockerfiles.
- `RUN` executes commands during image creation.
- `CMD` specifies the default command when the container starts.
- Dockerfiles are the industry-standard approach for building production-ready Docker images.

---

# 🎉 Conclusion

Today, I learned how to create a **Docker image using a Dockerfile**, replacing manual image creation with an automated, repeatable, and version-controlled process.

Writing Dockerfiles is a fundamental DevOps skill that enables consistent deployments, simplifies collaboration, and forms the foundation of modern containerized application delivery.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

**Happy Learning! 🚀**
