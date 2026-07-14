# Docker

## Labs
- Day 01: Install Docker Packages and Start Docker Service
- Day 02: Deploy Nginx Container on Application Server
- Day 03: Copy File to Docker Container
- Day 04: Pull Docker Image
- Day 05: Create a Docker Image From Container
- Day 06: Docker EXEC Operations
- Day 07: Write a Docker File
- Day 08: Create a Docker Network
- Day 09: Docker Ports Mapping
- Day 10: Write a Docker Compose File
- Day 11: Resolve Dockerfile Issues
- Day 12: Deploy an App on Docker Containers
- Day 13: Docker Python App

---

## Solutions

### Day 01: Install Docker Packages and Start Docker Service

#### Lab Environment
Use an Ubuntu EC2 instance (recommended) or an Ubuntu VM.

Verify OS:
```bash
cat /etc/os-release
```
Expected output:
```
Ubuntu 24.04 LTS
```

#### Steps

1. Update the package list
```bash
sudo apt update
```

2. Install Docker
```bash
sudo apt install docker.io -y
```

3. Check whether Docker is installed
```bash
docker --version
```
Example:
```
Docker version 29.6.1
```

4. Start the Docker service
```bash
sudo systemctl start docker
```

5. Check Docker service status
```bash
sudo systemctl status docker
```

6. Enable Docker at boot
```bash
sudo systemctl enable docker
```

Verify:
```bash
sudo systemctl is-enabled docker
```
Expected output:
```
enabled
```

Why?
- Enabling ensures Docker starts automatically after a reboot.

---

## Challenge Questions

Q1: What is Docker Engine?  
Ans: Docker Engine is the core software that allows you to build, run, and manage Docker containers. It provides the runtime environment required to:
- Build Docker images
- Run containers
- Stop containers
- Pull images from Docker Hub
- Manage networks and volumes

Q2: What is the Docker daemon (dockerd)?  
Ans: The Docker daemon (dockerd) is a background service that runs on the host and performs Docker operations. It receives requests from the Docker CLI and is responsible for:
- Building Docker images
- Pulling images from registries
- Creating, starting, stopping, and removing containers
- Managing Docker networks and volumes
- Monitoring container status

Q3: Difference between Docker Engine and Docker daemon  
Ans:
- Docker Engine: the complete Docker platform/runtime (includes CLI, daemon, REST API). Responsible for the overall container management system; installed as the Docker package.
- Docker Daemon (dockerd): a single component of Docker Engine; a background service that executes Docker operations and runs continuously as a system service.
- Users interact with Docker Engine via the Docker CLI; the CLI sends requests to dockerd, which executes them.