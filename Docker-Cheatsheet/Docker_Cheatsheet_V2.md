# 🐳 DOCKER CHEATSHEET - Zero to Hero

**Perfect for Beginners to Advanced Users**  
**Version:** 2.0 | 

---

## 📚 TABLE OF CONTENTS

| # | Section |
|---|---------|
| 1 | Getting Started |
| 2 | Installation Guide |
| 3 | Docker Basics |
| 4 | Working with Images |
| 5 | Working with Containers |
| 6 | Dockerfile - Create Your Own Image |
| 7 | Docker Compose - Multiple Containers |
| 8 | Networks - Connect Containers |
| 9 | Volumes - Save Your Data |
| 10 | Docker Hub & Registries |
| 11 | Docker Desktop GUI |
| 12 | Production Tips & Tricks |
| 13 | Quick Commands Reference |
| 14 | Troubleshooting |

---

## 🚀 SECTION 1: GETTING STARTED

### What is Docker? (Easy Explanation)

Think of Docker like **shipping containers for software**:

```
┌─────────────────────────────────────────────────────────────┐
│                    WITHOUT DOCKER 😞                         │
├─────────────────────────────────────────────────────────────┤
│  My Computer          Your Computer        Server            │
│  ✓ Works              ✗ Broken             ✗ Broken           │
│  (Python 3.8)         (Python 3.6)         (Python 3.9)      │
│  ✓ Works              ✗ Missing libs       ✗ Different OS    │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│                    WITH DOCKER 😊                            │
├─────────────────────────────────────────────────────────────┤
│  📦 My App           📦 My App             📦 My App         │
│  ✓ Works             ✓ Works               ✓ Works           │
│  (All inside same     (All inside same     (All inside same   │
│   container)         container)           container)        │
└─────────────────────────────────────────────────────────────┘
```

### 🎯 Key Docker Concepts (Simple)

| Concept | What It Is | Real-World Example |
|---------|-----------|-------------------|
| 🖼️ **Image** | A blueprint/template | Recipe for a cake |
| 📦 **Container** | Running copy of image | The cake itself |
| 🔨 **Dockerfile** | Instructions to build image | Written recipe |
| 📚 **Registry** | Image storage (like library) | Recipe book library |
| 🔗 **Network** | How containers talk | Phone network |
| 💾 **Volume** | Storage that survives | Kitchen pantry |

### 🤔 Containers vs Virtual Machines (Why Docker?)

```
┌──────────────────────────────────────────────────────────────┐
│  VIRTUAL MACHINES (Heavy) ❌ vs DOCKER CONTAINERS (Light) ✅  │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│  VM Size:      4-8 GB          Container:     10-100 MB      │
│  Startup:      Minutes          Startup:      Seconds        │
│  Memory Use:   Very High        Memory Use:    Low           │
│  Speed:        Slower           Speed:         Super Fast     │
│  Isolation:    Hardware         Isolation:     Process        │
│                                                               │
└──────────────────────────────────────────────────────────────┘
```

---

## 💻 SECTION 2: INSTALLATION GUIDE

### 🍎 macOS Installation

**Option 1: Docker Desktop (Easiest) ⭐**
```bash
# Download from: https://www.docker.com/products/docker-desktop
# Double-click the installer
# Follow the prompts
# That's it! 🎉
```

**Option 2: Using Homebrew**
```bash
brew install docker
brew install docker-compose
```

**Verify Installation:**
```bash
docker --version        # Should show: Docker version 24.x.x
docker run hello-world  # Should print: Hello from Docker!
```

---

### 🪟 Windows Installation

**Option 1: Docker Desktop for Windows (Easiest) ⭐**
```
1️⃣  Download from: https://www.docker.com/products/docker-desktop
2️⃣  Install and enable WSL 2 (Windows Subsystem for Linux)
3️⃣  Restart your computer
4️⃣  Open Docker Desktop
5️⃣  Done! 🎉
```

**Option 2: Using Chocolatey**
```powershell
choco install docker-desktop
```

**Quick Test:**
```powershell
docker --version
docker run hello-world
```

---

### 🐧 Linux Installation (Ubuntu/Debian)

```bash
# 1️⃣ Update system
sudo apt-get update

# 2️⃣ Install Docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# 3️⃣ Allow Docker without sudo
sudo usermod -aG docker $USER
newgrp docker

# 4️⃣ Verify
docker --version
docker run hello-world
```

**📍 For CentOS/RHEL:**
```bash
sudo yum install docker-ce docker-ce-cli
sudo systemctl start docker
sudo systemctl enable docker  # Start on boot
```

---

## 📦 SECTION 3: DOCKER BASICS

### 🎮 Most Important Commands (Learn These First!)

```bash
# 📥 PULL - Download an image from Docker Hub
docker pull nginx        # Downloads official nginx image
docker pull ubuntu:22.04 # Specific version

# 🏃 RUN - Create and start a container
docker run nginx                          # Run nginx
docker run -d nginx                       # Run in background
docker run -p 8080:80 nginx              # Map ports
docker run -it ubuntu /bin/bash           # Interactive terminal

# 📋 PS - List containers
docker ps                # Show running containers only
docker ps -a             # Show all containers (running + stopped)

# 🛑 STOP - Stop a running container
docker stop <container-name-or-id>

# 🗑️ RM - Delete a stopped container
docker rm <container-name-or-id>

# 📖 LOGS - See what container is doing
docker logs <container-name-or-id>       # Show logs once
docker logs -f <container-name-or-id>    # Follow logs (live)

# 🏠 INFO - Get system information
docker version          # Docker version info
docker info             # System-wide information
docker stats            # Container resource usage (live)
```

### 🎯 The Docker Workflow (Visual)

```
┌─────────────────────────────────────────────────────────────────┐
│                      DOCKER WORKFLOW                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  Step 1️⃣ : PULL IMAGE                                            │
│  ┌──────────────────────────────┐                                │
│  │ docker pull nginx            │                                │
│  │ ✓ Downloads blueprint        │                                │
│  └──────────────────────────────┘                                │
│           ⬇️                                                     │
│  Step 2️⃣ : RUN CONTAINER (Create from image)                    │
│  ┌──────────────────────────────┐                                │
│  │ docker run -d nginx          │                                │
│  │ ✓ Creates running instance   │                                │
│  └──────────────────────────────┘                                │
│           ⬇️                                                     │
│  Step 3️⃣ : USE IT                                                │
│  ┌──────────────────────────────┐                                │
│  │ Your app is now running!     │                                │
│  │ Visit: http://localhost:80   │                                │
│  └──────────────────────────────┘                                │
│           ⬇️                                                     │
│  Step 4️⃣ : STOP CONTAINER                                        │
│  ┌──────────────────────────────┐                                │
│  │ docker stop <container>      │                                │
│  │ ✓ Gracefully shuts down      │                                │
│  └──────────────────────────────┘                                │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🖼️ SECTION 4: WORKING WITH IMAGES

### 🎨 What is an Image?

An image is like a **DVD movie**: You can copy it, share it, run it many times. Each time you run it, you get a fresh copy (container).

### 📥 Download & Manage Images

```bash
# PULL - Download image from Docker Hub
docker pull nginx                    # Latest version
docker pull nginx:1.25.3            # Specific version
docker pull ubuntu:22.04

# LIST - See images you have
docker images                    # All local images
docker images | grep nginx       # Filter search

# SEARCH - Find images online
docker search nginx              # Search Docker Hub
docker search nginx --limit 5    # Show only 5 results

# INSPECT - Get image details
docker inspect nginx             # Detailed image information
```

### 🏗️ Build Your Own Image

```bash
# Build an image from Dockerfile
docker build -t myapp:1.0 .      # -t = tag (name), . = current folder

# Build with build arguments
docker build -t myapp:2.0 --build-arg VERSION=2.0 .

# Build without cache (rebuild everything)
docker build --no-cache -t myapp:1.0 .
```

### 🏷️ Tag Images (Give Them Names/Versions)

```bash
# Create a new tag (alias) for an image
docker tag nginx:latest myapp:v1        # Copy as new name
docker tag myapp:v1 myapp:latest        # Version tagging

# Tag for Docker Hub upload
docker tag myapp:1.0 myusername/myapp:1.0
```

### 📤 Share Images (Push to Docker Hub)

```bash
# Login to Docker Hub
docker login
# Enter your username and password

# Push your image
docker push myusername/myapp:1.0

# Others can now download it
docker pull myusername/myapp:1.0
```

### 🗑️ Delete Images

```bash
docker rmi nginx              # Remove image by name
docker rmi -f nginx           # Force remove (even if used)
docker image prune            # Remove unused images
docker image prune -a         # Remove ALL unused images
```

### 📊 Image Quick Reference

```
┌──────────────────────────────────────────────────────────────┐
│              POPULAR DOCKER IMAGES (Pre-built)               │
├──────────────────────────────────────────────────────────────┤
│                                                               │
│  🌐 WEB SERVERS:                                              │
│     • nginx - Fast web server                                 │
│     • apache - Popular web server                             │
│     • httpd - HTTP server                                     │
│                                                               │
│  🐍 PROGRAMMING:                                              │
│     • python - Python runtime                                 │
│     • node - JavaScript/Node.js                               │
│     • golang - Go language                                    │
│     • java - Java runtime                                     │
│                                                               │
│  🗄️ DATABASES:                                               │
│     • postgres - PostgreSQL database                          │
│     • mysql - MySQL database                                  │
│     • mongodb - NoSQL database                                │
│     • redis - In-memory cache                                 │
│                                                               │
│  🔧 UTILITIES:                                                │
│     • ubuntu - Ubuntu Linux                                   │
│     • alpine - Tiny Linux (only 5MB!)                         │
│     • busybox - Minimal utilities                             │
│                                                               │
│  💾 FIND MORE: https://hub.docker.com                         │
│                                                               │
└──────────────────────────────────────────────────────────────┘
```

---

## 🏗️ SECTION 5: WORKING WITH CONTAINERS

### 🎮 Container Commands (Most Used)

```bash
# RUN - Start a new container
docker run nginx                    # Basic
docker run -d nginx                 # Detached (background)
docker run -p 8080:80 nginx        # Port mapping
docker run -it ubuntu bash          # Interactive terminal
docker run --name myapp nginx       # Give it a name
docker run -e VAR=value nginx       # Set environment variable
docker run -m 512m nginx            # Limit memory to 512MB

# PS - List containers
docker ps                           # Running containers
docker ps -a                        # All containers
docker ps -q                        # Only IDs

# START/STOP/RESTART - Control containers
docker start myapp                  # Start stopped container
docker stop myapp                   # Stop running container
docker restart myapp                # Restart container
docker pause myapp                  # Pause (pause processes)
docker unpause myapp                # Resume paused container

# RM - Delete containers
docker rm myapp                     # Remove stopped container
docker rm -f myapp                  # Force remove (even running)

# LOGS - See what's happening
docker logs myapp                   # Show all logs
docker logs -f myapp                # Follow logs (live view)
docker logs --tail 100 myapp        # Last 100 lines

# EXEC - Run command inside container
docker exec myapp whoami            # Run command once
docker exec -it myapp bash          # Open interactive terminal
docker exec -it myapp /bin/sh       # Open shell

# INSPECT - Get container details
docker inspect myapp                # Full configuration
docker port myapp                   # Show port mappings
docker stats myapp                  # Resource usage (live)
docker top myapp                    # Running processes

# ATTACH - Connect to running container
docker attach myapp                 # Attach to container
```

### 🌍 Port Mapping (Simple Explanation)

Port mapping connects your computer's port to container's port:

```
┌─────────────────────────────────────────────────────────┐
│  Your Computer          Docker Container                 │
│  ─────────────────      ─────────────────                 │
│  Port 8080 (yours)  ➜   Port 80 (container's)            │
│                                                           │
│  Visit: http://localhost:8080                            │
│  ✓ Your computer gets traffic on 8080                    │
│  ✓ Forwards to container on port 80                      │
│  ✓ Nginx listens on port 80 inside container             │
└─────────────────────────────────────────────────────────┘
```

**Examples:**
```bash
docker run -p 8080:80 nginx        # 8080 on you → 80 in container
docker run -p 3000:3000 myapp      # Same port on both
docker run -p 5432:5432 postgres   # Database access
docker run -p 9000:8080 -p 9001:8081 myapp  # Multiple ports
```

### 📦 Container Lifecycle (How It Works)

```
   CREATED
      │
      ▼
┌──────────┐
│ STOPPED  │◄─────┐
└──────────┘      │
      │           │
      │ docker    │ docker
      │ run       │ stop
      │           │
      ▼           │
┌──────────┐      │
│ RUNNING  ├──────┘
└──────────┘
      │
      │ docker
      │ remove
      ▼
  REMOVED
  (deleted)
```

### 🎯 Common Container Examples

```bash
# ✅ Run a Web Server
docker run -d -p 8080:80 --name webserver nginx

# ✅ Run a Database
docker run -d -e POSTGRES_PASSWORD=secret --name db postgres:15

# ✅ Interactive Linux
docker run -it ubuntu bash
# Inside container type commands: ls, pwd, etc.
# Type 'exit' to leave

# ✅ Python Script
docker run -it python:3.11 python
# Now in Python interactive shell

# ✅ Run with Volume (Save data)
docker run -d -v mydata:/data --name storage ubuntu
# Files saved in /data persist even after container stops
```

---

## 🔨 SECTION 6: DOCKERFILE - Create Your Own Image

### 📝 What is Dockerfile?

A **Dockerfile** is a recipe file that tells Docker how to build your image. It's like a cooking recipe!

### 🏗️ Simple Dockerfile Example

```dockerfile
# Start from a base image
FROM python:3.11-slim

# Set working directory (where we work inside container)
WORKDIR /app

# Copy files from your computer to container
COPY . /app

# Install dependencies
RUN pip install -r requirements.txt

# Tell Docker which port to expose
EXPOSE 8000

# Command to run when container starts
CMD ["python", "app.py"]
```

### 📚 Dockerfile Instructions Explained

| Instruction | What It Does | Example |
|-------------|-------------|---------|
| **FROM** | Base image (starting point) | `FROM python:3.11` |
| **WORKDIR** | Where to work in container | `WORKDIR /app` |
| **COPY** | Copy files from computer to container | `COPY . /app` |
| **RUN** | Execute command during build | `RUN pip install flask` |
| **ENV** | Set environment variable | `ENV DEBUG=true` |
| **EXPOSE** | Which port container listens on | `EXPOSE 8000` |
| **CMD** | Default command when container starts | `CMD ["python", "app.py"]` |
| **LABEL** | Add metadata/labels | `LABEL version="1.0"` |
| **USER** | Which user runs the command | `USER appuser` |

### ⭐ Best Dockerfile (WITH BEST PRACTICES)

```dockerfile
# ✅ GOOD: Use specific version, not latest
FROM python:3.11-slim

# ✅ GOOD: Add labels with image info
LABEL maintainer="your-email@example.com" \
      version="1.0" \
      description="My awesome app"

# ✅ GOOD: Set working directory
WORKDIR /app

# ✅ GOOD: Copy requirements first (better caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# ✅ GOOD: Then copy application code
COPY . .

# ✅ GOOD: Create non-root user for security
RUN useradd -m appuser
USER appuser

# Tell which port we use
EXPOSE 8000

# Command to start the app
CMD ["python", "app.py"]
```

### 🚫 BAD Dockerfile (What NOT to do)

```dockerfile
# ❌ BAD: Using latest (unpredictable)
FROM python:latest

# ❌ BAD: No labels
# ❌ BAD: Copy everything at once (breaks caching)
COPY . /app

# ❌ BAD: No cleanup (makes image bigger)
RUN apt-get update && apt-get install -y everything

# ❌ BAD: No user (runs as root = security risk)
# ❌ BAD: Running as root (default)

# ❌ BAD: No CMD specified
```

### 🔨 Build Your Image

```bash
# Build with name and version
docker build -t myapp:1.0 .

# Build from specific Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build without using cache (rebuild everything)
docker build --no-cache -t myapp:1.0 .

# Build with arguments
docker build --build-arg VERSION=1.0 -t myapp:1.0 .
```

### 📊 Build Process (Visual)

```
┌─────────────────────────────────────────────────────────┐
│  DOCKERFILE                        DOCKER IMAGE         │
├─────────────────────────────────────────────────────────┤
│                                                          │
│  FROM ubuntu:22.04                  Layer 1: Ubuntu     │
│           │                                   (100 MB)  │
│           ▼                                             │
│  RUN apt-get install curl      +    Layer 2: + curl    │
│           │                                   (50 MB)   │
│           ▼                                             │
│  COPY app.py /app              +    Layer 3: + app.py  │
│           │                                   (5 MB)    │
│           ▼                                             │
│  CMD ["python", "app.py"]            Final Image        │
│                                      (155 MB total)     │
│                                                          │
└─────────────────────────────────────────────────────────┘
```

---

## 📋 SECTION 7: DOCKER COMPOSE - Multiple Containers

### 🎯 What is Docker Compose?

Run **multiple containers together** using a single configuration file. Perfect for apps with database, cache, etc.

### 📝 Simple Docker Compose Example

```yaml
version: '3.8'

services:
  # Web application
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html

  # Database
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/postgresql/data

# Named volumes (persistent storage)
volumes:
  dbdata:
```

### 🎮 Docker Compose Commands

```bash
# START all services
docker-compose up           # Start (show logs)
docker-compose up -d        # Start in background

# STOP all services
docker-compose down         # Stop and remove containers

# VIEW services
docker-compose ps           # List running services
docker-compose logs         # Show all logs
docker-compose logs -f web  # Follow logs for 'web' service

# REBUILD images
docker-compose build        # Rebuild images if needed
docker-compose build --no-cache  # Force rebuild

# OTHER commands
docker-compose restart      # Restart all services
docker-compose pause        # Pause services
docker-compose unpause      # Resume services
```

### 📊 Real Example: Flask + PostgreSQL

```yaml
version: '3.8'

services:
  # Python Web App
  web:
    build: .                          # Build from Dockerfile
    container_name: flask_app
    ports:
      - "5000:5000"                   # Access on localhost:5000
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    depends_on:
      - db                            # Start db first
    volumes:
      - ./app:/app                    # Sync code changes

  # PostgreSQL Database
  db:
    image: postgres:15
    container_name: postgres_db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data  # Keep data
    ports:
      - "5432:5432"                   # Access database

volumes:
  postgres_data:                      # Named volume for database
```

### 🌍 Service Communication

When using Docker Compose, services can talk to each other by **service name**:

```
┌──────────────────────────────────────────────┐
│          SERVICE COMMUNICATION                │
├──────────────────────────────────────────────┤
│                                               │
│  From 'web' service:                          │
│  ✓ Connect to PostgreSQL using: db:5432      │
│    (not localhost, not IP address)            │
│                                               │
│  From 'db' service:                           │
│  ✓ Connect to web using: web:5000            │
│                                               │
│  Magic: Docker automatically creates          │
│         internal DNS for all services!        │
│                                               │
└──────────────────────────────────────────────┘
```

---

## 🌐 SECTION 8: NETWORKS - Connect Containers

### 🤝 How Containers Talk

By default, containers can talk to each other on the same **network**. Docker creates networks automatically!

### 🎮 Network Commands

```bash
# CREATE a network
docker network create myapp-net

# LIST networks
docker network ls

# INSPECT network (see connected containers)
docker network inspect myapp-net

# CONNECT container to network
docker network connect myapp-net mycontainer

# DISCONNECT container
docker network disconnect myapp-net mycontainer

# DELETE network
docker network rm myapp-net
```

### 💡 Network Example

```bash
# 1️⃣ Create network
docker network create myapp

# 2️⃣ Run database
docker run -d --name db \
  --network myapp \
  -e POSTGRES_PASSWORD=secret \
  postgres:15

# 3️⃣ Run web app
docker run -d --name webapp \
  --network myapp \
  -p 8000:8000 \
  myapp:latest

# 4️⃣ From webapp, connect to database:
# Inside app code: db:5432  (NOT localhost!)
# Docker automatically resolves 'db' to database IP
```

### 📊 Container Communication (Visual)

```
┌─────────────────────────────────────────────────┐
│           DOCKER NETWORK "myapp"                 │
├─────────────────────────────────────────────────┤
│                                                   │
│  ┌──────────────┐        ┌──────────────┐       │
│  │   Database   │        │   Web App    │       │
│  │   (db)       │◄──────►│   (webapp)   │       │
│  │  postgres:15 │        │   nodejs     │       │
│  └──────────────┘        └──────────────┘       │
│        ▲                         ▲               │
│        │ Can talk via:           │ Can talk via:│
│        │ database:5432           │ webapp:8000  │
│        └─────────────────────────┘              │
│                                                   │
│  📌 Same network = automatic DNS resolution!     │
│                                                   │
└─────────────────────────────────────────────────┘
```

---

## 💾 SECTION 9: VOLUMES - Save Your Data

### ⚠️ Important: Why Volumes Matter!

```
┌──────────────────────────────────────────────────────┐
│ ❌ WITHOUT VOLUMES:                                  │
│                                                       │
│  docker run postgres                                 │
│  [Create database with data]                         │
│  docker stop postgres                                │
│  docker rm postgres                                  │
│  ⚠️ ALL DATA LOST! 😢                                │
│                                                       │
├──────────────────────────────────────────────────────┤
│ ✅ WITH VOLUMES:                                     │
│                                                       │
│  docker run -v mydata:/var/lib/postgresql postgres  │
│  [Create database with data]                         │
│  docker stop postgres                                │
│  docker rm postgres                                  │
│  ✓ DATA STILL EXISTS! 😊                             │
│  docker run -v mydata:/var/lib/postgresql postgres  │
│  ✓ Data is restored!                                 │
│                                                       │
└──────────────────────────────────────────────────────┘
```

### 🎮 Volume Commands

```bash
# CREATE a volume
docker volume create mydata

# LIST volumes
docker volume ls

# INSPECT volume
docker volume inspect mydata

# DELETE volume
docker volume rm mydata

# DELETE unused volumes
docker volume prune
```

### 📌 Use Volumes with Containers

```bash
# Named volume (recommended)
docker run -d -v mydata:/data postgres:15
# -v mydata:/data = volume named 'mydata' mounted at /data

# Host folder binding (for development)
docker run -d -v $(pwd)/app:/app myapp:latest
# $(pwd)/app = folder on your computer
# /app = path inside container

# Read-only volume
docker run -d -v mydata:/data:ro postgres:15
# :ro = read-only (container can't modify)
```

### 📊 Volume Types

| Type | What | Use Case |
|------|------|----------|
| **Named** | Docker manages it | Production databases |
| **Bind Mount** | Your folder | Development, config files |
| **Tmpfs** | In memory (temp) | Cache, temporary files |

---

## 📤 SECTION 10: DOCKER HUB & REGISTRIES

### 🌍 What is Docker Hub?

**Docker Hub** = GitHub for container images. Download and upload your images!

### 🔑 Docker Hub Workflow

```
┌──────────────────────────────────────────────────┐
│          DOCKER HUB WORKFLOW                      │
├──────────────────────────────────────────────────┤
│                                                   │
│  Step 1️⃣ : SIGN UP                               │
│  📍 https://hub.docker.com                        │
│  Create free account                              │
│                                                   │
│  Step 2️⃣ : LOGIN                                 │
│  $ docker login                                   │
│  Enter username & password                        │
│                                                   │
│  Step 3️⃣ : TAG YOUR IMAGE                        │
│  $ docker tag myapp:1.0 username/myapp:1.0       │
│                                                   │
│  Step 4️⃣ : PUSH TO DOCKER HUB                    │
│  $ docker push username/myapp:1.0                 │
│                                                   │
│  Step 5️⃣ : OTHERS CAN USE IT!                    │
│  $ docker pull username/myapp:1.0                 │
│                                                   │
└──────────────────────────────────────────────────┘
```

### 💻 Commands

```bash
# LOGIN to Docker Hub
docker login
# Enter your username and password

# PUSH (upload) your image
docker push username/myapp:1.0

# PULL (download) an image
docker pull username/myapp:1.0

# TAG your image (prepare for upload)
docker tag myapp:1.0 username/myapp:1.0
# Format: docker tag local-name:tag username/repo:tag

# LOGOUT
docker logout
```

### ✅ Before Pushing to Docker Hub

```bash
# 1️⃣ Build your image
docker build -t myapp:1.0 .

# 2️⃣ Tag it with your Docker Hub username
docker tag myapp:1.0 myusername/myapp:1.0

# 3️⃣ Login to Docker Hub
docker login

# 4️⃣ Push it
docker push myusername/myapp:1.0

# 5️⃣ Verify (others can now pull it)
docker pull myusername/myapp:1.0
```

---

## 🖥️ SECTION 11: DOCKER DESKTOP GUI

### 🎨 Easy Visual Interface

Docker Desktop provides a **graphical interface** so you don't always need commands!

### 📌 Main Sections

```
┌─────────────────────────────────────────────────────┐
│         DOCKER DESKTOP DASHBOARD                    │
├─────────────────────────────────────────────────────┤
│                                                      │
│  LEFT SIDEBAR             MAIN CONTENT               │
│  ─────────────────        ─────────────────          │
│  📦 Containers    ────▶   Container Details          │
│     • View all           • Status                    │
│     • Start/Stop         • Port mappings             │
│     • Logs               • Environment vars         │
│                          • CPU/Memory usage         │
│  🖼️ Images        ────▶   Image Details              │
│     • Download          • Size                      │
│     • Build             • Layers                    │
│     • Delete            • Pull/Push options         │
│                                                      │
│  💾 Volumes       ────▶   Volume Details             │
│     • Create            • Size                      │
│     • Delete            • Used by containers       │
│                                                      │
│  🌐 Networks      ────▶   Network Details            │
│     • View all          • Connected containers     │
│                         • IP addresses             │
│                                                      │
└─────────────────────────────────────────────────────┘
```

### 🎯 Common Tasks in Docker Desktop GUI

```
✅ START CONTAINER
  1. Click Containers tab
  2. Click image name
  3. Click play button (▶️)

✅ STOP CONTAINER
  1. Click Containers tab
  2. Find running container
  3. Click stop button (⏹️)

✅ VIEW LOGS
  1. Click container name
  2. Scroll to "Logs" section
  3. See real-time output

✅ OPEN TERMINAL
  1. Click Containers
  2. Find container
  3. Click "Exec" button
  4. Opens terminal inside container

✅ DOWNLOAD IMAGE
  1. Click "Search" button
  2. Type image name (e.g., postgres)
  3. Click "Pull"

✅ VIEW STATISTICS
  1. Click Containers
  2. Click container name
  3. See CPU, Memory usage
```

---

## ⭐ SECTION 12: PRODUCTION TIPS & TRICKS

### 🔒 Security Best Practices

```bash
# ✅ DO: Use specific versions
docker run postgres:15.2  # Good!
docker run postgres:latest  # Avoid!

# ✅ DO: Run as non-root user
FROM python:3.11
RUN useradd -m appuser
USER appuser  # Not root!

# ✅ DO: Use small images
FROM python:3.11-alpine   # 40MB
FROM python:3.11          # 900MB

# ✅ DO: Set resource limits
docker run -m 512m --cpus 1 myapp

# ✅ DO: Use environment variables for secrets
docker run -e DB_PASSWORD=secret myapp

# ✅ DO: Keep image clean
RUN apt-get update && apt-get install -y \
    package1 \
    package2 \
    && rm -rf /var/lib/apt/lists/*  # Cleanup!
```

### 📊 Health Checks (Keep Containers Healthy)

```bash
# Health check in run command
docker run \
  --health-cmd='curl --fail http://localhost || exit 1' \
  --health-interval=10s \
  --health-timeout=5s \
  --health-retries=3 \
  nginx

# OR in Dockerfile
HEALTHCHECK --interval=10s --timeout=5s --retries=3 \
  CMD curl -f http://localhost || exit 1
```

### 🔄 Auto-Restart Containers

```bash
# Always restart
docker run --restart always myapp

# Restart on failure (max 5 times)
docker run --restart on-failure:5 myapp

# Don't restart
docker run --restart no myapp
```

### 💾 Backup Important Data

```bash
# Backup volume to tar file
docker run --rm \
  -v mydata:/data \
  -v $(pwd):/backup \
  ubuntu \
  tar czf /backup/mydata.tar.gz -C /data .

# Restore from backup
docker run --rm \
  -v mydata:/data \
  -v $(pwd):/backup \
  ubuntu \
  sh -c "cd /data && tar xzf /backup/mydata.tar.gz"
```

### 📈 Monitor Resources

```bash
# Real-time statistics
docker stats

# For specific container
docker stats myapp

# Check resource usage
docker container inspect myapp | grep -i memory
```

---

## ⚡ SECTION 13: QUICK COMMANDS REFERENCE

### 🎯 Most Used Commands (Copy & Paste)

```bash
# 🖼️ IMAGE COMMANDS
docker pull nginx                      # Download image
docker build -t myapp:1.0 .           # Build image
docker images                          # List all images
docker rmi nginx                       # Delete image
docker search nginx                    # Search images
docker push username/myapp:1.0         # Upload image

# 📦 CONTAINER COMMANDS
docker run -d nginx                    # Run container
docker run -it ubuntu bash             # Interactive shell
docker ps                              # List running
docker ps -a                           # List all
docker start myapp                     # Start container
docker stop myapp                      # Stop container
docker rm myapp                        # Delete container
docker logs myapp                      # See logs
docker exec -it myapp bash             # Open shell

# 💾 VOLUME COMMANDS
docker volume create mydata            # Create volume
docker volume ls                       # List volumes
docker run -v mydata:/data postgres    # Use volume

# 🌐 NETWORK COMMANDS
docker network create mynet            # Create network
docker network ls                      # List networks
docker run --network mynet nginx       # Use network

# 📋 COMPOSE COMMANDS
docker-compose up                      # Start
docker-compose down                    # Stop
docker-compose logs                    # See logs
docker-compose ps                      # List services

# 🧹 CLEANUP COMMANDS
docker system prune                    # Clean unused
docker image prune                     # Clean images
docker volume prune                    # Clean volumes
```

### 📋 Common Options

| Option | What It Does | Example |
|--------|-------------|---------|
| `-d` | Detached (background) | `docker run -d nginx` |
| `-it` | Interactive terminal | `docker run -it ubuntu bash` |
| `-p` | Port mapping | `docker run -p 8080:80 nginx` |
| `-v` | Volume mount | `docker run -v data:/data postgres` |
| `-e` | Environment variable | `docker run -e VAR=value nginx` |
| `--name` | Container name | `docker run --name myapp nginx` |
| `-m` | Memory limit | `docker run -m 512m nginx` |
| `--network` | Connect to network | `docker run --network mynet nginx` |
| `-f` | Follow logs | `docker logs -f myapp` |

---

## 🔧 SECTION 14: TROUBLESHOOTING

### ❌ Common Problems & Solutions

| Problem | Solution |
|---------|----------|
| **Docker daemon not running** | Start Docker Desktop or `sudo systemctl start docker` |
| **Port already in use** | Change port: `docker run -p 8081:80 nginx` |
| **Permission denied** | `sudo usermod -aG docker $USER` then `newgrp docker` |
| **Container exits immediately** | Check logs: `docker logs myapp` |
| **Out of disk space** | Run: `docker system prune -a` |
| **Can't connect to container** | Verify network: `docker network inspect mynet` |
| **Volume data lost** | Use: `docker run -v mydata:/data ...` |
| **Image pull fails** | Login: `docker login` then try again |

### 🔍 Debugging Commands

```bash
# See what's wrong
docker logs myapp                    # Container logs
docker logs -f myapp                 # Live logs
docker inspect myapp                 # Full details
docker stats myapp                   # Resource usage
docker top myapp                     # Running processes

# Test connectivity
docker exec myapp ping 8.8.8.8       # Internet test
docker exec myapp curl localhost     # Local test
docker exec -it myapp bash           # Full terminal

# Check network
docker network inspect mynet         # Network details
docker exec myapp ping servicename   # Service test
```

### 📊 Check System Health

```bash
# See everything
docker info                          # System info
docker version                       # Docker version
docker ps -a                         # All containers
docker images                        # All images
docker volume ls                     # All volumes
docker network ls                    # All networks
```

---

## 🎓 FINAL TIPS & TRICKS

### 🚀 Speed Tips

```bash
# ✅ Clean up regularly
docker system prune -a               # Remove all unused stuff
docker image prune -a                # Remove unused images

# ✅ Use Alpine images (smaller)
FROM alpine:latest                   # 5MB!
FROM ubuntu:latest                   # 80MB

# ✅ Cache better in Dockerfile
FROM python:3.11
COPY requirements.txt .
RUN pip install -r requirements.txt   # Cached if unchanged
COPY . .                              # Only rebuild if needed
```

### 🎯 Learning Path

```
Step 1️⃣:  Learn basic commands
         docker run, docker ps, docker logs

Step 2️⃣:  Learn to build images
         Understand Dockerfile, docker build

Step 3️⃣:  Learn Docker Compose
         Multiple containers together

Step 4️⃣:  Learn networks & volumes
         How containers communicate

Step 5️⃣:  Learn best practices
         Security, performance, monitoring
```

### 📚 Resources

```
📖 Official Docs:     https://docs.docker.com/
🎥 Video Tutorials:   Search "Docker tutorial" on YouTube
🤝 Community:         https://forums.docker.com/
🔍 Docker Hub:        https://hub.docker.com/
📝 Cheatsheets:       https://docs.docker.com/get-started/
```

---

## ✨ SUMMARY

```
┌───────────────────────────────────────────────────────────┐
│           YOU NOW KNOW DOCKER! 🎉                          │
├───────────────────────────────────────────────────────────┤
│                                                            │
│  ✅ What Docker is & why it's awesome                      │
│  ✅ How to install & setup                                 │
│  ✅ How to use containers (run, stop, logs)                │
│  ✅ How to build your own images (Dockerfile)              │
│  ✅ How to run multiple containers (Docker Compose)        │
│  ✅ How to save data (Volumes)                             │
│  ✅ How containers talk (Networks)                         │
│  ✅ How to share images (Docker Hub)                       │
│  ✅ Best practices for production                          │
│  ✅ How to troubleshoot problems                           │
│                                                            │
│  🚀 Ready to deploy amazing applications!                  │
│                                                            │
└───────────────────────────────────────────────────────────┘
```

---

**Made with ❤️ by ***Mannan Kazi*** for Docker beginners**  
**Remember: Start small, experiment, and have fun! 🐳**
