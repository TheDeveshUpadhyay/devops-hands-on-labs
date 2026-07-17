# Day 04: Pull Docker Image

## Objective

Learn how to search, pull, inspect, verify, and remove Docker images using Docker CLI.

By the end of this lab, you'll understand:

- How to search Docker Hub for images
- How to pull the latest image
- How to pull a specific image version
- How to inspect image details
- How to view image history
- How to remove images
- Difference between `docker image prune` and `docker image prune -a`

---

## Lab Environment

- **OS:** Ubuntu 24.04 LTS
- **Docker:** Installed and Running
- **Platform:** AWS EC2

---

## Step 1: Search Ubuntu Image

```bash
docker search ubuntu
```

---

## Step 2: Pull Ubuntu Image

```bash
docker pull ubuntu
```

### Expected Output

```text
Using default tag: latest
latest: Pulling from library/ubuntu
...
Status: Downloaded newer image for ubuntu:latest
```

---

## Step 3: Verify

```bash
docker images
```

---

## Step 4: Pull a Specific Version

```bash
docker pull ubuntu:22.04
```

---

## Step 5: Verify Again

```bash
docker images
```

---

## Step 6: Inspect an Image

```bash
docker image inspect ubuntu
```

This command displays:

- Image ID
- Architecture
- Operating System
- Environment Variables
- Layers
- Creation Time

---

## Step 7: View Image History

```bash
docker history ubuntu
```

This command displays all image layers.

---

## Step 8: Remove an Image

```bash
docker rmi ubuntu
```

If the image is being used by a container, Docker returns an error.

---

# Remove Unused Images

## Remove Dangling Images

```bash
docker image prune
```

## Remove All Unused Images

```bash
docker image prune -a
```

---

# Difference Between

| Command | Removes |
|----------|---------|
| `docker image prune` | Dangling images only |
| `docker image prune -a` | All unused images |

---

## What are Dangling Images?

Example:

```text
REPOSITORY     TAG         IMAGE ID
nginx          latest      a12345
ubuntu         latest      b67890
<none>         <none>      c98765
```

Images having `<none>` as Repository or Tag are called **Dangling Images**.

They are created when:

- Rebuilding an image
- Re-tagging an image
- Interrupted image builds

They consume disk space but are no longer useful.

---

## Memory Trick

✅ **docker image prune**

**P = Partial Cleanup**

Only removes **Dangling Images**.

---

✅ **docker image prune -a**

**A = All Unused Images**

Removes every image that is not referenced by any container.

---

## Summary

In this lab, we learned how to:

- Search Docker images
- Pull latest and specific versions
- Inspect image metadata
- View image history
- Remove images
- Clean up unused Docker images
- Understand the difference between `docker image prune` and `docker image prune -a`
