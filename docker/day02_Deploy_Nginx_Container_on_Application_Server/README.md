
# Day 02: Deploy Nginx Container on Application Server

## Objective
Deploy a Nginx container using Docker and access the default Nginx web page from a browser.

By the end of this lab, you'll understand:
- How to pull an image from Docker Hub
- How to create and run a container
- How port mapping works
- How to verify a running container
- How to stop and remove a container

## Lab Environment
- **OS:** Ubuntu 24.04 LTS
- **Docker:** Installed and running
- **Platform:** AWS EC2 (recommended)

## Architecture
```
Internet
    ↓
AWS Security Group (Allow Port 80)
    ↓
EC2 Instance
    ↓
Docker Engine
    ↓
Nginx Container (Port 80)
```

## Step 1: Check Existing Images
```bash
docker images
```
**Question:** Is the nginx image already present?

---

## Step 2: Pull the Nginx Image
```bash
docker pull nginx
docker images
```
You should now see an nginx image in the list.

---

## Step 3: Run the Nginx Container
```bash
docker run -d --name nginx-server -p 80:80 nginx
```

| Option | Meaning |
|--------|---------|
| `docker run` | Create and start a container |
| `-d` | Run in detached (background) mode |
| `--name nginx-server` | Give the container a friendly name |
| `-p 80:80` | Map host port 80 to container port 80 |
| `nginx` | Image name |

---

## Step 4: Verify the Container
```bash
docker ps
```

---

## Step 5: Test from the EC2 Instance
```bash
curl localhost
```
**Expected output:**
```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
```

---

## Step 6: Test from Your Browser
Navigate to:
```
http://<EC2-Public-IP>
```
**Expected:** Welcome to nginx! page

---

## Step 7: View Logs
```bash
docker logs nginx-server
```

---

## Step 8: Remove the Container
```bash
docker stop nginx-server
docker rm nginx-server
```

---

## Understanding Port Mapping

### Port Mapping Syntax
```
-p <Host Port>:<Container Port>
```

**Example:** `-p 80:80`
```
Host Port (80) → Container Port (80)
```

### Why is Port Mapping Needed?
- Docker containers have their own network namespace
- Port 80 inside a container is isolated from the host
- Port mapping creates a bridge between host and container
- Without `-p`, the container runs but isn't accessible from outside

### Another Example
```bash
docker run -d -p 8080:80 nginx
```
```
Host Port (8080) → Container Port (80)
```
Access via: `http://<EC2-Public-IP>:8080`

---

## Troubleshooting: Port Already in Use

### Issue
If another application (Apache, another container, etc.) uses host port 80:

```bash
docker run -d --name nginx-server -p 80:80 nginx
```

**Error:**
```
docker: Error response from daemon: Bind for 0.0.0.0:80 failed: port is already allocated
```

### Why?
A host port can only be bound by one process at a time.

### Solutions

#### Option 1: Use a Different Host Port (Recommended)
```bash
docker run -d -p 8080:80 nginx
```
Access via: `http://<EC2-Public-IP>:8080`

#### Option 2: Stop the Existing Application
**For Apache:**
```bash
sudo systemctl stop apache2
```

**For another Docker container:**
```bash
docker ps
docker stop <container_id>
```

Then run:
```bash
docker run -d -p 80:80 nginx
```

#### Option 3: Find What's Using Port 80
```bash
sudo ss -tulpn | grep :80
```
