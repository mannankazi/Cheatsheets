# 🚀 JENKINS CHEATSHEET - Easy Read Edition
**From Zero to Hero | Beginner to Advanced++**

---

## 📑 QUICK NAVIGATION

| 🔰 **BEGINNER** | 📚 **INTERMEDIATE** | 🎯 **ADVANCED** | 🏆 **EXPERT** |
|---|---|---|---|
| What is Jenkins? | Pipelines | CLI Commands | Groovy DSL |
| Installation | Environment Vars | Credentials | JCasC |
| Dashboard Basics | Parameters | Error Handling | REST API |
| Architecture | Build Triggers | Parallel Builds | Job DSL |
| Job Types | Agents | Shared Libraries | Performance |
| Freestyle Jobs | Blue Ocean | Security | Monitoring |

---

# 🔰 PART 1: BEGINNER FUNDAMENTALS

## 1️⃣ What is Jenkins?

```
┌─────────────────────────────────────────────┐
│         JENKINS = CI/CD AUTOMATION          │
├─────────────────────────────────────────────┤
│  ✅ Automate build/test/deploy              │
│  ✅ Trigger on code commit                  │
│  ✅ Run tests automatically                 │
│  ✅ Deploy to production                    │
│  ✅ Scale across multiple servers           │
└─────────────────────────────────────────────┘
```

### 🎯 Core Concepts Simplified

| Concept | What It Does | Example |
|---------|-------------|---------|
| **Job** | A task Jenkins runs | Build Java app |
| **Build** | One execution of job | Build #42 |
| **Pipeline** | Steps to complete | Build → Test → Deploy |
| **Stage** | Logical grouping | Build stage = compile + package |
| **Trigger** | What starts build | Code commit, schedule |
| **Agent** | Computer that runs it | Linux server, Mac, Windows |

---

## 2️⃣ Installation in 3 Steps

### 🐧 Linux (Ubuntu/Debian)

```bash
# Step 1: Add Jenkins repository
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Step 2: Install
sudo apt-get update
sudo apt-get install jenkins

# Step 3: Start
sudo systemctl start jenkins
sudo systemctl enable jenkins  # Auto-start on reboot

# 🔑 Get password:
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 🐳 Docker (Easiest)

```bash
docker run -d -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  --name jenkins \
  jenkins/jenkins:lts

# 🔑 Get password:
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```

### 🪟 Windows

1. Download: https://www.jenkins.io/download/
2. Run installer
3. Follow setup wizard
4. Visit: http://localhost:8080

---

## 3️⃣ Jenkins Dashboard - First Look

```
                    🏠 JENKINS HOME
        ┌──────────────────────────────────────┐
        │  Jenkins Logo  🔍 Search  👤 Admin   │  ← Top Bar
        ├──────────────────────────────────────┤
        │ 📌 New Item                          │
        │ 📌 Manage Jenkins                    │  ← Left Sidebar
        │ 📌 My Views                          │
        │                                      │
        ├──────────────────────────────────────┤
        │  Jobs List                           │
        │  ┌──────────────────────────────────┐│
        │  │ ✅ my-app-build     #42           ││  ← Job Status
        │  │    5 days ago                    ││
        │  │ ⚠️  my-app-test     #41           ││  (✅ success, ❌ fail,
        │  │    last failed 2025-01-15        ││   ⚠️ unstable)
        │  │ ❌ my-app-deploy    #40           ││
        │  │    2025-01-14                    ││
        │  └──────────────────────────────────┘│
        └──────────────────────────────────────┘
```

### 🗺️ Dashboard Navigation Map

```
Dashboard
├─ 🔨 New Item
│  └─ Create Freestyle / Pipeline / Multibranch
├─ ⚙️  Manage Jenkins
│  ├─ Configure System (global settings)
│  ├─ Manage Plugins (install plugins)
│  ├─ Manage Credentials (store secrets)
│  ├─ Manage Users (authentication)
│  └─ System Log (debugging)
├─ 👁️ My Views (custom job collections)
└─ 📋 Job List (all jobs)
```

---

## 4️⃣ Jenkins Architecture - Simplified

### 🖼️ Master-Agent Architecture

```
                    INTERNET
                       │
                       ▼
        ┌──────────────────────────┐
        │   JENKINS MASTER         │  ◄── Main Server
        │  (Orchestrator)          │     • Web UI (port 8080)
        │  • Schedules jobs        │     • Manages plugins
        │  • Stores configurations │     • Handles webhooks
        │  • Shows logs            │     • Security
        └──────────────────────────┘
                    │
         ┌──────────┼──────────┬──────────┐
         │          │          │          │
         ▼          ▼          ▼          ▼
    ┌────────┐ ┌────────┐ ┌────────┐ ┌────────┐
    │Agent 1 │ │Agent 2 │ │Agent 3 │ │Agent 4 │  ◄── Build Servers
    │Linux   │ │Windows │ │Mac     │ │Docker  │
    │Build   │ │Test    │ │Deploy  │ │CI      │
    └────────┘ └────────┘ └────────┘ └────────┘
        4 Jobs  4 Jobs   4 Jobs    4 Jobs = 16 Parallel Builds!
```

**Why Agents?** ✨
- 🚀 Run builds in parallel
- 🖥️ Use different operating systems
- 🌍 Distribute load
- 🔒 Isolate environments

---

# 📚 PART 2: INTERMEDIATE CORE CONCEPTS

## 5️⃣ Job Types Comparison

```
┌─────────────────────────────────────────────────────────┐
│                    JOB TYPES                            │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  🎯 FREESTYLE (Simple)                                 │
│  ├─ GUI-based configuration                           │
│  ├─ Good for: Learning, simple tasks                  │
│  ├─ NOT version controlled                            │
│  └─ Example: Compile & test                           │
│                                                         │
│  📦 PIPELINE (Recommended)                             │
│  ├─ Code-based (Jenkinsfile)                          │
│  ├─ Version controlled                                │
│  ├─ Complex workflows                                 │
│  └─ Example: Build → Test → Deploy stages             │
│                                                         │
│  🌿 MULTIBRANCH (Git-aware)                           │
│  ├─ Auto-scans repository branches                    │
│  ├─ One pipeline per branch                           │
│  ├─ PR support                                        │
│  └─ Example: main, develop, feature-* branches        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**When to Use?**

| Scenario | Use |
|----------|-----|
| First time with Jenkins | 🎯 Freestyle |
| Production pipelines | 📦 Pipeline |
| Multiple branches | 🌿 Multibranch |
| Quick testing | 🎯 Freestyle |

---

## 6️⃣ Build Triggers Explained

### 🎚️ What Triggers Builds?

```
┌─────────────────────────────────────────┐
│         WHAT STARTS A BUILD?            │
├─────────────────────────────────────────┤
│                                         │
│  👤 Manual        Build Now button      │
│                                         │
│  ⏰ Scheduled      Every day at 2 AM    │
│                                         │
│  🔔 Poll SCM      Check Git every 15m  │
│                                         │
│  🪝 Webhook       GitHub push = build  │
│                                         │
│  ⬆️  Upstream     Other job finished   │
│                                         │
└─────────────────────────────────────────┘
```

### 📊 Trigger Comparison

| Trigger | Speed | Setup | Cost |
|---------|-------|-------|------|
| Manual | Slow | Easy | Free |
| Scheduled | Slow | Easy | Free |
| Poll SCM | ~5min delay | Easy | Server load |
| **Webhook** | **Instant** | **Hard** | **Best** |
| Upstream | Instant | Easy | Free |

**⚡ Best Practice**: Use **Webhook** for real-time builds!

---

## 7️⃣ Pipeline Basics - Build, Test, Deploy

### 📝 Simple Pipeline Example

```groovy
pipeline {                           // 🏗️ Start pipeline
    agent any                        // 💻 Run on any agent
    
    stages {                         // 🎬 Define stages
        stage('Build') {             // Stage 1
            steps {
                echo '🔨 Compiling...'
                sh 'mvn compile'
            }
        }
        
        stage('Test') {              // Stage 2
            steps {
                echo '✅ Testing...'
                sh 'mvn test'
            }
        }
        
        stage('Deploy') {            // Stage 3
            steps {
                echo '🚀 Deploying...'
                sh './deploy.sh'
            }
        }
    }
    
    post {                           // 📬 After pipeline
        always {
            echo '✋ Cleanup'
        }
        success {
            echo '🎉 All good!'
        }
        failure {
            echo '❌ Something failed!'
        }
    }
}
```

### 🎨 Pipeline Visualization

```
   START
     │
     ▼
  ┌─────────────┐
  │   🔨 BUILD   │  (Compile code)
  └─────────────┘
     │
     ▼ (if success)
  ┌─────────────┐
  │    ✅ TEST    │  (Run tests)
  └─────────────┘
     │
     ▼ (if success)
  ┌─────────────┐
  │   🚀 DEPLOY   │  (Push to server)
  └─────────────┘
     │
     ▼
   ✅ SUCCESS
   or
   ❌ FAILED (at any stage)
```

---

## 8️⃣ Environment Variables - Available Values

### 📋 Common Built-in Variables

| Variable | Example Value | Use Case |
|----------|---------------|----------|
| `BUILD_NUMBER` | `42` | Build ID |
| `BUILD_URL` | `http://jenkins.com/job/my-job/42` | Share build link |
| `JOB_NAME` | `my-job` | Log job name |
| `BRANCH_NAME` | `main`, `develop` | Different actions per branch |
| `GIT_COMMIT` | `a1b2c3d...` | Tag release |
| `WORKSPACE` | `/var/lib/jenkins/jobs/my-job` | File operations |
| `NODE_NAME` | `linux-agent-1` | Which agent ran it |

### 💡 How to Use

```groovy
pipeline {
    agent any
    environment {
        VERSION = "${BUILD_NUMBER}"           // Custom variable
        IMAGE_TAG = "${BUILD_NUMBER}-${GIT_COMMIT.take(7)}"  // Build unique tag
    }
    stages {
        stage('Show Variables') {
            steps {
                echo "Build: ${BUILD_NUMBER}"
                echo "Job: ${JOB_NAME}"
                echo "Branch: ${BRANCH_NAME}"
                echo "Image: ${IMAGE_TAG}"
            }
        }
    }
}
```

**Key Rule**: 
```
"Double quotes" = Variables work ✅
'Single quotes' = Variables don't work ❌
```

---

## 9️⃣ Parameters - User Input

### 🎮 Give Users Choices

```groovy
pipeline {
    parameters {
        string(name: 'VERSION', defaultValue: '1.0', description: 'Release version')
        
        choice(name: 'ENV', 
               choices: ['dev', 'staging', 'prod'],
               description: 'Environment')
        
        booleanParam(name: 'RUN_TESTS', 
                     defaultValue: true,
                     description: 'Run automated tests')
    }
    
    stages {
        stage('Build') {
            when {
                expression { params.RUN_TESTS == true }  // Conditional
            }
            steps {
                echo "📦 Version: ${params.VERSION}"
                echo "🌍 Environment: ${params.ENV}"
                sh 'mvn test'
            }
        }
    }
}
```

**UI Result:**
```
┌──────────────────────────────┐
│ Build with Parameters        │
├──────────────────────────────┤
│ VERSION:  [1.0]            │
│ ENV:      [dev ▼]          │
│ RUN_TESTS: ☑ (checked)    │
│                            │
│ [BUILD] button             │
└──────────────────────────────┘
```

---

## 🔟 Credentials - Secure Secrets

### 🔐 Store Passwords & Keys Safely

```groovy
pipeline {
    environment {
        // Reference stored credential
        DB_CREDS = credentials('database-password')
        DOCKER_HUB = credentials('dockerhub-credentials')
    }
    
    stages {
        stage('Deploy') {
            steps {
                // Safe - password is masked in logs: ****
                withCredentials([
                    usernamePassword(
                        credentialsId: 'github-token',
                        usernameVariable: 'GIT_USER',
                        passwordVariable: 'GIT_PASS'
                    )
                ]) {
                    sh 'echo "Using secure credentials"'
                    sh 'curl -u $GIT_USER:$GIT_PASS https://github.com/api'
                }
            }
        }
    }
}
```

**UI Steps to Add Credentials:**

```
Dashboard
  ├─ Manage Jenkins
  ├─ Manage Credentials
  ├─ System → Global credentials
  ├─ Add Credentials
  │  ├─ Kind: Username & Password
  │  ├─ Username: myuser
  │  ├─ Password: secret123
  │  ├─ ID: database-password
  │  └─ [Create]
```

---

# 🎯 PART 3: ADVANCED FEATURES

## 1️⃣1️⃣ CLI Commands - Control Jenkins from Terminal

### 🖥️ Common Commands

```bash
# ✅ Build a job
java -jar jenkins-cli.jar build my-job

# ✅ Build with parameters
java -jar jenkins-cli.jar build my-job -p VERSION=1.0 -p ENV=prod

# ✅ List all jobs
java -jar jenkins-cli.jar list-jobs

# ✅ Get job configuration
java -jar jenkins-cli.jar get-job my-job > config.xml

# ✅ Update job configuration
java -jar jenkins-cli.jar update-job my-job < updated-config.xml

# ✅ Copy job
java -jar jenkins-cli.jar copy-job old-job new-job

# ✅ Delete job
java -jar jenkins-cli.jar delete-job my-job

# ✅ Disable job
java -jar jenkins-cli.jar disable-job my-job

# ✅ Enable job
java -jar jenkins-cli.jar enable-job my-job

# ✅ Get build console output
java -jar jenkins-cli.jar console my-job 42

# ✅ Stop a running build
java -jar jenkins-cli.jar stop-builds my-job
```

**Shortcut:**
```bash
alias jenkins='java -jar jenkins-cli.jar -s http://localhost:8080'
jenkins build my-job
jenkins list-jobs
jenkins get-job my-job > config.xml
```

---

## 1️⃣2️⃣ Error Handling - When Things Go Wrong

### 🛡️ Protect Your Pipeline

```groovy
pipeline {
    agent any
    
    stages {
        stage('Build') {
            steps {
                script {
                    try {
                        echo '🔨 Building...'
                        sh 'mvn clean package'
                        echo '✅ Build successful'
                    } catch (Exception e) {
                        echo "❌ Build failed: ${e.message}"
                        currentBuild.result = 'FAILURE'
                        throw e  // Re-throw to stop pipeline
                    }
                }
            }
        }
        
        stage('Test with Retry') {
            steps {
                retry(3) {  // Try 3 times
                    echo '🧪 Testing...'
                    sh 'npm test'
                }
            }
        }
        
        stage('Deploy') {
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    echo '🚀 Deploying...'
                    sh './deploy.sh'
                }
            }
        }
    }
    
    post {
        failure {
            echo '❌ Pipeline failed!'
            sh 'send_alert_to_team.sh'
        }
    }
}
```

### 📊 Error Handling Flow

```
Stage starts
    │
    ├─ ✅ Success → Continue to next stage
    │
    ├─ ❌ First try fails
    │  └─ Retry (2 more times)
    │
    ├─ Still fails → Enter catch block
    │  ├─ Log error message
    │  ├─ Set result = FAILURE
    │  └─ Stop pipeline
    │
    └─ Post-build runs (always)
       ├─ always { } block
       ├─ failure { } block (send alerts)
       └─ success { } block
```

---

## 1️⃣3️⃣ Parallel Execution - Run Multiple Tasks

### ⚡ Speed Up Your Pipeline

```groovy
pipeline {
    agent any
    
    stages {
        stage('Parallel Testing') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        echo '🧪 Running unit tests...'
                        sh 'mvn test -Dgroups=unit'
                    }
                }
                
                stage('Integration Tests') {
                    steps {
                        echo '🔗 Running integration tests...'
                        sh 'mvn test -Dgroups=integration'
                    }
                }
                
                stage('Code Quality') {
                    steps {
                        echo '📊 SonarQube scan...'
                        sh 'sonar-scanner'
                    }
                }
            }
        }
    }
}
```

### 🏁 Execution Timeline Comparison

```
SERIAL (One after another):
Build ═══ Test ═══ Deploy ═══ Total: 20 minutes
 (5m)      (7m)     (8m)

PARALLEL (Simultaneously):
Unit Tests ════════════ (7m)
Integration ════════════ (7m)  } Total: 10 minutes
Code Quality ═══════ (5m)      (All run together!)

TIME SAVED: 50% ⚡
```

---

## 1️⃣4️⃣ Shared Libraries - Code Reuse

### 🔄 Don't Repeat Yourself

```groovy
// 📚 In shared library: vars/deployApp.groovy
def call(String environment) {
    echo "🚀 Deploying to ${environment}"
    sh "./scripts/deploy.sh ${environment}"
    sh "curl -f http://${environment}-app.com/health"
}

// 📝 In pipeline: use it!
@Library('my-shared-lib') _

pipeline {
    agent any
    stages {
        stage('Deploy Dev') {
            steps {
                deployApp('dev')      // ✅ Reusable!
            }
        }
        stage('Deploy Prod') {
            steps {
                deployApp('prod')     // ✅ Same function!
            }
        }
    }
}
```

**Directory Structure:**
```
my-shared-library/
├── vars/
│   ├── deployApp.groovy
│   ├── runTests.groovy
│   └── notifySlack.groovy
├── src/
│   └── com/company/Utils.groovy
└── README.md
```

---

# 🏆 PART 4: EXPERT LEVEL

## 1️⃣5️⃣ Groovy DSL - Advanced Scripting

### 🧙 Groovy Magic

```groovy
// ✨ Collections & Loops
def numbers = [1, 2, 3, 4, 5]
def doubled = numbers.collect { it * 2 }  // [2, 4, 6, 8, 10]
def filtered = numbers.findAll { it > 2 }  // [3, 4, 5]

// ✨ Closures (Code blocks)
def greet = { name -> "Hello, $name!" }
echo greet("Jenkins")  // Hello, Jenkins!

// ✨ String interpolation
def version = "1.0"
echo "Building version: $version"       // ✅ Works (double quotes)
echo 'Building version: $version'       // ❌ Literal (single quotes)

// ✨ Map operations
def config = [host: 'localhost', port: 8080]
config.each { key, value ->
    echo "$key = $value"
}

// ✨ Regular expressions
def text = "Build failed"
if (text =~ /failed/) {
    echo "⚠️ Detected failure!"
}
```

---

## 1️⃣6️⃣ JCasC - Configuration as Code

### 📄 Jenkins Config in YAML

```yaml
# 💾 jenkins.yaml - Entire Jenkins setup!

jenkins:
  systemMessage: "My Enterprise Jenkins"
  numExecutors: 0                    # No builds on master
  
  securityRealm:
    ldap:
      server: ldap.company.com
  
  authorizationStrategy:
    matrix:
      permissions:
        - "Overall/Administer:admin"
        - "Overall/Read:developers"
  
  nodes:
    - permanent:
        name: "linux-build"
        numExecutors: 8
        labels: "linux docker"
        launcher:
          ssh:
            host: "build.company.com"
            credentialsId: "ssh-key"

credentials:
  system:
    domainCredentials:
      - credentials:
          - usernamePassword:
              id: "github"
              username: "devops"
              password: "${GITHUB_TOKEN}"

unclassified:
  location:
    url: "https://jenkins.company.com"
```

**Benefits:**
```
✅ Version controlled
✅ Reproducible setup
✅ No UI configuration drift
✅ Automated deployment
✅ Disaster recovery ready
```

---

## 1️⃣7️⃣ REST API - Control Jenkins via HTTP

### 🌐 API Calls

```bash
# 🔑 Authenticate with API token
TOKEN="11e9d8c1f7a0b2c3d"
USER="admin"
JENKINS="http://localhost:8080"

# ✅ Get system info
curl -u $USER:$TOKEN $JENKINS/api/json | jq .

# ✅ Get job info
curl -u $USER:$TOKEN $JENKINS/job/my-job/api/json

# ✅ Trigger build
curl -X POST -u $USER:$TOKEN $JENKINS/job/my-job/build

# ✅ Trigger with parameters
curl -X POST -u $USER:$TOKEN \
  "$JENKINS/job/my-job/buildWithParameters?VERSION=1.0&ENV=prod"

# ✅ Get console output
curl -u $USER:$TOKEN $JENKINS/job/my-job/42/consoleText

# ✅ List all jobs
curl -u $USER:$TOKEN "$JENKINS/api/json?tree=jobs[name,status]"
```

---

## 1️⃣8️⃣ Job DSL - Generate Jobs

### 🏭 Create Jobs Programmatically

```groovy
// 🔨 Seed Job Script

// Create job template
def createJob(String name, String gitBranch, String env) {
    job("${name}-${env}") {
        description "Build ${name} for ${env}"
        
        scm {
            git {
                remote { url "https://github.com/myorg/${name}.git" }
                branch "*/${gitBranch}"
            }
        }
        
        steps {
            shell "echo Building for ${env}"
            shell "./build.sh"
        }
        
        publishers {
            archiveArtifacts('build/**/*')
        }
    }
}

// 📊 Generate jobs for multiple environments
['dev', 'staging', 'prod'].each { env ->
    createJob('my-app', 'main', env)
}

// Result: Creates 3 jobs!
// ✅ my-app-dev
// ✅ my-app-staging
// ✅ my-app-prod
```

---

## 1️⃣9️⃣ Performance Optimization

### ⚡ Make Jenkins Fast

```groovy
pipeline {
    agent any
    
    options {
        // 🚀 Performance settings
        timeout(time: 1, unit: 'HOURS')
        
        buildDiscarder(logRotator(
            numToKeepStr: '10',           // Keep last 10 builds
            artifactNumToKeepStr: '5'     // Keep last 5 artifacts
        ))
        
        disableConcurrentBuilds()         // One at a time
    }
    
    stages {
        stage('Fast Build') {
            steps {
                script {
                    sh '''
                        # Shallow clone (faster)
                        git clone --depth=1 https://github.com/user/repo.git
                        
                        # Parallel compilation
                        mvn clean package -T 1C -DskipTests
                        
                        # Parallel tests
                        mvn test -T 2C
                    '''
                }
            }
        }
    }
}
```

**Performance Tips:**
```
⚡ Use shallow clones (--depth=1)
⚡ Parallel builds (-T flag)
⚡ Cache dependencies
⚡ Clean workspace after build
⚡ Set master executors to 0
⚡ Use fast agents for build
```

---

## 2️⃣0️⃣ Monitoring & Metrics

### 📊 Keep Track of Everything

```bash
# 🔍 Monitor queue
curl http://localhost:8080/queue/api/json | jq '.items | length'

# 🔍 Monitor executors
curl http://localhost:8080/api/json | jq '.executors'

# 🔍 Monitor agent status
curl http://localhost:8080/api/json | jq '.nodes[] | {name, offline}'

# 🔍 Check build status
curl http://localhost:8080/job/my-job/api/json | jq '.lastBuild.result'
```

**Dashboard Metrics:**
```
┌────────────────────────────────┐
│ 📊 JENKINS METRICS             │
├────────────────────────────────┤
│ Total Jobs: 42                 │
│ Running Builds: 5              │
│ Queue Length: 3                │
│ Agents Online: 8/8             │
│ Avg Build Time: 5m 30s         │
│ Success Rate: 96%              │
└────────────────────────────────┘
```

---

## 2️⃣1️⃣ Backup & Disaster Recovery

### 💾 Protect Your Jenkins

```bash
# 🔄 Automated backup script

#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP="/backup/jenkins-$DATE.tar.gz"

# Backup with excluded folders
tar -czf $BACKUP \
    --exclude='workspace' \
    --exclude='.m2' \
    --exclude='.ivy2' \
    /var/lib/jenkins

echo "✅ Backup saved: $BACKUP"

# Keep only last 30 days
find /backup -name "jenkins-*.tar.gz" -mtime +30 -delete

# 🔄 Add to crontab (daily at 2 AM)
# 0 2 * * * /usr/local/bin/backup-jenkins.sh
```

**Recovery Steps:**
```
1️⃣  Stop Jenkins
    sudo systemctl stop jenkins

2️⃣  Restore backup
    cd /var/lib
    tar -xzf /backup/jenkins-20250115_020000.tar.gz

3️⃣  Fix permissions
    sudo chown -R jenkins:jenkins /var/lib/jenkins

4️⃣  Start Jenkins
    sudo systemctl start jenkins
```

---

## 2️⃣2️⃣ Production Pipeline Template

### 🏢 Enterprise-Ready Pipeline

```groovy
@Library('shared-library') _

pipeline {
    agent any
    
    parameters {
        string(name: 'VERSION', defaultValue: '1.0.0')
        choice(name: 'ENV', choices: ['dev', 'prod'])
    }
    
    environment {
        APP = 'my-app'
        DOCKER_REGISTRY = 'docker.io'
    }
    
    triggers {
        githubPush()              // Build on code push
        cron('H 2 * * *')         // Daily at 2 AM
    }
    
    stages {
        stage('🔍 Validate') {
            steps {
                echo "✓ Validating environment"
                sh 'java -version && docker -v'
            }
        }
        
        stage('🔨 Build') {
            parallel {
                stage('Compile') { steps { sh 'mvn compile' } }
                stage('Quality') { steps { sh 'mvn sonar:sonar' } }
            }
        }
        
        stage('✅ Test') {
            steps {
                sh 'mvn test'
                junit '**/target/surefire-reports/*.xml'
            }
        }
        
        stage('📦 Package') {
            steps {
                sh 'mvn package -DskipTests'
                archiveArtifacts 'target/*.jar'
            }
        }
        
        stage('🐳 Docker Build') {
            steps {
                sh "docker build -t $DOCKER_REGISTRY/$APP:${params.VERSION} ."
            }
        }
        
        stage('🚀 Deploy') {
            when { branch 'main' }
            input { message "Deploy to prod?" }
            steps {
                sh "./deploy.sh ${params.ENV}"
            }
        }
    }
    
    post {
        always {
            echo "🧹 Cleanup"
            cleanWs()
        }
        success {
            echo "🎉 Pipeline succeeded!"
            mail(to: 'team@company.com', subject: 'Build Success')
        }
        failure {
            echo "❌ Pipeline failed!"
            mail(to: 'team@company.com', subject: 'Build Failed')
        }
    }
}
```

---

# 🎓 QUICK REFERENCE CHEAT SHEET

## 📝 Pipeline Syntax Quick Reference

| Element | Purpose | Example |
|---------|---------|---------|
| `agent` | Where to run | `agent any`, `agent { label 'linux' }` |
| `stages` | Main workflow | Groups all stages |
| `stage` | Logical step | `stage('Build')` |
| `steps` | Commands | `sh 'mvn clean'` |
| `when` | Conditional | `when { branch 'main' }` |
| `post` | After pipeline | `post { always { } }` |
| `triggers` | What starts it | `githubPush()`, `cron('H * * * *')` |
| `parameters` | User input | `string()`, `choice()` |
| `environment` | Variables | `VERSION = '1.0'` |
| `parallel` | Run together | Multiple stages at once |

---

## 🔗 Common Command Combinations

```bash
# 🔨 Build & wait for output
jenkins build my-job -s

# 🔨 Build with parameters & wait
jenkins build my-job -p ENV=prod -p VERSION=1.0 -s

# 📋 Copy and update job
jenkins get-job template > my-job.xml
# [edit my-job.xml]
jenkins create-job my-new-job < my-job.xml

# 🧹 Clean old builds (keep last 10)
for i in {1..200}; do jenkins delete-build my-job $i; done

# 📊 List failed builds
jenkins api my-job/api/json | jq '.builds[] | select(.result=="FAILURE")'
```

---

## 🎯 Decision Tree - What to Use?

```
                    Need to automate?
                           │
                    ┌──────┴──────┐
                    │             │
                YES │             │ NO
                    │             │
                    ▼             ▼
              Simple task?    Do it manually
              ┌────┴────┐
              │         │
           YES│         │NO
              ▼         ▼
          🎯 FREESTYLE 📦 PIPELINE
          ✅ Freestyle    ✅ Multibranch
          ✅ GUI config   ✅ Code control
          ✅ Quick       ✅ Complex

                     │
              Multi-branch?
                     │
                  ┌──┴──┐
                  │     │
                YES│     │NO
                  │     │
                  ▼     ▼
              🌿 MULTIBRANCH  📦 PIPELINE
              (Auto-scans)    (Manual setup)
```

---

## 🚨 Troubleshooting Quick Fixes

| Problem | Fix |
|---------|-----|
| ❌ Build hangs | Add `timeout(time: 1, unit: 'HOURS')` |
| ❌ Out of memory | Increase JAVA_OPTS: `-Xmx4g` |
| ❌ No agents available | Add more agents or reduce concurrency |
| ❌ Password visible in logs | Use `withCredentials { }` block |
| ❌ Build slow | Use parallel stages, shallow clone |
| ❌ Disk full | Enable `buildDiscarder()` to clean old builds |
| ❌ Agent offline | Check SSH connection, restart agent |

---

## 📞 Need Help?

```
┌─────────────────────────────────────┐
│          JENKINS RESOURCES          │
├─────────────────────────────────────┤
│ 📚 Official Docs                    │
│    https://www.jenkins.io           │
│                                     │
│ 🎥 Video Tutorials                  │
│    YouTube: "Jenkins tutorial"      │
│                                     │
│ 💬 Community                        │
│    Jenkins Forums & Stack Overflow  │
│                                     │
│ 🐛 Report Issues                    │
│    GitHub: jenkinsci/jenkins        │
│                                     │
│ 🏢 Enterprise Support               │
│    CloudBees Jenkins Support        │
└─────────────────────────────────────┘
```

---

## 🎯 Learning Roadmap

```
Week 1: BASICS
├─ Install Jenkins
├─ Create first job
├─ Trigger manual build
└─ View console output

Week 2-3: INTERMEDIATE
├─ Create pipeline
├─ Use Git triggers
├─ Add parameters
└─ Store credentials

Week 4-5: ADVANCED
├─ Setup agents
├─ Parallel stages
├─ Error handling
└─ Email notifications

Week 6+: EXPERT
├─ Shared libraries
├─ JCasC setup
├─ REST API integration
└─ Performance tuning
```

---

## ✨ Key Emojis Reference

| Emoji | Meaning |
|-------|---------|
| 🔨 | Build |
| ✅ | Success / Correct |
| ❌ | Failure / Wrong |
| ⚠️ | Warning / Unstable |
| 🚀 | Deploy |
| 💾 | Backup |
| ⚙️ | Configuration |
| 🔐 | Security / Credentials |
| 🌐 | Network / HTTP |
| 📊 | Metrics / Data |
| 🔄 | Retry / Repeat |
| ⏰ | Schedule / Time |
| 👤 | User / Manual |

---

**Made with ❤️ by ***Mannan Kazi*** for Jenkins learners**

---

# 🎊 THAT'S ALL FOLKS!

You now have everything you need to master Jenkins from beginner to expert level! 

**What's Next?**
- 📝 Pick a section, try it out
- 🏗️ Build your first pipeline
- 📚 Keep this cheatsheet handy
- 🤝 Help others learn Jenkins

---

## 👤 About the Author

**Mannan Kazi**  
DevOps Engineer | Jenkins | CI/CD | Automation  

- 💡 Passionate about simplifying DevOps concepts
- 🛠️ Hands-on experience with Jenkins pipelines & automation
- 📚 Created this cheatsheet to help learners go from beginner to expert

---


**Happy Jenkinysing!** 🎉
