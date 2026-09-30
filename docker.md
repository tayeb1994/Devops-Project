
# Docker Documentation

## **1. What Docker is and why developers use it**
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

“Docker architecture uses a client–server model. The Docker Client sends commands to the Docker Daemon, which builds images, runs containers, manages resources, and interacts with registries. Images act as blueprints, containers are the running instances, and everything is optimized through a layered filesystem.”

**Core concepts:**

**Image:**

1. Read‑only template (snapshot of filesystem + metadata).

2. Built from a Dockerfile.

3. Stored in a registry (Docker Hub, GitHub Container Registry, AWS ECR). 

**Container:**

1. Running instance of an image.

2. Has a writable layer on top of image layers.

3. You can run many containers from the same image. 

**Layers:**

Each Dockerfile instruction = one layer.

Docker caches layers → rebuilds are faster. 

**Registry:**

Online storage for images (public or private).

Default: Docker Hub. 

**4. How to write a Dockerfile (beginner → advanced)**

#Base image
**FROM node:20-alpine**

#Set working directory
**WORKDIR /app**

#Copy dependency files
**COPY package*.json ./**

#Install dependencies
**RUN npm install**

#Copy source code
**COPY . .**

#Expose port
**EXPOSE 3000**

#Start app
**CMD ["node", "server.js"]**

**5. Multi‑stage Dockerfile (real project)**

Example: Node.js app, multi‑stage

**# Stage 1: Build**
FROM node:20-alpine AS build

- WORKDIR /app
- COPY package*.json ./
- RUN npm ci
- COPY . .
- RUN npm run build    **# e.g. builds /dist**

**# Stage 2: Production**
- FROM node:20-alpine AS prod

- WORKDIR /app
- COPY --from=build /app/dist ./dist
- COPY package*.json ./
- RUN npm ci --only=production

- ENV NODE_ENV=production
- EXPOSE 3000
- CMD ["node", "dist/server.js"]

**7. Docker Compose explained (with examples)**

Docker Compose lets you define multiple containers (services) in one YAML file and run them together.

- - version: "3.9"

- - services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DB_HOST=db
      - DB_USER=appuser
      - DB_PASS=secret
    depends_on:
      - db

  db:
    image: postgres:16-alpine
    environment:
      - POSTGRES_USER=appuser
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=appdb
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data
