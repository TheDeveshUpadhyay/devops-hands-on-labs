# 🌐 Day 08: Create a Docker Network

Learn how to create a **custom Docker network** and enable communication between multiple Docker containers.

---

# 📌 Objective

Learn how to create a custom Docker network and enable communication between multiple Docker containers.

By the end of this lab, you will understand:

- How to create a Docker network
- How to connect containers to a custom network
- How containers communicate with each other
- How Docker provides DNS resolution using container names
- How to disconnect and reconnect containers from a network

---

# 🌍 Scenario

In real DevOps projects, applications are often split into multiple containers.

For example:

```text
Frontend Container
        │
        ▼
Backend Container
        │
        ▼
Database Container
```

These containers need to communicate with each other.

Docker Networks provide an isolated networking environment that allows containers to communicate with each other.

---

# 🛠️ Prerequisites

- Docker Installed
- Basic understanding of Docker Images and Containers
- Ubuntu image available locally
- Basic Linux command knowledge

---

# 📁 Step 1: View Existing Docker Networks

Run:

```bash
docker network ls
```

Example output:

```text
NETWORK ID     NAME      DRIVER    SCOPE
xxxxxxxx       bridge    bridge    local
xxxxxxxx       host      host      local
xxxxxxxx       none      null      local
```

### Explanation

| Network | Driver | Purpose |
|---|---|---|
| `bridge` | bridge | Default network for containers |
| `host` | host | Container shares the host's network |
| `none` | null | Disables networking |

---

# 🌐 Step 2: Create a Custom Docker Network

Create a custom network named:

```text
devops-network
```

Run:

```bash
docker network create devops-network
```

Verify:

```bash
docker network ls
```

Expected output:

```text
NETWORK ID     NAME             DRIVER    SCOPE
xxxxxxxx       bridge           bridge    local
xxxxxxxx       host             host      local
xxxxxxxx       none             null      local
xxxxxxxx       devops-network   bridge    local
```

The custom network `devops-network` is created using the **bridge** network driver.

---

# 🔍 Step 3: Inspect the Network

Run:

```bash
docker network inspect devops-network
```

Expected output:

```json
[
    {
        "Name": "devops-network",
        "Driver": "bridge",
        "IPAM": {
            "Driver": "default"
        },
        "Containers": {}
    }
]
```

Notice:

```text
"Containers": {}
```

This means that no containers are connected to the network yet.

---

# 🐳 Step 4: Create the First Container

Create the first Ubuntu container and attach it to the custom network.

Run:

```bash
docker run -dit --name container1 --network devops-network ubuntu
```

Verify:

```bash
docker ps
```

Example output:

```text
CONTAINER ID   IMAGE     STATUS       NAMES
xxxxxxxx       ubuntu    Up 1 minute  container1
```

The container is now connected to:

```text
devops-network
```

---

# 🐳 Step 5: Create the Second Container

Create another Ubuntu container on the same network.

Run:

```bash
docker run -dit --name container2 --network devops-network ubuntu
```

Verify:

```bash
docker ps
```

Expected output:

```text
CONTAINER ID   IMAGE     STATUS        NAMES
xxxxxxxx       ubuntu    Up 2 minutes  container1
xxxxxxxx       ubuntu    Up 1 minute   container2
```

Both containers are now connected to the same custom Docker network.

---

# 🔍 Step 6: Verify Connected Containers

Run:

```bash
docker network inspect devops-network
```

Now you should see:

```text
container1
container2
```

Both containers are attached to:

```text
devops-network
```

The network now looks like:

```text
                 devops-network
                       │
              ┌────────┴────────┐
              ▼                 ▼
         container1        container2
```

---

# 🔄 Step 7: Test Container-to-Container Communication

Open a shell inside `container1`:

```bash
docker exec -it container1 bash
```

Inside the container, update the package repository:

```bash
apt update
```

Install the ping utility:

```bash
apt install -y iputils-ping
```

Now ping `container2` using its container name:

```bash
ping container2
```

Expected output:

```text
PING container2 (172.xx.xx.xx) 56(84) bytes of data.

64 bytes from container2 (172.xx.xx.xx):
64 bytes from container2 (172.xx.xx.xx):
64 bytes from container2 (172.xx.xx.xx):
```

Press:

```text
Ctrl + C
```

Exit the container:

```bash
exit
```

### What happened?

`container1` was able to communicate with `container2` using:

```text
container2
```

instead of directly using its IP address.

Docker provides **built-in DNS resolution** for containers connected to the same user-defined network.

---

# 🔌 Step 8: Disconnect a Container

Disconnect `container2` from the custom network.

Run:

```bash
docker network disconnect devops-network container2
```

Verify:

```bash
docker network inspect devops-network
```

Now only `container1` should be listed in the network configuration.

---

# 🔗 Step 9: Connect the Container Again

Connect `container2` back to the network.

Run:

```bash
docker network connect devops-network container2
```

Verify:

```bash
docker network inspect devops-network
```

Both containers should appear again:

```text
container1
container2
```

---

# 🔄 Workflow

```text
Docker Network
       │
       ▼
devops-network
       │
       ├──────────────┐
       ▼              ▼
container1       container2
       │              │
       └───────┬──────┘
               ▼
      Container Communication
               │
               ▼
       Container Name / DNS
```

---

# 🌍 Real Project Example

Suppose you deploy a web application consisting of three containers:

```text
Frontend (React)
Backend (Spring Boot)
MySQL Database
```

All containers can communicate through:

```text
devops-network
```

### Architecture

```text
Frontend
   │
   ▼
Backend
   │
   ▼
MySQL
```

The Backend communicates with MySQL using the container name:

```text
mysql-db
```

instead of using an IP address.

For example:

```text
DB_HOST=mysql-db
```

---

# 💡 Why Use Container Names Instead of IP Addresses?

Container IP addresses may change when a container is recreated.

For example:

```text
Old Container IP:
172.18.0.4
```

After the container is recreated:

```text
New Container IP:
172.18.0.7
```

If an application uses the IP address directly, the configuration may break.

Instead, use the container name:

```text
mysql-db
```

Docker automatically resolves the container name to its current IP address within the user-defined network.

Therefore:

```text
Application
     │
     ▼
 mysql-db
     │
     ▼
Docker DNS
     │
     ▼
Current Container IP
```

This makes container-to-container communication more reliable.

---

# 📚 Docker Network Commands

### Create Network

```bash
docker network create my-network
```

### List Networks

```bash
docker network ls
```

### Inspect Network

```bash
docker network inspect my-network
```

### Connect Container

```bash
docker network connect my-network container1
```

### Disconnect Container

```bash
docker network disconnect my-network container1
```

### Remove Network

```bash
docker network rm my-network
```

---

# ✅ Expected Outcome

- Custom Docker network created successfully.
- Two containers attached to the custom network.
- Containers communicate with each other.
- Container-to-container communication tested using container names.
- Docker DNS resolution understood.
- Container successfully disconnected from the network.
- Container successfully connected back to the network.

---

# 🎤 Interview Questions

## Q1. What is a Docker Network?

**Answer:**

A Docker Network allows Docker containers to communicate with each other and with external systems while providing network isolation.

---

## Q2. Why do we create a custom Docker network?

**Answer:**

A custom Docker network enables communication between related containers and provides automatic DNS resolution using container names.

---

## Q3. What is the default network driver in Docker?

**Answer:**

The default network driver is:

```text
bridge
```

---

## Q4. How do containers communicate within the same Docker network?

**Answer:**

Containers connected to the same user-defined network can communicate using their container names because Docker provides built-in DNS resolution.

For example:

```bash
ping container2
```

---

## Q5. Which command displays all Docker networks?

**Answer:**

```bash
docker network ls
```

---

## Q6. How do you inspect a Docker network?

**Answer:**

```bash
docker network inspect <network-name>
```

Example:

```bash
docker network inspect devops-network
```

---

## Q7. How do you create a Docker network?

**Answer:**

```bash
docker network create devops-network
```

---

## Q8. How do you connect an existing container to a Docker network?

**Answer:**

```bash
docker network connect devops-network container1
```

---

## Q9. How do you disconnect a container from a Docker network?

**Answer:**

```bash
docker network disconnect devops-network container1
```

---

# 🔑 Key Takeaways

- Docker Networks allow containers to communicate with each other.
- Custom networks provide isolated communication between related containers.
- Containers on the same user-defined network can communicate using container names.
- Docker provides built-in DNS resolution for containers on user-defined networks.
- Container IP addresses can change when containers are recreated.
- Using container names is more reliable than hard-coded IP addresses.
- `docker network inspect` is useful for troubleshooting Docker networking.
- Docker networking is an important foundation for Docker Compose and Kubernetes networking.

---

# 🎉 Conclusion

Today, I learned how to create a **custom Docker network** and connect multiple containers to it.

I also tested container-to-container communication using **container names** and understood how Docker's built-in DNS resolution works.

This hands-on lab strengthened my understanding of Docker networking, an essential concept for working with containerized applications in real-world DevOps environments.

---

## ⭐ Support

If you found this repository helpful, consider giving it a **⭐ Star** and follow my DevOps learning journey.

Happy Learning! 🚀
