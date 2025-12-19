# 🐳 DOCKER ADVANCED CHEATSHEET - Deep Dive

**Enterprise & Production-Level Guide**  
**Version:** 3.0 | 
**Advanced Topics & Deep Concepts**

---

## 📚 ADVANCED TOPICS TABLE OF CONTENTS

| # | Section | Level | Topics |
|---|---------|-------|--------|
| 1 | Advanced Dockerfile | 🔴 Hard | Multi-stage, Layer caching, Optimization |
| 2 | Docker BuildKit | 🔴 Hard | Modern builds, Parallel execution, Caching |
| 3 | Advanced Networking | 🔴 Hard | Overlay, Macvlan, Custom drivers, Policies |
| 4 | Storage & Drivers | 🔴 Hard | Layers, Copy-on-write, Storage drivers |
| 5 | Docker Swarm | 🔴 Hard | Orchestration, Services, Clustering |
| 6 | Container Security | 🔴 Hard | Capabilities, Seccomp, AppArmor, Signing |
| 7 | Logging & Monitoring | 🔴 Hard | ELK Stack, Prometheus, Datadog |
| 8 | Registry Management | 🔴 Hard | Private registries, Harbor, Artifactory |
| 9 | API & Programmatic Control | 🔴 Hard | Docker SDK, Remote API, Automation |
| 10 | Performance Tuning | 🔴 Hard | Optimization, Benchmarking, Profiling |
| 11 | Debugging & Profiling | 🔴 Hard | Deep inspection, dlv, pprof, Trace |
| 12 | Custom Networks & Plugins | 🔴 Hard | Custom drivers, Third-party plugins |
| 13 | Image Optimization | 🔴 Hard | Size reduction, Layer management |
| 14 | CI/CD Integration | 🔴 Hard | Jenkins, GitLab CI, GitHub Actions |
| 15 | High Availability & Backup | 🔴 Hard | HA clusters, Disaster recovery |
| 16 | Advanced Compose Features | 🔴 Hard | Overrides, Interpolation, Extensions |

---

## 🔴 SECTION 1: ADVANCED DOCKERFILE TECHNIQUES

### 🎯 Multi-Stage Builds (Production Optimization)

**What is it?** Use multiple FROM statements to create multiple build stages, keeping only final artifacts.

**Problem it solves:** Reduce final image size by excluding build tools.

```dockerfile
# STAGE 1: Builder
FROM golang:1.21 as builder
WORKDIR /app
COPY . .
RUN go build -o myapp .
# Result: 800MB image with all build tools

# STAGE 2: Runtime
FROM alpine:3.18
WORKDIR /app
COPY --from=builder /app/myapp .
EXPOSE 8000
CMD ["./myapp"]
# Result: 50MB final image!
```

**Key Concepts:**
```bash
# Each FROM = new stage
# Use --from=stagename to copy between stages
# Only final stage is in image
# Previous stages are discarded

# Build specific stage
docker build --target builder -t myapp:builder .

# Use different names for clarity
FROM golang:1.21 as build-stage
FROM alpine:3.18 as final-stage
FROM ubuntu:22.04 as testing-stage
```

**Real-World Example: Python Multi-Stage**

```dockerfile
# Stage 1: Build dependencies
FROM python:3.11 as builder
WORKDIR /wheels
COPY requirements.txt .
RUN pip wheel --no-cache-dir --no-deps --wheel-dir /wheels -r requirements.txt

# Stage 2: Runtime
FROM python:3.11-slim
WORKDIR /app
COPY --from=builder /wheels /wheels
COPY --from=builder requirements.txt .
RUN pip install --no-cache /wheels/*
COPY . .
CMD ["python", "app.py"]

# Benefits:
# ✓ Stage 1: 1GB (build environment)
# ✓ Stage 2: 200MB (runtime only)
# ✓ 80% size reduction!
```

---

### 🔗 Layer Caching Optimization

**Understanding Docker Layers:**

```dockerfile
# Layer 1: Base image
FROM python:3.11-slim

# Layer 2: Environment
ENV PYTHONUNBUFFERED=1

# Layer 3: Dependencies (changes rarely)
COPY requirements.txt .
RUN pip install -r requirements.txt

# Layer 4: Application code (changes often)
COPY . .

# Layer 5: Metadata
EXPOSE 8000

# Layer 6: Startup
CMD ["python", "app.py"]
```

**Cache Invalidation Strategy:**

```dockerfile
# ❌ BAD: Copy everything at once (cache broken by any change)
FROM python:3.11
COPY . .                           # Layer changes with any file
RUN pip install -r requirements.txt
CMD ["python", "app.py"]

# ✅ GOOD: Layer dependencies separately (smarter caching)
FROM python:3.11
COPY requirements.txt .            # Only if requirements.txt changes
RUN pip install -r requirements.txt
COPY . .                           # Application changes don't break dependency cache
CMD ["python", "app.py"]

# ✅ BETTER: Separate static and dynamic
FROM python:3.11
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install -r requirements.txt  # Cached unless requirements change

COPY . .                             # Application code
CMD ["python", "app.py"]
```

**Cache Control Commands:**

```bash
# Rebuild everything (no cache)
docker build --no-cache -t myapp:1.0 .

# Build with specific cache source
docker build --cache-from myrepo/myapp:latest -t myapp:1.0 .

# Build with inline caching (for BuildKit)
DOCKER_BUILDKIT=1 docker build \
  --build-arg BUILDKIT_INLINE_CACHE=1 \
  -t myapp:1.0 .

# View build history (see layers)
docker history myapp:1.0

# View layer details
docker image inspect myapp:1.0 | jq '.[0].RootFS.Layers'
```

---

### 🎨 Advanced Dockerfile Features

**ARG vs ENV Deep Dive:**

```dockerfile
# ARG: Available ONLY during build
ARG BUILD_DATE
ARG VERSION=1.0
RUN echo "Building version ${VERSION} on ${BUILD_DATE}"
# Variables NOT available in running container

# ENV: Available during build AND runtime
ENV APP_ENV=production
ENV LOG_LEVEL=info
# Variables AVAILABLE in running container

# ARG sets ENV default
ARG PYTHON_VERSION=3.11
FROM python:${PYTHON_VERSION}  # Uses ARG
ENV PYTHON_VERSION=${PYTHON_VERSION}  # Convert to ENV

# Build-time vs Runtime
FROM python:3.11
ARG BUILD_DATE        # Build only
ENV BUILD_TIME_ZONE=UTC  # Runtime available
```

**Using ARG in Practice:**

```dockerfile
# Build arguments for flexibility
ARG DEBIAN_FRONTEND=noninteractive
ARG REGISTRY=docker.io
ARG BASE_IMAGE=ubuntu:22.04

FROM ${REGISTRY}/${BASE_IMAGE}

ARG PACKAGES="curl git vim"
RUN apt-get update && apt-get install -y ${PACKAGES}

LABEL build.date="${BUILD_DATE}" \
      vcs.ref="${VCS_REF}" \
      version="${VERSION}"
```

**Build with arguments:**
```bash
docker build \
  --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
  --build-arg VCS_REF=$(git rev-parse --short HEAD) \
  --build-arg VERSION=1.2.3 \
  -t myapp:1.2.3 .
```

**HEALTHCHECK - Advanced Monitoring:**

```dockerfile
# Basic healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost/health || exit 1

# Custom healthcheck for different apps
# For web service
HEALTHCHECK --interval=10s --timeout=3s --retries=3 \
  CMD curl -f http://localhost:8000/health || exit 1

# For database
HEALTHCHECK --interval=10s --timeout=5s --retries=3 \
  CMD pg_isready -U postgres || exit 1

# For Python app
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
  CMD python -c "import socket; socket.create_connection(('localhost', 8000), timeout=3)" || exit 1

# Get healthcheck status
docker inspect --format='{{.State.Health.Status}}' mycontainer
# Output: healthy, unhealthy, or starting
```

---

### 📦 ONBUILD & ADVANCED DIRECTIVES

```dockerfile
# ONBUILD: Execute in child images
FROM ubuntu:22.04
ONBUILD RUN echo "This runs in child images"
ONBUILD COPY . /app
ONBUILD WORKDIR /app
# Creates base for other Dockerfiles to inherit

# Child Dockerfile
FROM mybase:latest
# Automatically executes:
# - RUN echo "This runs in child images"
# - COPY . /app
# - WORKDIR /app
```

---

## 🚀 SECTION 2: DOCKER BUILDKIT - ADVANCED BUILDS

### 🎯 What is BuildKit?

Modern Docker build system with:
- ✅ Parallel execution (faster builds)
- ✅ Better caching
- ✅ Cross-platform builds
- ✅ Advanced features

### 🔨 Enable BuildKit

```bash
# Method 1: Environment variable
export DOCKER_BUILDKIT=1
docker build -t myapp:1.0 .

# Method 2: Daemon configuration
{
  "features": {
    "buildkit": true
  }
}

# Method 3: Docker CLI (newer versions)
docker buildx create --name mybuilder
docker buildx use mybuilder
```

### 🎨 BuildKit Features

**1. Cache Mounts (Persist cache between builds)**

```dockerfile
# Without cache: dependencies reinstalled each time
FROM python:3.11
RUN pip install -r requirements.txt

# With cache: cached packages reused
FROM python:3.11
RUN --mount=type=cache,target=/root/.cache/pip \
    pip install -r requirements.txt

# For Node.js
RUN --mount=type=cache,target=/root/.npm \
    npm ci

# For apt
RUN --mount=type=cache,target=/var/cache/apt \
    apt-get update && apt-get install -y package
```

**Build with cache mounts:**
```bash
DOCKER_BUILDKIT=1 docker build -t myapp:1.0 .
```

**2. Secrets Mount (Secure secrets during build)**

```dockerfile
# Access secrets without exposing in layers
FROM node:18
RUN --mount=type=secret,id=npm_token \
    npm set //registry.npmjs.org/:_authToken=$(cat /run/secrets/npm_token) && \
    npm install

# Or for SSH keys
RUN --mount=type=ssh \
    git clone git@github.com:myrepo/mycode.git /app
```

**Build with secrets:**
```bash
DOCKER_BUILDKIT=1 docker build \
  --secret npm_token=/path/to/token \
  -t myapp:1.0 .

# Or with SSH
docker build \
  --ssh default \
  -t myapp:1.0 .
```

**3. Progress Output**

```bash
# Show build steps
DOCKER_BUILDKIT=1 docker build --progress=plain -t myapp:1.0 .

# Show as plain text (no spinner)
DOCKER_BUILDKIT=1 docker build --progress=plain -t myapp:1.0 .
```

**4. Build Metadata**

```dockerfile
# Add build metadata
ARG BUILDKIT_INLINE_CACHE=1
ARG BUILDKIT_CONTEXT_KEEP_GIT_DIR=1

FROM alpine:3.18
# These enable caching and Git metadata
```

### 📊 Parallel Builds

```dockerfile
# BuildKit executes in parallel when possible
FROM python:3.11
WORKDIR /app

# These run in parallel
RUN apt-get update && apt-get install -y build-essential
RUN pip install numpy pandas scipy

# This waits for above
COPY requirements.txt .
RUN pip install -r requirements.txt

# BuildKit optimizes order automatically
```

---

## 🌐 SECTION 3: ADVANCED NETWORKING

### 🎯 Network Types Deep Dive

**1. Bridge Network (Default)**

```bash
# Create custom bridge
docker network create \
  --driver bridge \
  --subnet=172.20.0.0/16 \
  --ip-range=172.20.240.0/20 \
  --gateway=172.20.240.1 \
  mybridge

# Connect containers
docker run -d --network mybridge --ip 172.20.240.2 --name app1 nginx
docker run -d --network mybridge --ip 172.20.240.3 --name app2 nginx

# They communicate: ping app1 (from app2)
docker exec app2 ping app1
```

**2. Overlay Network (Swarm/Multi-host)**

```bash
# Only in Swarm mode
docker swarm init

# Create overlay network
docker network create \
  --driver overlay \
  --subnet=10.0.0.0/24 \
  --opt encrypted=true \
  swarmnet

# Services connect
docker service create \
  --network swarmnet \
  --name web \
  nginx

docker service create \
  --network swarmnet \
  --name db \
  postgres
```

**3. Macvlan Network (Physical network access)**

```bash
# Create Macvlan network
docker network create \
  --driver macvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  --ip-range=192.168.1.100/28 \
  -o parent=eth0 \
  mymacvlan

# Container gets IP from physical network
docker run -d \
  --network mymacvlan \
  --ip=192.168.1.100 \
  --name myapp \
  nginx

# From other machines on network:
# ping 192.168.1.100
# Container appears as physical device!
```

**4. IPvlan Network (Advanced)**

```bash
# Similar to Macvlan, different approach
docker network create \
  --driver ipvlan \
  --subnet=192.168.1.0/24 \
  --gateway=192.168.1.1 \
  -o parent=eth0 \
  -o ipvlan_mode=l2 \
  myipvlan

# l2 mode: Physical network access
# l3 mode: IP routing
```

### 🔒 Network Policies & Security

**Host Network (Advanced - No isolation)**

```bash
# Container shares host network stack
docker run --network host nginx
# Ports directly exposed on host
# No port mapping needed
# Useful for: performance-critical apps, network tools

# WARNING: Less isolation, security risk
```

**DNS & Service Discovery**

```bash
# Automatic DNS resolution
docker network create mynet
docker run -d --network mynet --name db postgres:15
docker run -d --network mynet --name app myapp:latest

# From app, access db:
# Automatically resolves to correct IP
# Works across containers

# Custom DNS
docker run \
  --dns 8.8.8.8 \
  --dns-search example.com \
  mycontainer

# Embedded DNS server
docker exec mycontainer nslookup db
# Returns: 172.17.0.2 (or whatever IP)
```

**Network Connectivity Testing**

```bash
# Test from container
docker run --rm --network mynet \
  busybox \
  nslookup db

# Test connectivity
docker run --rm --network mynet \
  busybox \
  ping -c 3 app

# Full network inspection
docker network inspect mynet
# Shows all containers, their IPs, gateway
```

---

## 💾 SECTION 4: STORAGE & LAYER DRIVERS

### 🎯 Docker Layered Filesystem

**How Docker Stores Layers:**

```
┌─────────────────────────────────────┐
│ Container Layer (Writable)          │
│ (thin writable layer on top)        │
├─────────────────────────────────────┤
│ Image Layer N (Read-only)           │
│ (Application code)                  │
├─────────────────────────────────────┤
│ Image Layer N-1 (Read-only)         │
│ (Dependencies)                      │
├─────────────────────────────────────┤
│ Image Layer 1 (Read-only)           │
│ (Base OS)                           │
├─────────────────────────────────────┤
│ Storage Driver (manages layers)     │
└─────────────────────────────────────┘
```

**Copy-on-Write (CoW) Concept:**

```bash
# When container modifies file:
# 1. Looks for file in container layer
# 2. If not found, searches image layers
# 3. If found, copies to container layer (CoW)
# 4. Modifies copy in container layer
# 5. Original in image layer unchanged

# Benefits:
# - Multiple containers share image layers
# - Changes isolated per container
# - Efficient storage usage

# Downsides:
# - Performance overhead on first write
# - Large file modifications are expensive
```

### 📊 Storage Drivers

| Driver | Best For | Pros | Cons |
|--------|----------|------|------|
| **overlay2** | Most systems | Fast, stable | Needs Linux 4.0+ |
| **aufs** | Older systems | Good performance | Limited support |
| **devicemapper** | Enterprise | Snapshots available | Complex setup |
| **btrfs** | Advanced features | Compression, CoW | Kernel module |
| **zfs** | Data integrity | Deduplication | Extra setup |

**Check current driver:**
```bash
docker info | grep 'Storage Driver'
# Output: Storage Driver: overlay2

# Configure driver in daemon.json
{
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
```

**Advanced Storage Configuration:**

```bash
# View storage details
docker inspect mycontainer | grep -A 20 'GraphDriver'

# Storage location
docker info | grep 'Docker Root Dir'
# Usually: /var/lib/docker

# Check disk usage by layers
docker system df
# Shows images, containers, volumes disk usage

# By container
docker container inspect mycontainer | grep -i 'SizeRw\|SizeRootFs'
```

---

## 🐝 SECTION 5: DOCKER SWARM - ADVANCED ORCHESTRATION

### 🎯 Swarm Architecture

```
┌──────────────────────────────────────────────┐
│          DOCKER SWARM CLUSTER                │
├──────────────────────────────────────────────┤
│                                               │
│  Manager Nodes (3, 5, 7 for HA)              │
│  ├─ Raft consensus                           │
│  ├─ Service state management                 │
│  └─ Task scheduling                          │
│                                               │
│  Worker Nodes (scale to many)                │
│  ├─ Execute tasks                            │
│  ├─ Report health                            │
│  └─ No state management                      │
│                                               │
│  Overlay Network                             │
│  └─ Inter-node communication                 │
│                                               │
└──────────────────────────────────────────────┘
```

### 🚀 Swarm Advanced Commands

**1. Initialize & Join Swarm**

```bash
# Initialize manager node
docker swarm init --advertise-addr 192.168.1.100

# Get join tokens (needed by workers)
docker swarm join-token worker
# Output: docker swarm join --token <token> <manager-ip>:2377

docker swarm join-token manager
# For adding more managers

# Join as worker
docker swarm join --token <token> <manager-ip>:2377

# View swarm info
docker swarm info
docker node ls  # List all nodes
docker node inspect <node-id>
```

**2. Advanced Node Management**

```bash
# Promote/Demote nodes
docker node promote <node-id>   # Worker → Manager
docker node demote <node-id>    # Manager → Worker

# Drain node (move tasks elsewhere)
docker node update --availability drain <node-id>
# Useful for: maintenance, updates

# Restore node
docker node update --availability active <node-id>

# Remove node
docker node rm <node-id>         # Node must be drained first
docker node rm --force <node-id> # Force remove (dangerous)

# Add labels for scheduling
docker node update --label-add role=database <node-id>
docker node update --label-add zone=us-east <node-id>
```

**3. Advanced Service Deployment**

```bash
# Service with resource constraints
docker service create \
  --name myservice \
  --replicas 3 \
  --limit-cpu 0.5 \
  --limit-memory 512m \
  --reserve-cpu 0.25 \
  --reserve-memory 256m \
  nginx

# Service with placement constraints
docker service create \
  --name database \
  --constraint node.role==manager \
  --constraint node.labels.zone==us-east \
  postgres:15

# Service with update policy
docker service create \
  --name myapp \
  --update-delay 10s \
  --update-parallelism 2 \
  --update-failure-action pause \
  myapp:1.0

# Service with restart policy
docker service create \
  --name myapp \
  --restart-condition on-failure \
  --restart-delay 5s \
  --restart-max-attempts 3 \
  myapp:latest
```

**4. Rolling Updates**

```bash
# Update service image (rolling update)
docker service update \
  --image myapp:2.0 \
  --update-delay 10s \
  --update-parallelism 1 \
  myservice

# Monitor update progress
docker service ps myservice

# Rollback if needed
docker service rollback myservice
```

**5. Secrets & Configs (Sensitive Data)**

```bash
# Create secrets
echo "supersecret" | docker secret create db_password -
docker secret create api_key /path/to/api_key

# Create configs (non-sensitive)
docker config create app_config /path/to/config.json

# List secrets/configs
docker secret ls
docker config ls

# Use in service
docker service create \
  --name myapp \
  --secret db_password \
  --secret source=api_key,target=/run/secrets/key.txt \
  --config app_config \
  myapp:latest

# Inside container, access at /run/secrets/
docker exec myapp cat /run/secrets/db_password
```

**6. Service Networking**

```bash
# Services automatically on overlay network
docker network create \
  --driver overlay \
  --scope swarm \
  backend

docker service create \
  --network backend \
  --name db \
  postgres:15

docker service create \
  --network backend \
  --name api \
  myapp:latest

# Services discover each other via service name
# Load balancing automatic across replicas
```

---

## 🔒 SECTION 6: CONTAINER SECURITY (ADVANCED)

### 🎯 Linux Capabilities

**Understand Capabilities:**

```bash
# Capabilities are granular permissions
# Instead of running as root with all permissions,
# give specific capabilities

# List all capabilities
getcap $(which ping)

# Run container with capability
docker run --cap-add NET_PING \
  --cap-drop ALL \
  alpine ping 8.8.8.8

# Common capabilities:
# CAP_NET_BIND_SERVICE - Bind to ports < 1024
# CAP_NET_RAW - Raw socket access
# CAP_SYS_ADMIN - System admin functions
# CAP_SYS_PTRACE - Debug processes
```

**Best Practices:**

```bash
# Drop all, add only needed
docker run \
  --cap-drop=ALL \
  --cap-add=NET_BIND_SERVICE \
  nginx

# Never use
docker run --cap-add=ALL myapp  # DANGEROUS!
```

### 🔐 Seccomp (Secure Computing)

**Seccomp filters system calls:**

```bash
# Default seccomp profile (strict)
docker run nginx
# Filters dangerous syscalls

# Custom seccomp profile
docker run \
  --security-opt seccomp=/path/to/seccomp.json \
  myapp

# Disable seccomp (not recommended)
docker run \
  --security-opt seccomp=unconfined \
  myapp
```

**Example seccomp.json:**
```json
{
  "defaultAction": "SCMP_ACT_ERRNO",
  "defaultErrnoRet": 1,
  "archMap": [
    {
      "architecture": "SCMP_ARCH_X86_64",
      "subArchitectures": [
        "SCMP_ARCH_X86",
        "SCMP_ARCH_X32"
      ]
    }
  ],
  "syscalls": [
    {
      "names": ["read", "write", "open"],
      "action": "SCMP_ACT_ALLOW"
    }
  ]
}
```

### 📋 AppArmor & SELinux

```bash
# AppArmor (Ubuntu/Debian)
docker run \
  --security-opt apparmor=docker-default \
  myapp

# SELinux (CentOS/RHEL)
docker run \
  --security-opt label=type:svirt_apache_t \
  myapp

# Disable security module (not recommended)
docker run \
  --security-opt apparmor=unconfined \
  myapp
```

### ✍️ Image Signing (Docker Content Trust)

```bash
# Enable content trust
export DOCKER_CONTENT_TRUST=1

# Generate key
docker trust signer add --key ~/.docker/trust/my_key myuser myregistry/myimage

# Sign and push image
docker push myregistry/myimage:1.0
# Requires key passphrase

# Pull only signed images
export DOCKER_CONTENT_TRUST=1
docker pull myregistry/myimage:1.0
# Fails if image not signed

# View signatures
docker inspect --format='{{json .Signature}}' myregistry/myimage:1.0
```

### 🔍 Image Scanning (Vulnerability Detection)

```bash
# Using docker scan (integrated)
docker scan myapp:1.0

# With details
docker scan --severity high myapp:1.0

# Using Trivy (external scanner)
trivy image myapp:1.0
trivy image --severity HIGH,CRITICAL myapp:1.0

# Using Grype
grype myapp:1.0

# Automated scanning in CI/CD
# Fail build if critical vulnerabilities found
```

### 🚫 Security Best Practices

```dockerfile
# ✅ Run as non-root
RUN useradd -m appuser
USER appuser

# ✅ Read-only root filesystem
docker run --read-only myapp

# ✅ Temporary files in tmpfs
docker run --tmpfs /tmp:rw,size=100m myapp

# ✅ Resource limits
docker run -m 512m --cpus 1 myapp

# ✅ Drop capabilities
docker run --cap-drop ALL myapp

# ✅ SecurityContext in stack files
services:
  myapp:
    image: myapp:latest
    security_opt:
      - no-new-privileges:true
    cap_drop:
      - ALL
    read_only: true
```

---

## 📊 SECTION 7: LOGGING & MONITORING (ADVANCED)

### 🎯 Docker Logging Drivers

**Types of Logging Drivers:**

```bash
# Default: json-file
docker run --log-driver json-file myapp

# Syslog (Linux only)
docker run --log-driver syslog \
  --log-opt syslog-address=udp://localhost:514 \
  myapp

# AWS CloudWatch
docker run --log-driver awslogs \
  --log-opt awslogs-group=/ecs/myapp \
  --log-opt awslogs-region=us-east-1 \
  myapp

# Splunk
docker run --log-driver splunk \
  --log-opt splunk-token=<token> \
  --log-opt splunk-url=https://your-splunk:8088 \
  myapp

# Google Cloud Logging
docker run --log-driver gcplogs \
  --log-opt gcp-project=myproject \
  --log-opt gcp-log-cmd=true \
  myapp

# ELK Stack (Elasticsearch, Logstash, Kibana)
docker run --log-driver json-file \
  --log-opt max-size=10m \
  --log-opt max-file=3 \
  myapp

# Datadog
docker run --log-driver datadog \
  --log-opt dd_service=myapp \
  --log-opt dd_source=myapp-source \
  myapp
```

**Logging Configuration in daemon.json:**

```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3",
    "labels": "app_name,env"
  }
}
```

### 📈 Monitoring with Prometheus

**Setup Prometheus monitoring:**

```bash
# Install Prometheus
docker run -d \
  -p 9090:9090 \
  -v /path/to/prometheus.yml:/etc/prometheus/prometheus.yml \
  prom/prometheus

# prometheus.yml configuration
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: 'docker'
    static_configs:
      - targets: ['localhost:9323']

# Enable Docker metrics endpoint
# In daemon.json:
{
  "metrics-addr": "127.0.0.1:9323",
  "experimental": true
}

# Query Docker metrics
curl http://localhost:9323/metrics
```

**Key Docker Metrics:**

```
# Container CPU usage
container_cpu_usage_seconds_total

# Container memory usage
container_memory_usage_bytes

# Container network I/O
container_network_receive_bytes_total
container_network_transmit_bytes_total

# Image build duration
engine_builder_builds_duration_seconds

# Swarm manager raft metrics
engine_swarm_raft_term
engine_swarm_raft_commit_index
```

### 🎯 Grafana Dashboards

```bash
# Run Grafana
docker run -d \
  -p 3000:3000 \
  -e GF_SECURITY_ADMIN_PASSWORD=admin \
  grafana/grafana

# Connect to Prometheus
# Add Prometheus as datasource
# URL: http://prometheus:9090

# Import Docker dashboard
# https://grafana.com/grafana/dashboards/893
```

### 🔍 Centralized Logging (ELK Stack)

```yaml
version: '3.8'

services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    environment:
      - discovery.type=single-node
    ports:
      - "9200:9200"

  logstash:
    image: docker.elastic.co/logstash/logstash:7.14.0
    volumes:
      - ./logstash.conf:/usr/share/logstash/pipeline/logstash.conf
    ports:
      - "5000:5000"

  kibana:
    image: docker.elastic.co/kibana/kibana:7.14.0
    ports:
      - "5601:5601"

  myapp:
    image: myapp:latest
    logging:
      driver: logstash
      options:
        logstash-address: localhost:5000
        tag: myapp
```

---

## 🏠 SECTION 8: REGISTRY MANAGEMENT (ADVANCED)

### 🎯 Private Registry Setup

**1. Simple Private Registry:**

```bash
# Run registry container
docker run -d \
  -p 5000:5000 \
  --name registry \
  -v registry-data:/var/lib/registry \
  registry:2.8

# Tag and push image
docker tag myapp:1.0 localhost:5000/myapp:1.0
docker push localhost:5000/myapp:1.0

# Pull from private registry
docker pull localhost:5000/myapp:1.0

# View images in registry
curl -X GET http://localhost:5000/v2/_catalog
curl -X GET http://localhost:5000/v2/myapp/tags/list
```

**2. Secure Registry (HTTPS & Authentication)**

```bash
# Generate SSL certificate
openssl req -newkey rsa:4096 -nodes -sha256 \
  -keyout certs/domain.key \
  -x509 -days 365 \
  -out certs/domain.crt

# Create htpasswd file
htpasswd -Bc auth/htpasswd myuser
# Enter password when prompted

# Run secure registry
docker run -d \
  -p 443:5000 \
  --name registry \
  -v registry-data:/var/lib/registry \
  -v $(pwd)/certs:/certs \
  -v $(pwd)/auth:/auth \
  -e REGISTRY_HTTP_ADDR=0.0.0.0:5000 \
  -e REGISTRY_HTTP_TLS_CERTIFICATE=/certs/domain.crt \
  -e REGISTRY_HTTP_TLS_KEY=/certs/domain.key \
  -e REGISTRY_AUTH=htpasswd \
  -e REGISTRY_AUTH_HTPASSWD_REALM="Registry Realm" \
  -e REGISTRY_AUTH_HTPASSWD_PATH=/auth/htpasswd \
  registry:2.8

# Login and push
docker login https://myregistry.com
docker tag myapp:1.0 myregistry.com/myapp:1.0
docker push myregistry.com/myapp:1.0
```

**3. Harbor (Enterprise Registry)**

```bash
# Harbor with Docker Compose
curl https://github.com/goharbor/harbor/releases/download/v2.6.0/harbor-docker-compose.tgz | tar xz
cd harbor
cp harbor.yml.tmpl harbor.yml
# Edit harbor.yml (hostname, ports, etc.)
docker-compose up -d

# Access on: https://hostname

# Registry features:
# - User authentication
# - RBAC (Role-based access)
# - Image scanning
# - Replication
# - Webhooks
```

**4. Artifactory (JFrog)**

```bash
# Enterprise registry alternative
# docker-compose setup available

# Features:
# - Multiple registries (Docker, npm, PyPI, etc.)
# - Advanced security
# - Artifact management
# - Compliance features
```

### 📊 Registry Monitoring

```bash
# Registry API endpoints
curl http://localhost:5000/v2/_catalog          # List repos
curl http://localhost:5000/v2/<name>/tags/list  # List tags
curl http://localhost:5000/v2/<name>/manifests/<tag>  # Get manifest

# Get image details
curl http://localhost:5000/v2/myapp/manifests/1.0 \
  -H "Accept: application/vnd.docker.distribution.manifest.v2+json"

# Cleanup dangling images
docker image prune
docker image prune -a

# Registry garbage collection
docker run --rm -v registry-data:/var/lib/registry \
  registry:2.8 \
  registry garbage-collect /etc/docker/registry/config.yml
```

---

## 🔌 SECTION 9: DOCKER API & PROGRAMMATIC CONTROL

### 🎯 Docker Remote API

**Enable Remote API:**

```bash
# Daemon configuration
{
  "hosts": [
    "unix:///var/run/docker.sock",
    "tcp://127.0.0.1:2375",
    "tcp://192.168.1.100:2375"
  ]
}

# Access API
curl http://localhost:2375/v1.41/containers/json
curl http://localhost:2375/v1.41/images/json
```

### 🐍 Docker SDK (Python)

**Installation:**
```bash
pip install docker
```

**Basic Usage:**

```python
import docker

# Connect to Docker daemon
client = docker.from_env()

# Pull image
client.images.pull('nginx:latest')

# List images
for image in client.images.list():
    print(f"{image.tags} - {image.short_id}")

# Run container
container = client.containers.run(
    'nginx:latest',
    name='my-web',
    ports={'80/tcp': 8080},
    detach=True
)
print(f"Container {container.short_id} running")

# List containers
for cont in client.containers.list():
    print(f"{cont.name} - {cont.status}")

# Execute command
result = container.exec_run('ls -la /var/www/html')
print(result.output.decode())

# Get logs
logs = container.logs()
print(logs.decode())

# Stop/Remove
container.stop()
container.remove()
```

**Advanced Docker SDK:**

```python
import docker

client = docker.from_env()

# Build image
image, logs = client.images.build(
    path='/path/to/dockerfile',
    tag='myapp:1.0'
)
for log in logs:
    print(log)

# Create volume
volume = client.volumes.create(name='mydata')

# Create network
network = client.networks.create('mynet', driver='bridge')

# Run with volume and network
container = client.containers.run(
    'postgres:15',
    name='db',
    volumes={'mydata': {'bind': '/var/lib/postgresql/data'}},
    network='mynet',
    environment=['POSTGRES_PASSWORD=secret'],
    detach=True
)

# Connect another container
app = client.containers.run(
    'myapp:latest',
    name='app',
    network='mynet',
    detach=True
)

# Inspect container
inspect = container.attrs
print(f"IP: {inspect['NetworkSettings']['IPAddress']}")

# Container stats
stats = container.stats(stream=False)
print(f"CPU: {stats['cpu_stats']['cpu_usage']['total_usage']}")
print(f"Memory: {stats['memory_stats']['usage']}")

# Events (subscribe to container events)
for event in client.events(
    filters={'type': 'container'},
    decode=True
):
    if event['Action'] == 'start':
        print(f"Container {event['Actor']['Attributes']['name']} started")
```

### 🟢 Go Client Example

```go
package main

import (
	"context"
	"fmt"
	"github.com/docker/docker/api/types"
	"github.com/docker/docker/api/types/container"
	"github.com/docker/docker/client"
	"io"
	"os"
)

func main() {
	ctx := context.Background()
	cli, err := client.NewClientWithOpts(client.FromEnv, client.WithAPIVersionNegotiation())
	if err != nil {
		panic(err)
	}
	defer cli.Close()

	// Pull image
	out, err := cli.ImagePull(ctx, "docker.io/library/nginx:latest", types.ImagePullOptions{})
	if err != nil {
		panic(err)
	}
	io.Copy(os.Stdout, out)

	// Create container
	resp, err := cli.ContainerCreate(ctx, &container.Config{
		Image: "nginx:latest",
		ExposedPorts: map[string]struct{}{"80/tcp": {}},
	}, nil, nil, nil, "my-nginx")
	if err != nil {
		panic(err)
	}

	// Start container
	if err := cli.ContainerStart(ctx, resp.ID, types.ContainerStartOptions{}); err != nil {
		panic(err)
	}

	fmt.Printf("Container %s started\n", resp.ID)

	// Get container info
	inspect, _ := cli.ContainerInspect(ctx, resp.ID)
	fmt.Printf("Status: %s\n", inspect.State.Status)
}
```

---

## ⚡ SECTION 10: PERFORMANCE TUNING & OPTIMIZATION

### 🎯 Container Performance

**Memory Management:**

```bash
# Set memory limit
docker run -m 512m myapp       # Hard limit
docker run -m 512m --memory-swap 1g myapp  # Swap limit

# Memory reservation (soft limit)
docker run --memory-reservation 256m myapp

# Out of memory behavior
docker run -m 512m --oom-kill-disable myapp  # Don't kill container

# View memory stats
docker stats myapp --no-stream
docker exec myapp free -h      # Inside container
```

**CPU Management:**

```bash
# CPU cores
docker run --cpus 1.5 myapp    # 1.5 CPU cores

# CPU shares (relative weight)
docker run --cpu-shares 1024 myapp

# CPU set (specific cores)
docker run --cpuset-cpus 0,1 myapp  # Use core 0 and 1

# CPU period and quota
docker run --cpu-period 100000 --cpu-quota 50000 myapp  # 50% CPU
```

**I/O Performance:**

```bash
# Disk write throughput limit
docker run --device-write-bps /dev/sda:1mb myapp

# Disk read throughput limit
docker run --device-read-bps /dev/sda:1mb myapp

# IOPS (I/O operations per second)
docker run --device-write-iops /dev/sda:100 myapp
```

### 📊 Benchmarking

```bash
# Time command execution
docker run --rm alpine time sleep 1

# Measure I/O performance
docker run --rm alpine dd if=/dev/zero of=/tmp/test bs=1M count=100

# CPU benchmark
docker run --rm alpine stress --cpu 1 --timeout 10s

# Memory benchmark
docker run --rm alpine stress --vm 1 --vm-bytes 256M --timeout 10s

# Full system benchmark
docker run --rm alpine \
  stress --cpu 2 --vm 2 --vm-bytes 256M --timeout 30s
```

### 🚀 Build Performance

```bash
# Parallel builds with BuildKit
DOCKER_BUILDKIT=1 docker build -t myapp:1.0 .

# With inline caching
DOCKER_BUILDKIT=1 docker build \
  --build-arg BUILDKIT_INLINE_CACHE=1 \
  -t myapp:1.0 .

# Build time analysis
DOCKER_BUILDKIT=1 BUILDKIT_PROGRESS=plain docker build \
  -t myapp:1.0 . 2>&1 | tee build.log

# Analyze slow steps
grep "DONE\|ERROR" build.log | tail -20
```

### 🔍 Runtime Profiling

```bash
# CPU profiling
docker run --cpuprofile=/tmp/cpu.prof myapp

# Memory profiling
docker run --memprofile=/tmp/mem.prof myapp

# Generate reports
go tool pprof /tmp/cpu.prof
go tool pprof /tmp/mem.prof

# Live profiling
curl http://localhost:6060/debug/pprof/heap > heap.prof
```

---

## 🐛 SECTION 11: DEEP DEBUGGING & INSPECTION

### 🎯 Advanced Container Inspection

```bash
# Complete configuration
docker inspect mycontainer | jq '.'

# Specific field extraction
docker inspect mycontainer | jq '.[0].State'
docker inspect mycontainer | jq '.[0].NetworkSettings'
docker inspect mycontainer | jq '.[0].Mounts'

# Format custom output
docker inspect mycontainer --format='{{.State.Pid}}'
docker inspect mycontainer --format='{{.NetworkSettings.IPAddress}}'
docker inspect mycontainer --format='{{range .Mounts}}{{.Source}}:{{.Destination}} {{end}}'
```

### 🔍 Detailed Logging

```bash
# Enable daemon debug
dockerd -D 2>&1 | tee docker.log

# Docker CLI debug
DOCKER_BUILDKIT=0 docker -D build -t myapp:1.0 .

# Container logs with timestamps
docker logs --timestamps mycontainer

# Stream logs continuously
docker logs -f --timestamps mycontainer

# Since/Until specific times
docker logs --since 2h mycontainer
docker logs --until 1h mycontainer

# Tail specific number of lines
docker logs --tail 1000 mycontainer
```

### 📊 System Events

```bash
# Real-time events
docker events

# Filter events
docker events --filter 'type=container'
docker events --filter 'event=start'
docker events --filter 'container=mycontainer'

# Formatted output
docker events --filter 'type=container' \
  --format '{{.Type}}: {{.Action}} {{.Actor.ID}}'

# Save events to file
docker events > events.log &
```

### 🔧 Container Internals

```bash
# Check PID
docker inspect mycontainer --format='{{.State.Pid}}'

# See processes from host
ps aux | grep <PID>

# Container networking info
docker exec mycontainer ip addr show
docker exec mycontainer ip route show
docker exec mycontainer netstat -tlnp

# Check mounted volumes
docker inspect mycontainer | jq '.[0].Mounts'

# View environment variables
docker exec mycontainer env

# Check cgroup limits
docker exec mycontainer cat /sys/fs/cgroup/memory/memory.limit_in_bytes
docker exec mycontainer cat /sys/fs/cgroup/cpuset/cpuset.cpus
```

### 🎯 nsenter (Advanced)

```bash
# Enter container namespace without exec
PID=$(docker inspect mycontainer --format='{{.State.Pid}}')
nsenter -t $PID -n ip addr show

# Mount filesystem
nsenter -t $PID -m ls /

# All namespaces
nsenter -t $PID bash
# Now inside container at system level
```

---

## 📝 SECTION 12: CUSTOM NETWORKS & PLUGINS

### 🎯 Custom Network Drivers

```bash
# Create with custom options
docker network create \
  -d bridge \
  --opt "com.docker.network.driver.mtu=1400" \
  --opt "com.docker.network.bridge.name=br0" \
  mynet

# Inspect network details
docker network inspect mynet | jq '.[0]'

# Get network config
docker network inspect mynet | \
  jq '.[0] | {Name, Driver, Subnet: .IPAM.Config[0].Subnet, Gateway: .IPAM.Config[0].Gateway}'
```

### 🔌 Third-Party Network Plugins

**Weave Network:**
```bash
# Install plugin
docker plugin install weaveworks/net-plugin:latest_release

# Create network with Weave
docker network create -d weave mynet

# Features:
# - Multi-host networking
# - Encryption
# - Service discovery
```

**Flannel:**
```bash
# Used in Kubernetes
# Provides networking across cluster

# Simple flat network model
# Good for container-to-container communication
```

**Calico:**
```bash
# Policy-based networking
# Advanced network policies

# Setup
docker network create -d calico mynet

# Features:
# - Network policies
# - BGP routing
# - Performance
```

---

## 🔄 SECTION 13: IMAGE OPTIMIZATION TECHNIQUES

### 🎯 Reduce Image Size

**Strategy 1: Multi-stage builds**

```dockerfile
# Before: 800MB
FROM ubuntu:22.04
COPY . .
RUN apt-get update && apt-get install -y build-essential \
    && gcc mycode.c -o myapp \
    && rm -rf /var/lib/apt/lists/*
CMD ["./myapp"]

# After: 50MB
FROM ubuntu:22.04 as builder
RUN apt-get update && apt-get install -y build-essential
COPY . .
RUN gcc mycode.c -o myapp

FROM ubuntu:22.04
COPY --from=builder /myapp .
CMD ["./myapp"]
```

**Strategy 2: Use Alpine**

```dockerfile
# Before: 900MB
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y python3 pip
COPY . .
CMD ["python3", "app.py"]

# After: 100MB
FROM python:3.11-alpine
COPY . .
CMD ["python", "app.py"]
```

**Strategy 3: Minimal base images**

```dockerfile
# Start minimal
FROM scratch
# Just add what you need

# Or use distroless
FROM gcr.io/distroless/python3-debian11
COPY --from=builder /app /app
CMD ["app.py"]
```

**Strategy 4: Layer ordering**

```dockerfile
# ✅ Good order (most stable to most volatile)
FROM python:3.11-slim
RUN apt-get update && apt-get install -y curl  # Stable
COPY requirements.txt .                         # Stable
RUN pip install -r requirements.txt              # Stable
COPY . .                                         # Volatile
CMD ["python", "app.py"]

# ✅ Size optimization
RUN apt-get update && apt-get install -y curl \
    && rm -rf /var/lib/apt/lists/*  # Clean immediately
```

### 📊 Analyze Image Layers

```bash
# View all layers
docker history myapp:1.0

# View layer details
docker inspect myapp:1.0 | jq '.[0].RootFS.Layers'

# Layer sizes
docker history --human myapp:1.0 --no-trunc

# Using dive (tool to analyze)
dive myapp:1.0
# Shows layer-by-layer breakdown
# Highlights wasted space

# Using imagelayers.io
# Web tool to visualize layers
```

---

## 🔄 SECTION 14: CI/CD INTEGRATION

### 🎯 Jenkins Pipeline

```groovy
pipeline {
    agent any
    
    options {
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
    }
    
    stages {
        stage('Build Image') {
            steps {
                sh 'docker build -t myapp:${BUILD_NUMBER} .'
            }
        }
        
        stage('Scan Image') {
            steps {
                sh 'docker scan myapp:${BUILD_NUMBER}'
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    docker run --rm myapp:${BUILD_NUMBER} \
                        pytest tests/
                '''
            }
        }
        
        stage('Push to Registry') {
            steps {
                sh '''
                    docker tag myapp:${BUILD_NUMBER} \
                        myregistry.com/myapp:${BUILD_NUMBER}
                    docker tag myapp:${BUILD_NUMBER} \
                        myregistry.com/myapp:latest
                    docker push myregistry.com/myapp:${BUILD_NUMBER}
                    docker push myregistry.com/myapp:latest
                '''
            }
        }
        
        stage('Deploy') {
            steps {
                sh '''
                    docker service update \
                        --image myregistry.com/myapp:${BUILD_NUMBER} \
                        myapp
                '''
            }
        }
    }
    
    post {
        always {
            sh 'docker rmi myapp:${BUILD_NUMBER} || true'
        }
    }
}
```

### 🎯 GitHub Actions

```yaml
name: Docker CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Build image
        run: docker build -t myapp:${{ github.sha }} .
      
      - name: Scan image
        run: docker scan myapp:${{ github.sha }}
      
      - name: Run tests
        run: docker run --rm myapp:${{ github.sha }} pytest tests/
      
      - name: Login to registry
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      
      - name: Push image
        run: |
          docker tag myapp:${{ github.sha }} \
            ${{ secrets.DOCKER_USERNAME }}/myapp:${{ github.sha }}
          docker push \
            ${{ secrets.DOCKER_USERNAME }}/myapp:${{ github.sha }}
      
      - name: Deploy
        run: |
          # Trigger deployment
          curl -X POST https://deploy-server/deploy \
            -H "Authorization: Bearer ${{ secrets.DEPLOY_TOKEN }}" \
            -d "version=${{ github.sha }}"
```

### 🎯 GitLab CI

```yaml
stages:
  - build
  - test
  - scan
  - push
  - deploy

variables:
  REGISTRY_IMAGE: $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA

build:
  stage: build
  script:
    - docker build -t $REGISTRY_IMAGE .

test:
  stage: test
  script:
    - docker run --rm $REGISTRY_IMAGE pytest tests/

scan:
  stage: scan
  script:
    - docker scan $REGISTRY_IMAGE

push:
  stage: push
  script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
    - docker push $REGISTRY_IMAGE

deploy:
  stage: deploy
  script:
    - docker pull $REGISTRY_IMAGE
    - docker service update --image $REGISTRY_IMAGE myapp
  only:
    - main
```

---

## 🔥 SECTION 15: HIGH AVAILABILITY & BACKUP

### 🎯 High Availability Setup

**Multi-Manager Swarm:**

```bash
# Initialize first manager
docker swarm init --advertise-addr 192.168.1.100

# Add more managers (3, 5, or 7 for HA)
# Get manager token
MANAGER_TOKEN=$(docker swarm join-token -q manager)

# On other nodes
docker swarm join --token $MANAGER_TOKEN 192.168.1.100:2377

# Verify HA
docker node ls
# Should see multiple managers

# Deploy service with replicas across nodes
docker service create \
  --name myapp \
  --replicas 3 \
  --mode global \
  myapp:latest
```

**Load Balancing:**

```bash
# Service automatic load balancing
docker service create \
  --name web \
  --replicas 3 \
  -p 8080:80 \
  nginx

# Access any node on port 8080
# Automatically load balanced to replica

# ingress network handles load balancing
docker network inspect ingress
```

### 💾 Backup Strategies

**Volume Backup:**

```bash
# Backup while running (using volume mount)
docker run --rm \
  -v production_db:/source \
  -v $(pwd)/backups:/backup \
  ubuntu \
  tar czf /backup/db-$(date +%Y%m%d).tar.gz -C /source .

# Scheduled backup (cron)
0 2 * * * docker run --rm \
  -v production_db:/source \
  -v /backups:/backup \
  ubuntu \
  tar czf /backup/db-$(date +\%Y\%m\%d).tar.gz -C /source .
```

**Database Backup:**

```bash
# PostgreSQL
docker exec mydb pg_dump \
  -U postgres \
  -d mydb \
  -Fc > backup.dump

# MySQL
docker exec mydb mysqldump \
  -u root \
  -psecret \
  mydb > backup.sql

# MongoDB
docker exec mydb mongodump \
  --archive=/backup/mongodb.archive
```

**Restore from Backup:**

```bash
# PostgreSQL restore
docker exec mydb pg_restore \
  -U postgres \
  -d mydb \
  /backup/backup.dump

# MySQL restore
docker exec mydb mysql \
  -u root \
  -psecret \
  mydb < backup.sql

# MongoDB restore
docker exec mydb mongorestore \
  --archive=/backup/mongodb.archive
```

---

## 🔧 SECTION 16: ADVANCED COMPOSE FEATURES

### 🎯 Compose Overrides

```bash
# Base compose file
cat docker-compose.yml

# Override file (local development)
cat docker-compose.override.yml

# Deploy specific override
docker-compose -f docker-compose.yml \
  -f docker-compose.prod.yml \
  up

# Different environments
docker-compose -f docker-compose.yml \
  -f docker-compose.staging.yml \
  up
```

**Example Override Structure:**

```yaml
# docker-compose.yml (base)
version: '3.8'
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
  app:
    image: myapp:latest
    depends_on:
      - db

# docker-compose.override.yml (development)
version: '3.8'
services:
  db:
    volumes:
      - ./db-data:/var/lib/postgresql/data
    ports:
      - "5432:5432"
  app:
    volumes:
      - ./app:/app
    environment:
      DEBUG: "true"
    ports:
      - "8000:8000"

# docker-compose.prod.yml (production)
version: '3.8'
services:
  db:
    restart: always
    environment:
      POSTGRES_PASSWORD_FILE: /run/secrets/db_password
    secrets:
      - db_password
  app:
    restart: always
    replicas: 3
    
secrets:
  db_password:
    file: /etc/secrets/db_password
```

### 🔄 Variable Interpolation

```yaml
# Use environment variables in compose
version: '3.8'

services:
  app:
    image: myapp:${APP_VERSION:-latest}
    ports:
      - "${PORT:-8000}:8000"
    environment:
      DEBUG: ${DEBUG:-false}
      LOG_LEVEL: ${LOG_LEVEL:-info}
    deploy:
      resources:
        limits:
          cpus: '${CPU_LIMIT:-1}'
          memory: ${MEMORY_LIMIT:-512m}

# Run with variables
APP_VERSION=1.0 PORT=9000 docker-compose up

# Or load from .env file
cat .env
APP_VERSION=1.0
PORT=9000
CPU_LIMIT=2
```

### 🚀 Advanced Compose Features

**Service dependencies:**

```yaml
version: '3.8'

services:
  db:
    image: postgres:15
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5

  app:
    image: myapp:latest
    depends_on:
      db:
        condition: service_healthy  # Wait for health check
```

**Conditionals and extensions:**

```yaml
version: '3.8'

x-common-variables: &common-variables
  LOG_LEVEL: info
  ENVIRONMENT: production

services:
  web:
    image: nginx:latest
    environment:
      <<: *common-variables  # Merge common vars

  app:
    image: myapp:latest
    environment:
      <<: *common-variables
      APP_NAME: myapp
```

---

## 🎓 ADVANCED LEARNING RESOURCES

### 📚 Deep Dive Topics

```
1. Container Networking
   - CNM (Container Network Model)
   - IPAM (IP Address Management)
   - Service Discovery
   - Load Balancing

2. Storage
   - Storage drivers comparison
   - Volume drivers
   - Distributed storage (ceph, glusterfs)
   - Database optimization

3. Orchestration
   - Swarm deep dive
   - Kubernetes fundamentals
   - Service mesh (Istio, Linkerd)
   - GitOps (ArgoCD, Flux)

4. Security
   - Image signing
   - Runtime security
   - Secrets management (Vault)
   - Compliance (PCI-DSS, HIPAA)

5. Performance
   - Kernel tuning
   - Network optimization
   - Storage optimization
   - Memory management

6. Monitoring
   - Prometheus advanced
   - Distributed tracing
   - Custom metrics
   - Log aggregation

7. CI/CD
   - Multi-stage builds
   - Test automation
   - Artifact management
   - Deployment strategies
```

### 🔗 Advanced Tools

```bash
# Image analysis
dive - layer analyzer
trivy - vulnerability scanner
grype - vulnerability database
clair - image scanning

# Networking
netshoot - container networking
cilium - eBPF networking

# Monitoring
prometheus - metrics collection
grafana - visualization
jaeger - distributed tracing
loki - log aggregation

# Security
falco - runtime security
sysdig - system inspection
twistlock - container security

# Orchestration
kubernetes - container orchestration
docker swarm - docker native
docker stack - compose production
```

---

## ✨ SUMMARY: ADVANCED TOPICS

You now understand:

✅ **Multi-stage builds** for optimization  
✅ **Docker BuildKit** for advanced builds  
✅ **Advanced networking** (overlay, macvlan, etc.)  
✅ **Storage drivers** and layer management  
✅ **Docker Swarm** orchestration  
✅ **Container security** (capabilities, seccomp)  
✅ **Logging & monitoring** at enterprise scale  
✅ **Registry management** (private, secure)  
✅ **Docker API** for programmatic control  
✅ **Performance tuning** techniques  
✅ **Deep debugging** methods  
✅ **Custom networks** and plugins  
✅ **Image optimization** strategies  
✅ **CI/CD integration** (Jenkins, GitHub Actions)  
✅ **HA and backup** strategies  
✅ **Advanced Compose** features  

---

**Ready for Enterprise Docker Deployment! 🚀**

*These advanced topics prepare you for production-grade containerization and orchestration.*
