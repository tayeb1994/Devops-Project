# Docker Documentation
**1. What Docker is and why developers use it**
Docker is a tool that lets you **package an app + all its dependencies into a container** so it runs the same everywhere—your laptop, a server, the cloud. 

**Why devs love it:**

1. No more “works on my machine”—environment is baked into the image. 

2. Fast setup—run a database, cache, or API with one command.

3. Portability—if a machine has Docker, your app can run there.

4. Standard in DevOps—Kubernetes, CI/CD, microservices… all sit on containers.

## **2. Containers vs virtual machines (teen‑friendly)**
Both containers and VMs isolate stuff—but in different ways.

**Virtual Machine (VM):**

1. Has a full OS inside (Windows, Ubuntu, etc.).

2. Heavy: GBs, slow to start (seconds–minutes).

3. Great when you need strong isolation or different OS. 

**Container:**

1. Shares the host OS kernel, isolates processes, filesystem, network.

2. Light: MBs, starts in milliseconds. 

3. Perfect for apps and microservices.

**Analogy:**

1. VM = your own house.

2. **Container = your own room in a big house—walls, door, privacy, but same building.**

## 3. Docker architecture: images, containers, layers, registries

Core concepts:

Image:

Read‑only template (snapshot of filesystem + metadata).

Built from a Dockerfile.

Stored in a registry (Docker Hub, GitHub Container Registry, AWS ECR). 

Container:

Running instance of an image.

Has a writable layer on top of image layers.

You can run many containers from the same image. 

Layers:

Each Dockerfile instruction = one layer.

Docker caches layers → rebuilds are faster. 

Registry:

Online storage for images (public or private).

Default: Docker Hub. 
