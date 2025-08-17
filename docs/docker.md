---
title: Docker
layout: default
has_toc: true            # 保险起见，也在页面里显式打开
toc_levels: [2,3,4,5]
---


# Docker

- TOC
{:toc}

### What is virtualization？
> chroot is an operation in Unix and Linux systems that changes the apparent root directory for a running process and its children. A process running in such an environment cannot access files outside the designated root directory.

**Virtualization technologies: VMware, VirtualBox, KVM**

Virtualization adds a **hypervisor layer** on top of the operating system, which virtualizes resources such as CPU, memory, disk, and network. Operating systems can then be installed on top of these virtualized resources, creating a virtual execution environment—i.e., Virtual Machines (VMs).

**Advantages of VMs**
- Improve IT efficiency and reduce maintenance costs
- Faster workload deployment
- Higher server availability

**Disadvantages of VMs**
- Higher resource overhead, weaker performance
- Poor scalability and migration capability

---

### Why Docker

**Scenarios**
- Developers code locally and share their work via Docker containers.
- Push applications to a test environment with Docker and run automated/manual tests.
- Fix issues in dev, redeploy to test, and verify quickly.
- After testing, promoting to production is as simple as pushing an updated image.

**Needs**
> Deliver applications quickly and consistently, package environments into images to avoid configuration drift, simplify the development lifecycle, and support fast, agile iteration.

---

### Container vs Virtual Machine

| Feature         | Container                   | Virtual Machine       |
|-----------------|-----------------------------|-----------------------|
| Startup speed   | Seconds                     | Minutes               |
| Performance     | Near-native                 | Weaker                |
| Memory overhead | Very small                  | Large                 |
| Disk usage      | Usually MB                  | Usually GB            |
| Runtime density | Thousands per host          | Dozens per host       |
| Isolation       | Process-level isolation     | Full OS isolation     |
| Portability     | Excellent                   | Average               |

---

### Core Concepts

**Docker Engine (Daemon)**  
Docker uses a client/server architecture. The **Docker client** communicates with the **Docker daemon (engine)** via a Unix socket or REST API. The engine performs the heavy lifting of building, running, and distributing containers.

**Docker Image & Dockerfile**  
A Docker image is a read-only template used to create containers, built layer-by-layer from instructions (e.g., copy files, run commands).

**Docker Registry (Hub)**  
Registries can be **public** or **private**. The largest public registry is **Docker Hub**, which hosts a vast number of images.

---

### Basic Operations

**Images**
```dockerfile
# Pull an image
docker pull mysql:5.7.30

# List images
docker images

# Tag an image
docker tag mysql:5.7.30 mysql5

# Inspect details
docker inspect mysql:5.7.30

# Search images
docker search mysql

# Remove an image
docker rmi mysql:5.7.30

# Push an image
docker push mysql[:TAG]
```

**Containers**
```dockerfile
# Create a container
docker create -it nginx

# Start a container
docker start 9cfcce23593a

# Show running containers
docker ps

# Show all containers
docker ps -a

# Run and remove after exit
docker run -it --rm --network host tomcat:8.5.56-jdk8-openjdk
```

---

### Practical Example

1. **Create a volume**
   ```dockerfile
   docker volume create test_volume
   ```

2. **Mount the same volume in two containers**
   ```dockerfile
   docker run -it --rm -v test_volume:/test nginx:latest /bin/bash
   docker run -it --rm -v test_volume:/test nginx:latest /bin/bash

   cd /test
   touch a.txt
   ls /test   # The file is visible from both containers
   ```

> **Notes**
> - Docker **does not support relative paths** for mount points inside containers.
> - Multiple containers can use the same named volume concurrently (e.g., one writes while another reads).
> - If a named volume already exists, Docker will reuse it automatically; otherwise it creates it on first use.
> - You can also bind-mount a host directory into a container; if the host path doesn’t exist, Docker creates it at container start.  