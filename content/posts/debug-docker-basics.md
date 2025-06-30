---
title: "Debugging Docker Images and Containers - A Practical Guide"
date: 2025-06-10
draft: false
summary: "Step-by-step practical guide for building, debugging, inspecting, and deploying Docker containers, including transfer to edge devices like Orange Pi."
tags: ["Docker", "DevOps", "Containers"]
categories: ["DevOps"]
---

This guide explains how to **build**, **debug**, **inspect**, and **deploy** Docker containers with a focus on real-world tasks like transferring images to edge devices (e.g., Orange Pi).



#### 🧱 1. Build a Docker Image from a Dockerfile

Use the following command to build a Docker image from a `Dockerfile`:

```bash
docker build -t my_image_name .
```

### Debugging During Build

* Add temporary steps inside your `Dockerfile`:

  ```dockerfile
  RUN ls -R /app
  RUN cat /app/somefile.txt
  ```

* Use verbose output for troubleshooting:

  ```bash
  docker build --progress=plain -t my_image_name .
  ```

---

#### 🔍 2. Run and Inspect a Container

### Start a Container with a Shell

```bash
docker run -it --entrypoint /bin/bash my_image_name
```

* `-it`: Interactive terminal
* `--entrypoint`: Override default CMD to get shell access

### Inspect Internals

Once inside the container:

```bash
ls /app
cat /etc/os-release
which python3
```

---

#### 🧰 3. Useful Debugging Commands

### Run in Detached Mode and Connect

```bash
docker run -dit --name my_container my_image_name
docker exec -it my_container /bin/bash
```

### Logs and Metadata

```bash
docker logs my_container
docker inspect my_container
```

---

#### 📂 4. Move Files Between Host and Container

### Copy from Host to Container

```bash
docker cp ./host_file.txt my_container:/app/host_file.txt
```

### Copy from Container to Host

```bash
docker cp my_container:/app/output.txt ./output.txt
```

---

#### 🧠 5. Explore Docker Image Layers

Use [`dive`](https://github.com/wagoodman/dive) to inspect image layers and filesystem changes.

Install:

### Ubuntu
```
sudo apt install dive
```

Use:

```bash
dive my_image_name
```

---

#### 🚀 6. Deploy Docker Image to Orange Pi

### Option 1: Transfer via SCP

1. Save image as tarball:

   ```bash
   docker save my_image_name > my_image.tar
   ```

2. Copy to Orange Pi:

   ```bash
   scp my_image.tar orangepi@<ip_address>:~/
   ```

3. SSH and Load:

   ```bash
   ssh orangepi@<ip_address>
   docker load < my_image.tar
   docker run -it my_image_name
   ```

### Option 2: Docker Hub or GitHub Container Registry

1. Tag and push:

   ```bash
   docker tag my_image_name username/my_image_name:latest
   docker push username/my_image_name:latest
   ```

2. On Orange Pi:

   ```bash
   docker pull username/my_image_name:latest
   docker run -it username/my_image_name:latest
   ```

---

#### 🧪 Minimal Debuggable Dockerfile Example

```dockerfile
FROM python:3.10-slim

WORKDIR /app
COPY . /app

RUN pip install -r requirements.txt

ENV PYTHONPATH=/app
CMD ["bash"]
```



For automation, container registry integration, or CI/CD workflows using GitHub Actions, see the next part of this blog series.

