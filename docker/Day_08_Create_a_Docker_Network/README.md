# Day 08: Create a Docker Network

## Solution

---

## Objective

Learn how to create a custom Docker network and enable communication between multiple Docker containers.

---

## Scenario

In real DevOps projects, applications are often split into multiple containers.

For example:

- Frontend Container
- Backend Container
- Database Container

These containers need to communicate with each other.

Docker Networks provide secure communication between containers without exposing containers directly to the host.

---

## Prerequisites

- Docker Installed
- Basic knowledge of Docker Images and Containers
- Ubuntu image available locally

---

## Step 1: View Existing Docker Networks

Run:

```bash
docker network ls
```

### Expected Output

```text
NETWORK ID     NAME      DRIVER    SCOPE
xxxxxxxx      bridge    bridge    local
xxxxxxxx      host      host      local
xxxxxxxx      none      null      local
```

### Explanation

- **bridge** → Default network for containers
- **host** → Shares the host's network
- **none** → No networking

---

## Step 2: Create a Custom Network

Run:

```bash
docker network create devops-network
```

Verify:

```bash
docker network ls
```

### Expected Output

```text
NETWORK ID     NAME              DRIVER    SCOPE
xxxxxxxx      bridge            bridge    local
xxxxxxxx      host              host      local
xxxxxxxx      none              null      local
xxxxxxxx      devops-network    bridge    local
```

The custom Docker network `devops-network` has now been created.

---

## Step 3: Inspect the Network

Run:

```bash
docker network inspect devops-network
```

### Expected Output

```json
[
  {
    "Name": "devops-network",
    "Driver": "bridge",
    "IPAM": {
      ...
    },
    "Containers": {}
  }
]
```

Notice:

```text
"Containers": {}
```

No containers are connected to the network yet.

---

## Step 4: Create the First Container

Run:

```bash
docker run -dit --name container1 --network devops-network ubuntu
```

Verify:

```bash
docker ps
```

The container should be running and connected to `devops-network`.

---

## Step 5: Create the Second Container

Run:

```bash
docker run -dit --name container2 --network devops-network ubuntu
```

Verify:

```bash
docker ps
```

### Expected Output

```text
container1
container2
```

Both containers are now connected to the same Docker network.

---

## Step 6: Verify Connected Containers

Run:

```bash
docker network inspect devops-network
```

Now you should see:

```text
Containers:

container1
container2
```

Both containers are attached to the custom network.

---

## Step 7: Test Container-to-Container Communication

Open a shell inside `container1`:

```bash
docker exec -it container1 bash
```

Inside the container, update the package repository:

```bash
apt update
```

Install the `ping` utility:

```bash
apt install -y iputils-ping
```

Now ping `container2` using its container name:

```bash
ping container2
```

### Expected Output

```text
PING container2 (172.xx.xx.xx)

64 bytes from container2...
64 bytes from container2...
```

This confirms that `container1` can communicate with `container2` using the container name.

Press:

```text
Ctrl + C
```

Exit the container:

```bash
exit
```

---

## Step 8: Disconnect a Container

Disconnect `container2` from the network:

```bash
docker network disconnect devops-network container2
```

Verify:

```bash
docker network inspect devops-network
```

Now only `container1` should be listed under the `Containers` section.

---

## Step 9: Connect the Container Again

Connect `container2` back to the network:

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

## Workflow

```text
                 Docker Network
                       |
                       v
               devops-network
                  /          \
                 /            \
                v              v
         container1       container2
                \              /
                 \            /
                  v          v
             Container Communication
                       |
                       v
              Communication by Name
```

---

# Types of Docker Networks

| Network Type | Description |
|--------------|-------------|
| **Bridge** | Default network for containers on the same host |
| **Host** | Container shares the host's network |
| **None** | No network connectivity |
| **Overlay** | Connects containers across multiple Docker hosts, commonly used with Docker Swarm |
| **Macvlan** | Assigns a MAC address so containers appear as physical network devices |

---

# Real Project Example

Suppose you deploy a web application consisting of:

- Frontend — React
- Backend — Spring Boot
- Database — MySQL

All these containers can communicate through:

```text
devops-network
```

### Architecture

```text
                 Frontend
                 (React)
                    |
                    v
                 Backend
               (Spring Boot)
                    |
                    v
                  MySQL
                (Database)
```

The Backend communicates with MySQL using the container name:

```text
mysql-db
```

instead of directly using the container IP address.

---

# Why Use Container Names Instead of IP Addresses?

Container IP addresses may change when a container is recreated.

For example:

```text
172.18.0.4
```

may change after the container is removed and recreated.

Instead, applications can communicate using the container name:

```text
mysql-db
```

Docker automatically resolves container names within the same user-defined network.

Therefore, instead of depending on:

```text
172.18.0.4
```

the application can use:

```text
mysql-db
```

This makes containerized applications more reliable and easier to manage.

---

# Docker Network Commands

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

# Expected Outcome

After completing this lab:

- Custom Docker network created successfully
- Two containers attached to the network
- Containers communicate using container names
- Container-to-container communication verified
- Container successfully disconnected from the network
- Container successfully connected back to the network
- Docker Network commands practiced successfully

---

# Interview Questions

## Q1. What is a Docker Network?

### Answer

A Docker Network allows Docker containers to communicate with each other and with external systems in an isolated and secure manner.

---

## Q2. Why do we create a custom Docker network?

### Answer

A custom Docker network enables secure communication between related containers and provides automatic DNS resolution using container names.

---

## Q3. What is the default network driver in Docker?

### Answer

The default network driver is **Bridge**.

---

## Q4. How do containers communicate within the same Docker network?

### Answer

Containers communicate using their **container names**, as Docker provides built-in DNS resolution within a user-defined network.

Example:

```bash
ping container2
```

---

## Q5. Which command displays all Docker networks?

### Answer

```bash
docker network ls
```

---

## Q6. How do you inspect a Docker network?

### Answer

```bash
docker network inspect <network-name>
```

Example:

```bash
docker network inspect devops-network
```

---

## Q7. How do you connect an existing container to a Docker network?

### Answer

```bash
docker network connect <network-name> <container-name>
```

Example:

```bash
docker network connect devops-network container2
```

---

## Q8. How do you disconnect a container from a Docker network?

### Answer

```bash
docker network disconnect <network-name> <container-name>
```

Example:

```bash
docker network disconnect devops-network container2
```

---

# Key Takeaways

```text
Docker Network
      |
      v
User-Defined Network
      |
      +-------------------+
      |                   |
      v                   v
 container1          container2
      |                   |
      +---------+---------+
                |
                v
       Container Communication
                |
                v
       Docker DNS Resolution
                |
                v
       Communication by Name
```

The key concept from this lab is:

> **Containers connected to the same user-defined Docker network can communicate with each other using container names instead of relying on container IP addresses.**

---

# Conclusion

In this hands-on lab, we learned how to:

1. View existing Docker networks
2. Create a custom Docker network
3. Inspect a Docker network
4. Create containers and attach them to a custom network
5. Test container-to-container communication
6. Use container names for communication
7. Disconnect a container from a network
8. Connect a container back to the network
9. Practice commonly used Docker Network commands

Docker networking is an important foundation for running multi-container applications and understanding more advanced technologies such as **Docker Compose, Kubernetes, microservices, and cloud-native architectures**.

---

**Day 08 Completed — Docker Network 🚀**
