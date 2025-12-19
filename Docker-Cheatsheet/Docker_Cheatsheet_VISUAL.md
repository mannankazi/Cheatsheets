# 🐳 DOCKER CHEATSHEET - Zero to Hero (Enhanced Visual Edition)

**Perfect for Beginners to Advanced Users**  
**Version:** 2.1 |
**Now with Visual Diagrams & Infographics!**

---

## 📚 TABLE OF CONTENTS

| # | Section | Emoji | Topics |
|---|---------|-------|--------|
| 1 | Getting Started | 🚀 | What is Docker, Key Concepts |
| 2 | Installation | 💻 | macOS, Windows, Linux Setup |
| 3 | Basics | 📦 | First Commands, Workflow |
| 4 | Images | 🖼️ | Pull, Build, Push, Manage |
| 5 | Containers | 🏗️ | Run, Stop, Logs, Execute |
| 6 | Dockerfile | 🔨 | Build Images, Best Practices |
| 7 | Compose | 📋 | Multiple Containers, Services |
| 8 | Networks | 🌐 | Container Communication |
| 9 | Volumes | 💾 | Save Data, Persistence |
| 10 | Docker Hub | 📤 | Share Images, Registries |
| 11 | Desktop GUI | 🖥️ | Visual Interface Guide |
| 12 | Production | ⭐ | Security, Monitoring |
| 13 | Commands | ⚡ | Quick Reference, Copy-Paste |
| 14 | Troubleshooting | 🔧 | Problems & Solutions |

---

## 🚀 SECTION 1: GETTING STARTED

### What is Docker? (Easy Explanation)

Think of Docker like **shipping containers for software**. Your app gets packaged with everything it needs to run!

```
WITHOUT DOCKER 😞
┌────────────┐  ┌────────────┐  ┌────────────┐
│ My Laptop  │  │ Your PC    │  │  Server    │
│ Works ✓    │  │ Broken ✗   │  │ Broken ✗   │
└────────────┘  └────────────┘  └────────────┘

WITH DOCKER 😊
┌────────────┐  ┌────────────┐  ┌────────────┐
│ 📦 My App  │  │ 📦 My App  │  │ 📦 My App  │
│ Works ✓    │  │ Works ✓    │  │ Works ✓    │
└────────────┘  └────────────┘  └────────────┘
```

**[Visual Reference: See Docker Concepts Infographic for detailed visual explanation]**

---

### 🎯 Key Docker Concepts (Super Simple!)

| Term | Meaning | Real-World Match |
|------|---------|------------------|
| 🖼️ **Image** | Blueprint (template) | Recipe card |
| 📦 **Container** | Running copy (instance) | Cooked meal |
| 🔨 **Dockerfile** | Instructions to build | Written recipe |
| 📚 **Registry** | Image storage (online) | Recipe book |
| 🔗 **Network** | How containers talk | Telephone line |
| 💾 **Volume** | Data storage (persistent) | Refrigerator |

---

## 💻 SECTION 2: INSTALLATION GUIDE

### 🍎 macOS - Super Easy!

```bash
# Option 1: Download Docker Desktop (Easiest! ⭐)
# Visit: https://www.docker.com/products/docker-desktop
# Download → Click → Done! 🎉

# Option 2: Using Homebrew (if you have it)
brew install docker docker-compose

# Check it works:
docker --version     # Shows Docker version
docker run hello-world  # Should print "Hello from Docker!"
```

---

### 🪟 Windows - Simple Setup!

```
Step 1: Download Docker Desktop
📍 https://www.docker.com/products/docker-desktop

Step 2: Run Installer
💾 Double-click Docker Desktop Installer.exe

Step 3: Enable WSL 2 (Windows Subsystem for Linux)
✓ Check the box during installation

Step 4: Restart Computer
🔄 Required for WSL 2 to work

Step 5: Done!
🎉 Open Docker Desktop from Start Menu
```

---

### 🐧 Linux - Copy & Paste!

**Ubuntu/Debian:**
```bash
# Easy one-liner installation
curl -fsSL https://get.docker.com -o get-docker.sh && sudo sh get-docker.sh

# Run without sudo
sudo usermod -aG docker $USER
newgrp docker

# Verify
docker --version
docker run hello-world
```

**CentOS/RHEL:**
```bash
sudo yum install docker-ce docker-ce-cli
sudo systemctl start docker
sudo systemctl enable docker    # Auto-start
docker --version
```

---

## 📦 SECTION 3: DOCKER BASICS

### 🎯 Your First Commands (Learn These!)

```bash
# 1️⃣ PULL - Download an image
docker pull nginx              # Get nginx image
docker pull ubuntu:22.04       # Specific version

# 2️⃣ RUN - Start a container
docker run nginx               # Run nginx (simple)
docker run -d nginx            # Run in background
docker run -p 8080:80 nginx    # Map port 8080→80
docker run -it ubuntu bash     # Interactive terminal!

# 3️⃣ PS - List containers
docker ps                      # Currently running
docker ps -a                   # All containers (including stopped)

# 4️⃣ LOGS - See what's happening
docker logs mycontainer        # Show logs once
docker logs -f mycontainer     # Live logs (press Ctrl+C to exit)

# 5️⃣ STOP - Stop container
docker stop mycontainer

# 6️⃣ REMOVE - Delete container
docker rm mycontainer

# 7️⃣ CLEAN UP - Delete unused stuff
docker system prune            # Remove dangling items
```

---

### 📊 Basic Docker Workflow

```
┌─────────────────────────────────────────┐
│  PULL (Get Image)                       │
│  docker pull nginx                      │
│  ✓ Downloads blueprint from Docker Hub  │
└──────────────┬──────────────────────────┘
               ▼
┌─────────────────────────────────────────┐
│  RUN (Create Container)                 │
│  docker run -d nginx                    │
│  ✓ Creates running instance from image  │
└──────────────┬──────────────────────────┘
               ▼
┌─────────────────────────────────────────┐
│  USE (Application is Running)           │
│  ✓ Visit http://localhost:8080          │
│  ✓ Container doing its job              │
└──────────────┬──────────────────────────┘
               ▼
┌─────────────────────────────────────────┐
│  STOP (When done)                       │
│  docker stop mycontainer                │
│  ✓ Gracefully shuts down                │
└─────────────────────────────────────────┘
```

**[Visual Reference: See Docker Architecture Diagram for complete workflow]**

---

## 🖼️ SECTION 4: WORKING WITH IMAGES

### 📥 Download & Find Images

```bash
# PULL - Get image from Docker Hub
docker pull nginx               # Latest version
docker pull nginx:1.25.3        # Specific version
docker pull ubuntu:22.04        # Ubuntu Linux

# LIST - See images you have
docker images                   # All your images
docker images | grep nginx      # Search locally

# SEARCH - Find online
docker search nginx             # Search Docker Hub
docker search python --limit 5  # Limit results
```

### 🏗️ Build Your Own Image

```bash
# CREATE a Dockerfile first (see Section 6)
cat > Dockerfile << 'EOF'
FROM python:3.11-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
EOF

# BUILD
docker build -t myapp:1.0 .     # -t = name:version

# BUILD (force rebuild, no cache)
docker build --no-cache -t myapp:1.0 .
```

### 🏷️ Tag & Share Images

```bash
# TAG - Create new name/version
docker tag myapp:1.0 myapp:latest
docker tag myapp:1.0 username/myapp:1.0  # For Docker Hub

# PUSH - Upload to Docker Hub
docker login                           # Login first
docker push username/myapp:1.0         # Upload

# OTHERS can now use
docker pull username/myapp:1.0
```

### 🗑️ Clean Up Images

```bash
docker rmi nginx                # Delete image
docker rmi -f nginx             # Force delete
docker image prune              # Remove unused
docker image prune -a           # Remove ALL unused
```

---

## 🏗️ SECTION 5: WORKING WITH CONTAINERS

### 🎮 Container Commands Explained

```bash
┌─────────────────────────────────────────────────────┐
│           RUN - Create & Start Container            │
├─────────────────────────────────────────────────────┤
│                                                      │
│  docker run nginx                                    │
│  └─ Run nginx normally (foreground)                  │
│                                                      │
│  docker run -d nginx                                 │
│  └─ Run in background (-d = detached)               │
│                                                      │
│  docker run -p 8080:80 nginx                         │
│  └─ Map your port 8080 → container port 80          │
│                                                      │
│  docker run -it ubuntu bash                          │
│  └─ Interactive terminal (-it = interactive)         │
│                                                      │
│  docker run --name myapp nginx                       │
│  └─ Give container a name                           │
│                                                      │
│  docker run -m 512m nginx                            │
│  └─ Limit memory to 512MB                            │
│                                                      │
└─────────────────────────────────────────────────────┘
```

```bash
┌─────────────────────────────────────────────────────┐
│           PS - List Containers                      │
├─────────────────────────────────────────────────────┤
│                                                      │
│  docker ps                                           │
│  └─ Running containers only                         │
│                                                      │
│  docker ps -a                                        │
│  └─ ALL containers (running + stopped)              │
│                                                      │
│  Output shows:                                       │
│  • CONTAINER ID (unique identifier)                 │
│  • IMAGE (what image it uses)                       │
│  • STATUS (running/exited)                          │
│  • PORTS (port mappings)                            │
│  • NAMES (container name)                           │
│                                                      │
└─────────────────────────────────────────────────────┘
```

```bash
┌─────────────────────────────────────────────────────┐
│      START/STOP/RESTART - Control Containers       │
├─────────────────────────────────────────────────────┤
│                                                      │
│  docker start myapp       • Start stopped one       │
│  docker stop myapp        • Stop running one        │
│  docker restart myapp     • Restart (stop + start)  │
│  docker pause myapp       • Pause (suspend)         │
│  docker unpause myapp     • Resume from pause       │
│  docker kill myapp        • Force stop              │
│                                                      │
└─────────────────────────────────────────────────────┘
```

```bash
┌─────────────────────────────────────────────────────┐
│         LOGS - See What Container is Doing         │
├─────────────────────────────────────────────────────┤
│                                                      │
│  docker logs myapp                                   │
│  └─ Show all logs (once)                            │
│                                                      │
│  docker logs -f myapp                                │
│  └─ Follow logs (live view, press Ctrl+C to exit)   │
│                                                      │
│  docker logs --tail 100 myapp                        │
│  └─ Show last 100 lines only                        │
│                                                      │
│  💡 Tip: Use logs to debug when things go wrong!    │
│                                                      │
└─────────────────────────────────────────────────────┘
```

```bash
┌─────────────────────────────────────────────────────┐
│      EXEC - Run Command Inside Container            │
├─────────────────────────────────────────────────────┤
│                                                      │
│  docker exec myapp whoami                            │
│  └─ Run command once                                │
│                                                      │
│  docker exec -it myapp bash                          │
│  └─ Open interactive terminal inside container      │
│                                                      │
│  docker exec -it myapp /bin/sh                       │
│  └─ Open shell (for Alpine Linux)                   │
│                                                      │
│  💡 Useful for debugging inside running container!  │
│                                                      │
└─────────────────────────────────────────────────────┘
```

### 📍 Port Mapping (Visual Explanation)

```
YOUR COMPUTER          DOCKER CONTAINER
┌──────────────┐       ┌──────────────┐
│ Port 8080    │──────→│ Port 80      │
│ (your access)│       │ (app running)│
└──────────────┘       └──────────────┘

Command: docker run -p 8080:80 nginx
         └──────────┬──────────┘
         Host:Container port

Visit: http://localhost:8080
Your browser
    ↓
Port 8080 on your PC
    ↓
Forwards to port 80 in container
    ↓
Nginx receives traffic
```

**[Visual Reference: See Container Lifecycle, Ports & Volumes diagram]**

---

## 🔨 SECTION 6: DOCKERFILE - CREATE YOUR OWN IMAGE

### 📝 What is a Dockerfile?

A **Dockerfile** is a text file with instructions to build an image. Think of it as a recipe!

### 🏗️ Simple Dockerfile Example

```dockerfile
# Step 1: Start with a base image
FROM python:3.11-slim

# Step 2: Set working directory
WORKDIR /app

# Step 3: Copy files
COPY requirements.txt .

# Step 4: Install dependencies
RUN pip install -r requirements.txt

# Step 5: Copy application code
COPY . .

# Step 6: Expose port
EXPOSE 8000

# Step 7: Run command
CMD ["python", "app.py"]
```

### 🎯 Dockerfile Instructions (Simple Explanations)

| Instruction | What | Example |
|-------------|------|---------|
| **FROM** | Starting point (base image) | `FROM python:3.11` |
| **WORKDIR** | Where to work inside | `WORKDIR /app` |
| **COPY** | Copy files from you → container | `COPY . /app` |
| **RUN** | Execute command during build | `RUN pip install flask` |
| **EXPOSE** | Which port container uses | `EXPOSE 8000` |
| **ENV** | Set environment variable | `ENV DEBUG=true` |
| **CMD** | Default startup command | `CMD ["python", "app.py"]` |
| **LABEL** | Add info/tags | `LABEL version="1.0"` |
| **USER** | Which user to run as | `USER appuser` |

### ✅ GOOD vs ❌ BAD Dockerfile

```dockerfile
✅ GOOD DOCKERFILE:

FROM python:3.11-slim          # ✓ Specific version, small image
LABEL maintainer="you@example.com"  # ✓ Add labels
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt  # ✓ Install dependencies first
COPY . .                              # ✓ Copy code last (better caching)
RUN useradd -m appuser               # ✓ Non-root user (security)
USER appuser                         # ✓ Run as non-root
EXPOSE 8000
CMD ["python", "app.py"]

────────────────────────────────────────────────────────

❌ BAD DOCKERFILE:

FROM python:latest             # ✗ Using "latest" is risky
# ✗ No labels
COPY . /app                    # ✗ Copy everything at once
RUN pip install everything    # ✗ Installs all packages
# ✗ Running as root (security risk)
CMD ["python", "app.py"]

────────────────────────────────────────────────────────

WHY GOOD IS BETTER:

1️⃣ Specific versions = predictable, reproducible
2️⃣ Small base images = faster, smaller files
3️⃣ Copy dependencies first = better caching (faster rebuilds)
4️⃣ Copy code after = only rebuilds when code changes
5️⃣ Non-root user = more secure
```

**[Visual Reference: See Dockerfile Best Practices Comparison chart]**

### 🔨 Build Your Image

```bash
# Basic build
docker build -t myapp:1.0 .
        ↓
        ├─ -t = give it a name and version
        └─ . = use Dockerfile in current folder

# Build without cache (rebuild everything)
docker build --no-cache -t myapp:1.0 .

# Build from specific Dockerfile
docker build -f Dockerfile.prod -t myapp:prod .

# Build with build arguments
docker build --build-arg VERSION=1.0 -t myapp:1.0 .
```

### 📊 Build Process (How It Works)

```
Dockerfile               Building              Docker Image
┌────────┐            ┌────────┐            ┌────────┐
│FROM    │──build────→│Layer 1 │────build───→│Image   │
│ubuntu  │            │(Ubuntu)│            │(155MB) │
└────────┘            └────────┘            └────────┘
┌────────┐            ┌────────┐
│RUN apt │──build────→│Layer 2 │
│install │            │(+ curl)│
└────────┘            └────────┘
┌────────┐            ┌────────┐
│COPY    │──build────→│Layer 3 │
│app.py  │            │+app.py │
└────────┘            └────────┘

Each instruction = one layer
Layers stack on top of each other
Final image = all layers combined
```

---

## 📋 SECTION 7: DOCKER COMPOSE - MULTIPLE CONTAINERS

### 🎯 What is Docker Compose?

Run **multiple containers together** with one command! Perfect for apps with database, cache, etc.

### 📝 Simple Example

```yaml
version: '3.8'

services:
  # Nginx web server
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html

  # PostgreSQL database
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
    volumes:
      - dbdata:/var/lib/postgresql/data

volumes:
  dbdata:              # Named volume
```

### 🎮 Docker Compose Commands

```bash
# START all services
docker-compose up           # Start (show logs)
docker-compose up -d        # Start in background

# STOP all services
docker-compose down         # Stop and remove

# VIEW status
docker-compose ps           # List services
docker-compose logs         # Show all logs
docker-compose logs -f web  # Follow specific service

# REBUILD
docker-compose build        # Rebuild if needed
docker-compose build --no-cache  # Force rebuild

# RESTART
docker-compose restart

# PAUSE
docker-compose pause
docker-compose unpause
```

### 📊 Real Example: Web + Database

```yaml
version: '3.8'

services:
  # Python/Flask Web App
  web:
    build: .                    # Build from Dockerfile
    container_name: flask_app
    ports:
      - "5000:5000"             # localhost:5000
    environment:
      DATABASE_URL: postgresql://user:pass@db:5432/mydb
    depends_on:
      - db                      # Start db first
    volumes:
      - ./app:/app              # Sync code changes

  # PostgreSQL Database
  db:
    image: postgres:15
    container_name: postgres_db
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: pass
      POSTGRES_DB: mydb
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:                # Persistent storage
```

### 🔗 How Services Talk

```
Inside docker-compose.yml:

FROM WEB service:
  Connect to database: db:5432
  (NOT localhost or IP address!)
  
  Magic: Docker automatically creates DNS
  'db' = the database container
  Works automatically! ✨

FROM DB service:
  Connect to web: web:5000
```

**[Visual Reference: See Docker Compose Multi-Container Stack diagram]**

---

## 🌐 SECTION 8: NETWORKS - CONNECT CONTAINERS

### 🤝 How Containers Talk

Containers on the same **network** can communicate using **service names** (automatic DNS)!

### 🎮 Quick Network Commands

```bash
docker network create myapp    # Create network
docker network ls              # List networks
docker network rm myapp        # Delete network

docker run --network myapp nginx    # Connect to network
docker network connect myapp container  # Connect running
docker network disconnect myapp container  # Disconnect
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

# Inside webapp, connect to: db:5432
# NOT localhost! Docker DNS resolves 'db' automatically!
```

---

## 💾 SECTION 9: VOLUMES - SAVE YOUR DATA

### ⚠️ Why Volumes Matter!

```
❌ WITHOUT VOLUMES:

docker run postgres
[Create database]
docker stop postgres
docker rm postgres
⚠️ ALL DATA LOST! 😢


✅ WITH VOLUMES:

docker run -v mydata:/data postgres
[Create database in volume]
docker stop postgres
docker rm postgres
✓ DATA STILL EXISTS! 😊
docker run -v mydata:/data postgres
✓ Data restored!
```

### 🎮 Volume Commands

```bash
docker volume create mydata      # Create volume
docker volume ls                 # List volumes
docker volume inspect mydata     # See details
docker volume rm mydata          # Delete volume
docker volume prune              # Remove unused
```

### 💡 Use Volumes

```bash
# Named volume (recommended for data)
docker run -v mydata:/data postgres
        └─────┬─────┘
        volume name (created by Docker)

# Bind mount (good for development)
docker run -v $(pwd)/app:/app myapp
        └─ your folder on computer

# Read-only volume
docker run -v mydata:/data:ro postgres
                        └── :ro = read-only
```

### 📊 Volume Types

| Type | What | Use |
|------|------|-----|
| **Named** | Docker manages | Production data |
| **Bind Mount** | Your folder | Development, code |
| **Tmpfs** | RAM (temporary) | Cache, temp files |

**[Visual Reference: See Container Lifecycle, Ports & Volumes diagram]**

---

## 📤 SECTION 10: DOCKER HUB & REGISTRIES

### 🌍 What is Docker Hub?

**Docker Hub** = GitHub for container images. Upload and download images!

### 🎯 Quick Workflow

```bash
# Step 1: Sign up
# Visit: https://hub.docker.com
# Create free account

# Step 2: Login locally
docker login
# Enter username and password

# Step 3: Tag image (format: username/imagename)
docker tag myapp:1.0 username/myapp:1.0

# Step 4: Push (upload)
docker push username/myapp:1.0

# Step 5: Others can use it!
docker pull username/myapp:1.0
```

### 💻 Commands

```bash
# Login
docker login
docker logout

# Push (upload)
docker push username/myapp:1.0

# Pull (download)
docker pull username/myapp:1.0

# Search
docker search nginx

# Tag for Docker Hub
docker tag myapp:1.0 username/myapp:1.0
```

---

## 🖥️ SECTION 11: DOCKER DESKTOP GUI

### 🎨 Visual Interface

Docker Desktop GUI lets you manage everything without commands!

### 📌 Main Sections

```
LEFT SIDEBAR          →        MAIN PANEL

📦 Containers         →        Container Details
   • See all                   • Status
   • Start/Stop               • Ports
   • View logs                • CPU/Memory
   • Delete

🖼️ Images            →        Image Details
   • Download                 • Size
   • Build                    • Created
   • Delete
   • Push

💾 Volumes           →        Volume Details
   • Create                   • Size
   • Delete                   • Used by

🌐 Networks          →        Network Details
   • Create                   • Containers
   • Delete                   • IPs
```

### 🎯 Common Tasks

```
✅ RUN AN IMAGE
   1. Click Containers tab
   2. Click image name
   3. Click play button ▶️

✅ STOP CONTAINER
   1. Click Containers
   2. Click stop button ⏹️

✅ VIEW LOGS
   1. Click container
   2. Click "Logs" section
   3. See live output

✅ OPEN TERMINAL
   1. Click container
   2. Click "Exec" button
   3. Terminal inside container

✅ DOWNLOAD IMAGE
   1. Click "Search" button
   2. Type image name
   3. Click "Pull"
```

---

## ⭐ SECTION 12: PRODUCTION TIPS & TRICKS

### 🔒 Security Best Practices

```bash
✅ DO THIS:

# Use specific versions (predictable)
FROM python:3.11.7-slim
docker run postgres:15.2

# Run as non-root user (secure)
RUN useradd -m appuser
USER appuser

# Use small base images (less attack surface)
FROM alpine:latest           # 5MB
FROM python:3.11-alpine      # 40MB

# Set resource limits (prevent resource hogging)
docker run -m 512m --cpus 1 myapp

# Environment variables for secrets
docker run -e DB_PASSWORD=secret myapp

# Keep images clean (no unnecessary packages)
RUN apt-get update && apt-get install -y \
    package1 package2 \
    && rm -rf /var/lib/apt/lists/*


❌ AVOID THIS:

# Latest tag (unpredictable, unsafe)
FROM python:latest

# Running as root (security risk!)
# (default behavior)

# Large base images
FROM ubuntu:latest           # 80MB

# No resource limits
docker run myapp

# Hardcoding secrets in image
ENV PASSWORD=secret123
```

### 🔄 Auto-Restart Containers

```bash
# Always restart
docker run --restart always myapp

# Restart on failure (max 5 times)
docker run --restart on-failure:5 myapp

# Don't restart (default)
docker run --restart no myapp
```

### 📈 Monitor Resources

```bash
# Live stats
docker stats

# For specific container
docker stats myapp --no-stream

# View limits
docker inspect myapp | grep -i memory
```

### 💾 Backup Data

```bash
# Backup volume to tar
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

---

## ⚡ SECTION 13: QUICK COMMANDS REFERENCE

### 🎯 Most Used (Copy & Paste Friendly!)

```bash
════════════════════════════════════════════════════════════

🖼️ IMAGE COMMANDS

docker pull nginx               ← Download image
docker build -t myapp:1.0 .   ← Build image
docker images                  ← List all images
docker rmi nginx               ← Delete image
docker search nginx            ← Search images
docker push username/myapp:1.0 ← Upload image

════════════════════════════════════════════════════════════

📦 CONTAINER COMMANDS

docker run -d nginx              ← Run in background
docker run -it ubuntu bash       ← Interactive terminal
docker ps                        ← List running
docker ps -a                     ← List all
docker start myapp               ← Start container
docker stop myapp                ← Stop container
docker rm myapp                  ← Delete container
docker logs myapp                ← See logs
docker logs -f myapp             ← Live logs
docker exec -it myapp bash       ← Open terminal

════════════════════════════════════════════════════════════

💾 VOLUME COMMANDS

docker volume create mydata        ← Create volume
docker volume ls                   ← List volumes
docker run -v mydata:/data postgres  ← Use volume

════════════════════════════════════════════════════════════

🌐 NETWORK COMMANDS

docker network create mynet         ← Create network
docker network ls                   ← List networks
docker run --network mynet nginx    ← Use network

════════════════════════════════════════════════════════════

📋 COMPOSE COMMANDS

docker-compose up              ← Start
docker-compose up -d           ← Start (background)
docker-compose down            ← Stop
docker-compose logs            ← View logs
docker-compose ps              ← List services

════════════════════════════════════════════════════════════

🧹 CLEANUP COMMANDS

docker system prune            ← Clean unused
docker image prune             ← Clean unused images
docker volume prune            ← Clean unused volumes

════════════════════════════════════════════════════════════
```

### 📋 Common Options

| Option | Meaning | Example |
|--------|---------|---------|
| `-d` | Detached (background) | `docker run -d nginx` |
| `-it` | Interactive + Terminal | `docker run -it ubuntu bash` |
| `-p` | Port mapping | `docker run -p 8080:80 nginx` |
| `-v` | Volume mount | `docker run -v data:/data postgres` |
| `-e` | Environment variable | `docker run -e VAR=value nginx` |
| `--name` | Container name | `docker run --name myapp nginx` |
| `-m` | Memory limit | `docker run -m 512m nginx` |
| `--network` | Network | `docker run --network mynet nginx` |
| `-f` | Follow output | `docker logs -f myapp` |

---

## 🔧 SECTION 14: TROUBLESHOOTING

### ❌ Common Problems & Quick Fixes

| Problem | Quick Fix |
|---------|-----------|
| **Docker won't start** | Start Docker Desktop (click app icon) |
| **Permission denied** | `sudo usermod -aG docker $USER` then restart |
| **Port 8080 already in use** | Use different port: `docker run -p 8081:80 nginx` |
| **Container exits immediately** | Check logs: `docker logs myapp` |
| **Out of disk** | Clean up: `docker system prune -a` |
| **Can't connect to service** | Verify network: `docker network inspect mynet` |
| **Data disappeared** | Use volumes next time: `docker run -v data:/data ...` |
| **Can't push image** | Login first: `docker login` |

### 🔍 Debugging Commands

```bash
docker logs myapp                    # See what went wrong
docker logs -f myapp                 # Live view
docker inspect myapp                 # Full configuration
docker stats myapp                   # Resource usage
docker top myapp                     # Running processes
docker exec myapp ping 8.8.8.8       # Test internet
docker exec -it myapp bash           # Full terminal access
```

---

## 🎓 FINAL LEARNING PATH

```
Week 1: BASICS
├─ Install Docker
├─ Learn: pull, run, ps, logs, stop
├─ Practice: Run 5 different images
└─ Goal: Comfortable with containers

Week 2: BUILDING IMAGES
├─ Understand Dockerfile
├─ Write first Dockerfile
├─ Learn: build, tag
└─ Goal: Build and run your own image

Week 3: MULTIPLE CONTAINERS
├─ Learn Docker Compose
├─ Create docker-compose.yml
├─ Practice: Web + Database
└─ Goal: Run multi-container app

Week 4: ADVANCED
├─ Learn: Networks, Volumes
├─ Learn: Production best practices
├─ Deploy somewhere
└─ Goal: Production-ready app

📚 Keep learning and experimenting!
```

---

## ✨ YOU'VE GOT THIS! 🎉

```
┌─────────────────────────────────────────┐
│ ✅ YOU NOW KNOW DOCKER!                  │
├─────────────────────────────────────────┤
│                                          │
│ ✓ What Docker is & why it's awesome      │
│ ✓ How to install & setup                 │
│ ✓ How to use containers                  │
│ ✓ How to build images                    │
│ ✓ How to run multiple containers         │
│ ✓ How to save data (volumes)             │
│ ✓ How containers communicate             │
│ ✓ How to share images                    │
│ ✓ Best practices & troubleshooting       │
│                                          │
│ 🚀 Ready to deploy amazing apps!         │
│                                          │
│ 💡 Pro Tip: Keep practicing!             │
│    The more you use Docker, the          │
│    more natural it becomes!              │
│                                          │
└─────────────────────────────────────────┘
```

---

### 📚 Resources & Links

```
📖 Official Docs: https://docs.docker.com/
🎥 Video Tutorials: Search "Docker tutorial" on YouTube
🤝 Community Help: https://forums.docker.com/
📦 Find Images: https://hub.docker.com/
💬 Questions: Stack Overflow Docker tag
```

---

**Made with ❤️ by ***Mannan Kazi*** for Docker Beginners**  
**Start small → Experiment → Have fun! 🐳**

**Remember:**
- Everyone starts as a beginner
- It's okay to make mistakes
- Practice makes perfect
- The Docker community is helpful!

Happy containerizing! 🚀✨
