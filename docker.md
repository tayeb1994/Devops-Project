<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>NGINX Caching Server Project</title>
  <style>
    body {
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #0f172a;
      color: #e5e7eb;
      margin: 0;
      padding: 2rem;
      line-height: 1.6;
    }
    .container {
      max-width: 900px;
      margin: 0 auto;
      background: #020617;
      border-radius: 12px;
      padding: 2rem 2.5rem;
      box-shadow: 0 20px 40px rgba(15, 23, 42, 0.7);
      border: 1px solid #1f2937;
    }
    h1, h2, h3 {
      color: #f9fafb;
      margin-top: 1.5rem;
      margin-bottom: 0.75rem;
    }
    h1 {
      font-size: 2.4rem;
      letter-spacing: 0.04em;
      text-transform: uppercase;
    }
    h2 {
      font-size: 1.6rem;
      border-bottom: 1px solid #1f2937;
      padding-bottom: 0.3rem;
    }
    p {
      margin: 0.4rem 0 0.8rem;
    }
    code {
      background: #111827;
      padding: 0.15rem 0.35rem;
      border-radius: 4px;
      font-size: 0.9rem;
      color: #e5e7eb;
    }
    pre {
      background: #020617;
      border-radius: 8px;
      padding: 1rem;
      overflow-x: auto;
      border: 1px solid #1f2937;
      font-size: 0.9rem;
    }
    .badge {
      display: inline-block;
      padding: 0.25rem 0.6rem;
      border-radius: 999px;
      font-size: 0.75rem;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      margin-right: 0.4rem;
      background: #1d4ed8;
      color: #e5e7eb;
    }
    .badge.secondary {
      background: #16a34a;
    }
    .section {
      margin-top: 1.5rem;
    }
    ul {
      margin: 0.4rem 0 0.8rem 1.2rem;
    }
    li {
      margin-bottom: 0.3rem;
    }
    .footer {
      margin-top: 2rem;
      font-size: 0.85rem;
      color: #9ca3af;
      border-top: 1px solid #1f2937;
      padding-top: 1rem;
    }
    a {
      color: #60a5fa;
      text-decoration: none;
    }
    a:hover {
      text-decoration: underline;
    }
    .highlight {
      color: #22c55e;
      font-weight: 600;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>NGINX Caching Server</h1>
    <div>
      <span class="badge">NGINX</span>
      <span class="badge secondary">Caching</span>
      <span class="badge">Reverse Proxy</span>
    </div>

    <div class="section">
      <h2>Overview</h2>
      <p>
        This project configures an <span class="highlight">NGINX-based caching server</span> that sits in front of your
        application or API as a reverse proxy. It improves performance, reduces backend load, and provides a clean,
        production-ready structure for serving cached content.
      </p>
      <p>
        Use this setup when you want fast responses for frequently requested resources, while still keeping full control
        over cache rules, invalidation, and logging.
      </p>
    </div>

    <div class="section">
      <h2>Features</h2>
      <ul>
        <li><strong>Reverse proxy:</strong> NGINX forwards requests to your upstream application server.</li>
        <li><strong>Static & dynamic caching:</strong> Cache HTML, JSON, images, and more with fine-grained rules.</li>
        <li><strong>Cache keys & TTLs:</strong> Control how long responses stay cached and how they are identified.</li>
        <li><strong>Custom headers:</strong> Add cache-related headers for debugging and observability.</li>
        <li><strong>Logging:</strong> Access logs and error logs for monitoring traffic and cache hits/misses.</li>
      </ul>
    </div>

    <div class="section">
      <h2>Architecture</h2>
      <p>
        The basic flow:
      </p>
      <ul>
        <li>Client sends a request to the NGINX server.</li>
        <li>NGINX checks its cache:
          <ul>
            <li>If there is a valid cached response → returns it immediately.</li>
            <li>If not cached or expired → forwards the request to the upstream backend.</li>
          </ul>
        </li>
        <li>Backend responds → NGINX stores the response in cache (if allowed) → returns it to the client.</li>
      </ul>
    </div>

    <div class="section">
      <h2>Example NGINX Cache Configuration</h2>
      <p>Below is a minimal example of an NGINX configuration with caching enabled:</p>
      <pre><code># /etc/nginx/conf.d/cache.conf

proxy_cache_path /var/cache/nginx levels=1:2 keys_zone=my_cache:10m
                 max_size=1g inactive=60m use_temp_path=off;

server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass         http://upstream_app;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;

        # Enable caching
        proxy_cache        my_cache;
        proxy_cache_valid  200 301 302 10m;
        proxy_cache_valid  404 1m;

        add_header X-Cache-Status $upstream_cache_status;
    }
}

upstream upstream_app {
    server 127.0.0.1:3000;
}
</code></pre>
    </div>

    <div class="section">
      <h2>Getting Started</h2>
      <ol>
        <li><strong>Install NGINX:</strong> Use your package manager (e.g., <code>apt</code>, <code>yum</code>).</li>
        <li><strong>Create cache config:</strong> Add a file like <code>cache.conf</code> in <code>/etc/nginx/conf.d/</code>.</li>
        <li><strong>Adjust upstream:</strong> Point <code>upstream_app</code> to your backend (Node, Python, PHP, etc.).</li>
        <li><strong>Test configuration:</strong> Run <code>nginx -t</code> to validate.</li>
        <li><strong>Reload NGINX:</strong> Apply changes with <code>systemctl reload nginx</code>.</li>
      </ol>
    </div>

    <div class="section">
      <h2>Project Structure</h2>
      <pre><code>.
├── nginx/
│   ├── cache.conf
│   └── nginx.conf
├── src/
│   └── app.js        # Example backend application
├── docker/
│   └── Dockerfile    # Optional Docker setup for NGINX + app
└── README.md         # Project documentation
</code></pre>
    </div>

    <div class="section">
      <h2>Usage Tips</h2>
      <ul>
        <li><strong>Debug cache:</strong> Check the <code>X-Cache-Status</code> header (HIT, MISS, BYPASS).</li>
        <li><strong>Fine-tune TTLs:</strong> Use <code>proxy_cache_valid</code> per status code or location.</li>
        <li><strong>Bypass cache:</strong> Add rules for authenticated routes or admin panels.</li>
        <li><strong>Monitor:</strong> Use access logs and tools like Grafana/Prometheus for traffic insights.</li>
      </ul>
    </div>

    <div class="footer">
      <p>
        Built for learning and production-ready experimentation with NGINX as a caching reverse proxy.
        Customize it to match your stack and performance goals.
      </p>
    </div>
  </div>
</body>
</html>

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

- version: "3.9"

- services:
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
