# Day 03: Copy File to Running Docker Container

## 📌 Objective

Learn how to copy a file from the local machine into a running Docker container using the `docker cp` command and verify the copied file inside the container.

---

## 🛠️ Lab Environment

- **Operating System:** Ubuntu 24.04 LTS
- **Docker Engine:** Installed and Running
- **Container Name:** `nginx-server`
- **Image Used:** `nginx:latest`

---

## 📖 Scenario

In real-world DevOps environments, you may need to copy configuration files, HTML files, scripts, or certificates into a running container without rebuilding the Docker image.

The `docker cp` command allows you to transfer files between the host machine and a running container.

---

## 📝 Step 1: Create a Test File

Create a sample text file on the host machine.

```bash
echo "Hello Docker" > test.txt
```

### Verify the File

```bash
cat test.txt
```

### Expected Output

```text
Hello Docker
```

---

## 📂 Step 2: Copy the File into the Running Container

Use the `docker cp` command to copy the file from the host machine to the Nginx container.

```bash
docker cp test.txt nginx-server:/usr/share/nginx/html/
```

### Command Explanation

| Parameter | Description |
|-----------|-------------|
| `docker cp` | Copies files between the host and a container |
| `test.txt` | Source file on the host machine |
| `nginx-server` | Name of the running container |
| `/usr/share/nginx/html/` | Destination directory inside the container |

---

## 🔍 Step 3: Verify the File Inside the Container

Access the running container.

```bash
docker exec -it nginx-server /bin/sh
```

Navigate to the copied file.

```bash
cd /usr/share/nginx/html/
```

List the files.

```bash
ls
```

Display the file contents.

```bash
cat test.txt
```

Exit the container.

```bash
exit
```

### Expected Output

```text
Hello Docker
```

---

# 📊 Workflow

```text
Local Machine
      │
      │  docker cp test.txt
      ▼
Running Docker Container
      │
      ▼
/usr/share/nginx/html/
      │
      ▼
Verify using:
docker exec
```

---

# 💡 Interview Questions

### Q1. What is the purpose of the `docker cp` command?

It is used to copy files or directories between the host machine and a Docker container.

---

### Q2. Can `docker cp` copy files from a container to the host?

Yes.

Example:

```bash
docker cp nginx-server:/usr/share/nginx/html/index.html .
```

---

### Q3. Does the container need to be running?

It is recommended to use `docker cp` with a running container, although it can also work with stopped containers in many cases.

---

### Q4. What are some real-world use cases of `docker cp`?

- Copy configuration files
- Upload SSL certificates
- Transfer application logs
- Copy static web content
- Troubleshoot running containers

---

# ⚠️ Common Errors

## Error

```text
No such container
```

### Reason

The specified container name is incorrect or the container does not exist.

### Solution

Verify the container name.

```bash
docker ps
```

---

## Error

```text
No such file or directory
```

### Reason

The source file does not exist on the host machine.

### Solution

Verify the file.

```bash
ls
```

---

# 📚 Key Commands Used

```bash
echo "Hello Docker" > test.txt
cat test.txt
docker cp test.txt nginx-server:/usr/share/nginx/html/
docker exec -it nginx-server /bin/sh
cd /usr/share/nginx/html/
ls
cat test.txt
exit
```

---

# 🎯 Summary

In this hands-on lab, I learned how to:

- Create a file on the host machine
- Copy a file into a running Docker container
- Access the container using `docker exec`
- Verify the copied file inside the container
- Understand practical use cases of the `docker cp` command in real-world DevOps environments