# 🦊 GitLab Complete Cheatsheet - Easy & Visual Edition

> **A Beginner-Friendly, Beautifully Formatted Guide to GitLab** 
> From First Steps to Advanced Mastery

---

## 📑 Table of Contents

```
🚀 QUICK START & BASICS
├─ ✨ Getting Started
├─ 🔐 Authentication Setup
└─ 📦 Your First Project

📊 MANAGING YOUR CODE
├─ 🌿 Branches & Merging
├─ 📝 Issues & Planning
├─ 🔀 Merge Requests
└─ 📚 Repository Management

⚙️ AUTOMATION & PIPELINES
├─ 🔧 CI/CD Basics
├─ 🎯 Advanced Pipeline Features
├─ 🐳 Docker & Containers
└─ 🚀 Deployment Strategies

🛠️ TOOLS & COMMANDS
├─ 💻 GitLab CLI (glab)
├─ 🌐 REST API
├─ 📊 GraphQL API
└─ ☸️ Kubernetes Integration

🔒 SECURITY & BEST PRACTICES
├─ 🛡️ Access Control
├─ 🔑 Secrets & Variables
└─ ✅ Security Checklist
```

---

# 🚀 QUICK START & BASICS

## ✨ What is GitLab?

```
┌─────────────────────────────────────┐
│   🦊 GitLab - Complete DevOps      │
│         Platform                    │
├─────────────────────────────────────┤
│ 📝 Source Code Management          │
│ 📋 Issue Tracking & Planning       │
│ 🔄 CI/CD Automation                │
│ 🚀 Deployment Tools                │
│ 🔐 Security & Compliance           │
│ 📊 Analytics & Insights            │
│ 🧪 Testing Frameworks              │
│ 🔧 Infrastructure as Code          │
└─────────────────────────────────────┘
```

### 🎯 Key Concepts at a Glance

| Term | What It Is | Example |
|------|-----------|---------|
| **📦 Project** | Your repository with code | `my-awesome-app` |
| **👥 Group** | Folder for multiple projects | `my-team` |
| **🌿 Branch** | Parallel development path | `feature/dark-mode` |
| **🔀 Merge Request** | Request to merge changes | "Add dark mode feature" |
| **⚙️ Pipeline** | Automated workflow | Build → Test → Deploy |
| **🏃 Runner** | Machine that runs jobs | Linux, Docker, Kubernetes |
| **🎯 Environment** | Deployment target | Dev, Staging, Production |
| **📦 Artifact** | Build output/files | Compiled code, images |

---

## 🔐 Getting Started: Setup in 5 Minutes

### Step 1️⃣: Create Your Account
```
Visit: https://gitlab.com
     ↓
Click "Sign up"
     ↓
Enter email & password
     ↓
Verify email
     ↓
✅ You're in!
```

### Step 2️⃣: Generate Your SSH Key 🔑

**On your computer (Terminal/PowerShell):**
```bash
ssh-keygen -t ed25519 -C "your@email.com"
# Press ENTER for default location
# Enter passphrase (or leave blank)

# View your public key:
cat ~/.ssh/id_ed25519.pub
```

**Add to GitLab:**
1. 🔧 Click your **Avatar** (top-right)
2. Select **Settings** → **SSH Keys**
3. 📋 Paste your public key
4. ✅ Click **Add key**

### Step 3️⃣: Create Your First Project

```
🦊 GitLab Home
     ↓
Click [+] → New project
     ↓
Enter project name: "hello-world"
     ↓
Set Visibility: 🔒 Private
     ↓
Click "Create project"
     ↓
✅ Done! Your repo is ready
```

### Step 4️⃣: Clone to Your Computer

```bash
# Copy the SSH URL from your project
git clone git@gitlab.com:yourname/hello-world.git

# Go into your project
cd hello-world

# Check status
git status
```

### Step 5️⃣: Make Your First Commit

```bash
# Create a file
echo "Hello, GitLab!" > README.md

# Stage changes
git add README.md

# Create commit
git commit -m "📝 Add README"

# Push to GitLab
git push origin main

# ✅ Done! Check your project on GitLab 🎉
```

---

# 📊 MANAGING YOUR CODE

## 🌿 Branches: Your Parallel Work Spaces

### 🎨 Visual Branch Workflow

```
                    🌿 main (Production)
                      ↑
                   Merge ✅
                      │
        🚀 Deploy     📝 Code Review
           │            │
          │             │
    ┌─────┴─────┬──────┴─────┐
    │           │            │
   feature  🌿 develop   hotfix
   /button   branch      branch
    │
 🛠️ You Work Here
 (Create & Edit Files)
```

### ✅ How to Create & Use a Branch

#### Method 1️⃣: Using Web UI (Easiest)
```
1. Go to Repository → Branches
2. Click [New branch]
3. Enter name: "feature/dark-mode"
4. Click [Create branch]
5. ✅ Ready to use!
```

#### Method 2️⃣: Using Terminal (Faster)
```bash
# Create new branch from main
git checkout -b feature/dark-mode

# Make your changes
echo "Dark mode code here" > app.js

# See what changed
git status

# Stage changes
git add app.js

# Commit
git commit -m "✨ Add dark mode toggle"

# Push to GitLab
git push origin feature/dark-mode

# ✅ Done! GitLab will show a banner to create MR
```

### 🛡️ Protect Important Branches

```
⚠️ Protecting "main" Branch
┌─────────────────────────────────┐
│ Settings → Repository           │
│ → Branch rules                  │
├─────────────────────────────────┤
│ Branch name: main               │
│                                 │
│ ✅ Require approvals: 2          │
│ ✅ Require code owner review    │
│ ✅ Code must pass pipeline      │
│ ✅ Block push directly          │
│                                 │
│ Result: 🛡️ Safe from accidents! │
└─────────────────────────────────┘
```

**What This Means:**
- ❌ Can't push directly to `main`
- ✅ Need 2 people to approve changes
- ✅ Pipeline must pass first
- ✅ Reduces bugs in production

---

## 📝 Issues: Your Task Manager

### 🎯 Create an Issue (Task/Bug/Feature Request)

```
Click [Issues] → [New issue]
        ↓
📋 Title: "Fix login button on mobile"
        ↓
📝 Description: 
   • Button not visible on mobile
   • Affects all users
   • Need urgent fix
        ↓
🏷️ Labels: bug, urgent, mobile
        ↓
👤 Assign to: @yourself
        ↓
📅 Due date: Today
        ↓
✅ Click [Create issue]
```

### ⚡ Quick Actions (Type These in Issue Comments)

These are **magic commands** - type them in the issue to take actions:

```
/assign @username          → 👤 Assign to someone
/unassign                  → 👤 Remove assignment

/label bug, urgent         → 🏷️ Add labels
/unlabel bug               → 🏷️ Remove label

/milestone v1.0            → 📍 Set release version
/due 2025-12-31            → 📅 Set deadline

/estimate 3h               → ⏱️ How long will it take?
/spend 2h                  → ⏱️ How long you spent

/weight 5                  → 📊 Complexity level (1-10)

/close                     → ✅ Mark as done
/reopen                    → 🔄 Reopen if needed

/subscribe                 → 🔔 Get notifications
/unsubscribe               → 🔇 Stop notifications
```

**Example:**
```
Just finished this task!

/close
/label done
/spend 4h

Great work, @team! 🎉
```

### 📌 Labels: Organize Everything

```
Bug Labels 🐛              Feature Labels ✨
├─ bug-critical           ├─ feature
├─ bug-high               ├─ enhancement  
├─ bug-medium             ├─ improvement
└─ bug-low                └─ ui/ux

Team Labels 👥            Status Labels 📊
├─ backend                ├─ todo
├─ frontend               ├─ in-progress
├─ devops                 ├─ review
├─ security               └─ done
└─ documentation
```

**How to Create Labels:**
```
Click [Labels] → [New label]
Enter name: "bug-critical"
Choose color: 🔴 Red
Click [Create]
```

### 📊 Issue Board (Kanban View)

```
┌──────────────┬──────────────┬──────────────┬──────────────┐
│   📋 TODO    │  🔄 DOING   │  👀 REVIEW   │   ✅ DONE    │
├──────────────┼──────────────┼──────────────┼──────────────┤
│  ┌────────┐  │ ┌────────┐  │ ┌────────┐  │ ┌────────┐   │
│  │ #1234  │  │ │ #1235  │  │ │ #1236  │  │ │ #1230  │   │
│  │ Fix    │  │ │ Add    │  │ │ Dark   │  │ │ Search │   │
│  │ Login  │  │ │ Dark   │  │ │ Mode   │  │ │ Fixed  │   │
│  │ Bug    │  │ │ Mode   │  │ │ Review │  │ │ ✅     │   │
│  └────────┘  │ └────────┘  │ └────────┘  │ └────────┘   │
│              │            │             │              │
│  ┌────────┐  │            │  ┌────────┐ │              │
│  │ #1237  │  │            │  │ #1238  │ │              │
│  │ Mobile │  │            │  │ API    │ │              │
│  │ Issues │  │            │  │ Update │ │              │
│  └────────┘  │            │  └────────┘ │              │
│              │            │             │              │
└──────────────┴──────────────┴──────────────┴──────────────┘

💡 Drag cards between columns to update status!
```

---

## 🔀 Merge Requests (MR): Code Review

### 📋 What is a Merge Request?

```
Your Branch          Main Branch
(feature/dark)         (main)
     │                  │
     │                  │
     └──── 🔀 MR ──────→ │
          Changes to
          review & merge
          
      It's like saying:
      "Hey team, I finished this feature,
       please review and merge it!"
```

### ✍️ Create a Merge Request

**After pushing your branch:**

```bash
# Push your changes
git push origin feature/dark-mode

# 🎉 GitLab shows a banner:
# ┌────────────────────────────────┐
# │ Create merge request for        │
# │ feature/dark-mode?              │
# │ [Create merge request] [Dismiss]│
# └────────────────────────────────┘

# Click [Create merge request] or:
# Go to Merge Requests → [New MR]
```

**Fill in the Details:**
```
Title: ✨ Add dark mode toggle

Description:
## What does this MR do?
Adds a dark mode toggle to the header

## Related Issues
Closes #1234

## How to test?
1. Go to settings
2. Click "Dark mode" toggle
3. Verify all colors work

## Checklist
- ✅ Tests pass
- ✅ No console errors
- ✅ Mobile responsive
- ✅ Documentation updated

Assignee: @reviewer
Labels: feature, ui/ux
Milestone: v2.0
```

### 👀 Code Review Process

```
1️⃣ You create MR
        ↓
2️⃣ Team receives notification
   "Hey, review my code!"
        ↓
3️⃣ Reviewers check your code
   Click [Changes] tab
   Click lines to comment
        ↓
4️⃣ Discussion & Changes
   You fix issues
   Push new commits
   Discussion updates
        ↓
5️⃣ Approval
   Reviewers click [Approve]
   (2 approvals needed)
        ↓
6️⃣ Merge ✅
   Click [Merge]
   Your code goes to main!
        ↓
7️⃣ Celebrate! 🎉
   Your feature is live!
```

### 💬 Commenting on Code

```
In the [Changes] tab:

File: app.js
┌─────────────────────┐
│ 1  const dark = true│
│ 2  if (dark) {      │  ← Click "+" here
│ 3    toggleTheme()  │     to add comment
│ 4  }                │
└─────────────────────┘

💭 Comment appears:
┌─────────────────────────────────┐
│ @reviewer                       │
│                                 │
│ Good change! Did you test on   │
│ mobile devices too?            │
│                                 │
│ [Reply] [Resolve] [Emoji ☺️]   │
└─────────────────────────────────┘
```

### ✅ Approval Workflow

```
MR Status Indicator:

🔴 Not Ready
   ├─ Waiting for pipeline
   ├─ Waiting for approvals
   └─ Failed checks

🟡 In Progress
   ├─ Pipeline running
   └─ Waiting for more approvals

🟢 Ready to Merge
   ├─ ✅ Pipeline passed
   ├─ ✅ 2/2 approvals
   └─ [Merge] button active!

✅ Merged
   └─ Code in main branch
```

---

## 📚 Repository Management

### 🔄 Clone Your Project

```bash
# Method 1: SSH (Recommended - uses your key)
git clone git@gitlab.com:group/project.git

# Method 2: HTTPS (Uses username/token)
git clone https://gitlab.com/group/project.git

# Go into folder
cd project

# See all branches
git branch -a

# Make sure you're on main
git checkout main

# Update to latest code
git pull origin main
```

### 📤 Push Your Changes

```bash
# See what changed
git status

# Stage all changes
git add .

# Or stage specific files
git add app.js styles.css

# Create commit
git commit -m "Type your message here"

# Push to GitLab
git push origin branch-name

# ✅ Done!
```

### 🔗 Link Issues to Commits

```
When writing commit message, add issue number:

git commit -m "Fix login bug #1234"

or in MR description:

"Closes #1234"
"Fixes #1234"
"Resolves #1234"

Result: 🎯 Issue auto-closes when MR merges!
```

---

# ⚙️ AUTOMATION & PIPELINES

## 🔧 CI/CD: What & Why?

```
┌──────────────────────────────────────┐
│   Your Code                          │
└────────────┬─────────────────────────┘
             │
             ▼
   ⚙️ AUTOMATED TESTING
   (Run your tests automatically)
             │
             ├─ ✅ Tests pass?
             │
             ├─ ✅ No errors?
             │
             └─ ✅ Security OK?
             │
             ▼
    🚀 AUTOMATED DEPLOYMENT
    (Update the live website)
             │
             ▼
         ✅ LIVE! 🎉
         
Without CI/CD:
❌ Run tests manually
❌ Deploy manually
❌ Easy to forget
❌ Human errors

With CI/CD:
✅ Automatic testing
✅ Automatic deployment
✅ Never forget
✅ Consistent results
```

## 📋 Your First Pipeline

### Step 1: Create `.gitlab-ci.yml` File

**Location:** Root of your project (same level as `README.md`)

```yaml
# 🚀 Your First Pipeline
stages:
  - build
  - test
  - deploy

# 🔨 Build Stage
build_job:
  stage: build
  image: node:18
  script:
    - echo "📦 Building project..."
    - npm install
    - npm run build
  artifacts:
    paths:
      - dist/    # Save build files

# 🧪 Test Stage
test_job:
  stage: test
  image: node:18
  script:
    - echo "🧪 Running tests..."
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'

# 🚀 Deploy Stage
deploy_job:
  stage: deploy
  image: alpine
  script:
    - echo "🚀 Deploying to production..."
    - echo "Success!"
  environment:
    name: production
    url: https://example.com
  only:
    - main  # Only deploy main branch
```

### Step 2: Push to GitLab

```bash
git add .gitlab-ci.yml
git commit -m "🔧 Add CI/CD pipeline"
git push origin main
```

### Step 3: Watch Pipeline Run! 🚀

```
Go to: CI/CD → Pipelines

┌──────────────────────────────────┐
│ Pipeline #123                    │
│ Status: ⏳ Running                │
├──────────────────────────────────┤
│                                  │
│  🔨 BUILD                        │
│   └─ build_job ⏳ Running        │
│                                  │
│  🧪 TEST                         │
│   └─ test_job ⏳ Queued          │
│                                  │
│  🚀 DEPLOY                       │
│   └─ deploy_job ⏳ Queued        │
│                                  │
└──────────────────────────────────┘

Click any job to see logs! 📜
```

### 📊 Pipeline Workflow Diagram

```
Your Code Pushed
      │
      ▼
┌─────────────┐
│🔨 BUILD    │ (Install dependencies, compile code)
└──────┬──────┘
       │
       ▼
┌─────────────┐
│🧪 TEST     │ (Run tests, check quality)
└──────┬──────┘
       │
       ├─ ✅ Passed?
       │
       ├─ ❌ Failed?
       │   └─ 🔴 STOP (Notify developer)
       │
       ▼
┌─────────────┐
│🚀 DEPLOY   │ (Update live server)
└──────┬──────┘
       │
       ▼
   ✅ LIVE! 🎉
```

## 🐳 Using Docker Images

```yaml
# Using a specific image for a job
build_job:
  image: node:18-alpine  # Lightweight version
  script:
    - npm install
    - npm run build

test_job:
  image: python:3.11
  script:
    - python -m pytest

deploy_job:
  image: golang:1.21
  script:
    - go build
```

### 🎯 Common Docker Images

| Language | Image |
|----------|-------|
| **JavaScript/Node** | `node:18`, `node:20` |
| **Python** | `python:3.11`, `python:3.12` |
| **Go** | `golang:1.21` |
| **Java** | `openjdk:17`, `maven:3.9` |
| **PHP** | `php:8.2`, `composer:latest` |
| **Ruby** | `ruby:3.2` |
| **C/C++** | `gcc:13`, `ubuntu:22.04` |

## 🔐 Secret Variables (API Keys, Passwords)

### ⚠️ Never hardcode secrets!

```yaml
❌ WRONG - Never do this!
────────────────────────────
deploy_job:
  script:
    - export API_KEY="abc123def456"
    - curl https://api.example.com -H "Authorization: $API_KEY"

✅ CORRECT - Use secret variables!
────────────────────────────
deploy_job:
  script:
    - curl https://api.example.com -H "Authorization: $API_KEY"
    
# API_KEY is stored safely in GitLab settings
```

### 🔑 Add Secret Variables

```
1. Go to Settings → CI/CD → Variables
2. Click [Add variable]
3. Key: API_KEY
4. Value: your-secret-key-here
5. ✅ Check [Protect variable]
   (only available on main branch)
6. ✅ Check [Mask variable]
   (hidden in logs)
7. Click [Add variable]
```

### 📝 Use in Pipeline

```yaml
deploy_job:
  script:
    - echo "API Key: $API_KEY"  # Shows as: API Key: ****
  variables:
    DATABASE_URL: "postgres://localhost"
    ENVIRONMENT: "production"
```

## 📊 Parallel Jobs (Run Multiple Tests at Once)

```yaml
test_job:
  stage: test
  parallel: 4  # Run this job 4 times in parallel
  script:
    - npm test -- --shard=$CI_NODE_INDEX/$CI_NODE_TOTAL
```

**Result:**
```
┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│ Test Job 1  │  │ Test Job 2  │  │ Test Job 3  │  │ Test Job 4  │
│ Shard 1/4   │  │ Shard 2/4   │  │ Shard 3/4   │  │ Shard 4/4   │
│ ⏳ Running   │  │ ⏳ Running   │  │ ⏳ Running   │  │ ⏳ Running   │
└─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘

All running at the same time = 4x faster! 🚀
```

## 🔄 Matrix Builds (Test Multiple Versions)

```yaml
build_job:
  stage: build
  parallel:
    matrix:
      - NODE_VERSION: ["16", "18", "20"]
        OS: ["ubuntu", "macos"]
```

**Creates 6 jobs automatically:**
```
1. Node 16 + Ubuntu  ✅
2. Node 16 + macOS   ✅
3. Node 18 + Ubuntu  ✅
4. Node 18 + macOS   ✅
5. Node 20 + Ubuntu  ✅
6. Node 20 + macOS   ✅

Perfect for testing across platforms!
```

---

# 🛠️ TOOLS & COMMANDS

## 💻 GitLab CLI (glab) - Control GitLab from Terminal

### 📥 Install glab

```bash
# macOS
brew install glab

# Ubuntu/Debian
sudo apt-get install glab

# Windows (Scoop)
scoop install glab

# Verify installation
glab --version
```

### 🔐 Login to GitLab

```bash
# Interactive login
glab auth login
# Follow the prompts

# Non-interactive (with token)
glab auth login --token YOUR_TOKEN

# Check status
glab auth status
```

### 📦 Project Commands

```bash
# List your projects
glab repo list

# View project details
glab repo view

# Clone a project
glab repo clone project-name

# Create a new project
glab repo create my-new-project

# View in browser
glab repo view --web
```

### 📋 Issue Commands

```bash
# Create issue quickly
glab issue create --title "Fix button bug"

# Create with details
glab issue create \
  --title "Dark mode" \
  --description "Add dark theme" \
  --label feature \
  --assignee @me

# List issues
glab issue list

# List only my issues
glab issue list --assigned-to me

# View issue
glab issue view 42

# Close issue
glab issue close 42

# Add comment
glab issue note 42 "Great work!"
```

### 🔀 Merge Request Commands

```bash
# Create MR
glab mr create

# Create with options
glab mr create \
  --title "Add dark mode" \
  --target-branch main \
  --assignee @reviewer

# List MRs
glab mr list

# View MR details
glab mr view 10

# Approve MR
glab mr approve 10

# Merge MR
glab mr merge 10

# Merge with squash (combine all commits)
glab mr merge 10 --squash
```

### ⚙️ Pipeline Commands

```bash
# Run pipeline for current branch
glab ci run

# View pipeline status
glab ci status

# List all pipelines
glab ci list

# View pipeline details
glab ci view PIPELINE_ID

# View job logs
glab ci trace JOB_ID

# Follow logs in real-time
glab ci trace JOB_ID --follow

# Retry failed job
glab ci retry JOB_ID

# Download artifacts
glab ci artifacts download PIPELINE_ID
```

---

## 🌐 REST API (Programmatic Access)

### 🔑 Get Your API Token

```
1. Avatar → Settings
2. Access Tokens
3. Add new token
4. Scopes: api, read_api
5. Copy token
```

### 📝 Basic API Call

```bash
# Get your user info
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/user"

# Pretty print JSON (install jq first)
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/user" | jq
```

### 📚 Common API Examples

**List your projects:**
```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects"
```

**Create an issue:**
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"title":"New bug","description":"Issue details"}' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/issues"
```

**Get commits from main branch:**
```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/repository/commits?ref=main"
```

### 📊 Pagination (Get Lots of Data)

```bash
# Get 50 items per page, page 1
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects?page=1&per_page=50"

# Response headers show:
# X-Total: 200 (total items)
# X-Total-Pages: 4 (total pages)
# X-Page: 1 (current page)
```

---

## 📊 GraphQL API (Modern API)

### 🚀 GraphQL vs REST

```
REST API                GraphQL API
────────────────        ──────────────
Multiple requests   → One request gets exactly
Get extra data      → Only what you need
Hard to document    → Self-documenting
Complex queries     → Simple queries
```

### 🔍 First GraphQL Query

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "query": "{
      currentUser {
        name
        email
        username
      }
    }"
  }' \
  "https://gitlab.com/api/graphql"
```

**Result:**
```json
{
  "data": {
    "currentUser": {
      "name": "Your Name",
      "email": "your@email.com",
      "username": "yourname"
    }
  }
}
```

### 📝 Query Project Info

```graphql
query {
  project(fullPath: "group/project") {
    name
    description
    visibility
    createdAt
  }
}
```

### 📋 Query Issues

```graphql
query {
  project(fullPath: "group/project") {
    issues(first: 10, state: OPENED) {
      edges {
        node {
          id
          title
          state
          author {
            name
          }
        }
      }
    }
  }
}
```

### ✏️ Create Issue (Mutation)

```graphql
mutation {
  createIssue(input: {
    projectPath: "group/project"
    title: "New bug"
    description: "This is a bug"
  }) {
    issue {
      id
      title
    }
  }
}
```

---

## ☸️ Kubernetes Integration (Advanced)

### 🤔 Why Kubernetes?

```
Traditional Deployment        Kubernetes
─────────────────────        ────────────
Buy 1 server            → Automatic load balancing
Deploy your app         → Self-healing containers
Oops, it crashed        → Automatic restart
Scale manually          → Auto-scaling
❌ Complex              → ✅ Automated & scalable
```

### 🚀 Connect GitLab to Kubernetes

```
Step 1: Install GitLab Agent on K8s Cluster
        ↓
Step 2: Create config file (.gitlab/agents/k8s/config.yaml)
        ↓
Step 3: Grant permissions
        ↓
Step 4: Deploy from GitLab CI/CD
        ↓
✅ Automatic deployments!
```

---

# 🔒 SECURITY & BEST PRACTICES

## 🛡️ Access Control (Who Can Do What)

### 👥 Permission Levels

```
┌────────────┬──────────┬────────┬───────┬────────┐
│ Role       │ View     │ Create │ Merge │ Delete │
├────────────┼──────────┼────────┼───────┼────────┤
│ Guest      │ Public   │ No     │ No    │ No     │
│ Reporter   │ All      │ Issues │ No    │ No     │
│ Developer  │ All      │ Code   │ Own   │ Own    │
│ Maintainer │ All      │ All    │ All   │ Own    │
│ Owner      │ All      │ All    │ All   │ All    │
└────────────┴──────────┴────────┴───────┴────────┘
```

### ✅ Best Practices

```
🔐 Protect Main Branch
├─ ✅ Require approvals (2 people)
├─ ✅ Require tests to pass
├─ ✅ Block direct pushes
└─ ✅ Require code owner review

🔑 Manage Access
├─ ✅ Regular audits (monthly)
├─ ✅ Remove inactive users
├─ ✅ Use minimal permissions
└─ ✅ Rotate tokens annually

🚀 Secure Deployments
├─ ✅ Protect production env
├─ ✅ Require approvals
├─ ✅ Use separate accounts
└─ ✅ Monitor all changes
```

## 🔑 Secret Management

### ❌ What NOT to Do

```bash
❌ Hardcode secrets in code
   git commit -m "Add password: abc123"

❌ Commit .env files
   git add .env  ← NEVER DO THIS

❌ Paste API keys in comments
   // API_KEY = "secret123"

❌ Use same password everywhere
   password: "mypassword123"
```

### ✅ What TO Do

```bash
✅ Use GitLab CI/CD Variables
   $API_KEY, $DATABASE_URL, etc.

✅ Use .env.example (no values)
   CONTENT GOES IN SETTINGS

✅ Rotate secrets regularly
   Change passwords every 90 days

✅ Use unique tokens
   Different token per service

✅ Add .env to .gitignore
   echo ".env" >> .gitignore
```

### 🔐 Add Secret Variables

```
Settings → CI/CD → Variables

┌─────────────────────────────┐
│ Key: API_KEY                │
│ Value: ••••••••••••••••     │
│ ☑️  Protect                 │
│ ☑️  Mask                    │
│ [Add variable]              │
└─────────────────────────────┘

✅ Protected = Only on main branch
✅ Masked = Hidden in logs
```

---

## ✅ Daily Checklist

### Before Starting Work
```
⬜ Pull latest code
   git pull origin main

⬜ Create new branch
   git checkout -b feature/name

⬜ No direct work on main
   ❌ Never work on main
   ✅ Always create branch
```

### Before Creating MR
```
⬜ Tests pass locally
   npm test

⬜ Code is formatted
   npm run format

⬜ No secrets in code
   Grep: "password", "key", "token"

⬜ Updated documentation
   Keep README.md fresh

⬜ Clear commit messages
   "Add dark mode" not "fix"
```

### Before Merging
```
⬜ 2+ approvals received
   Ask team to review

⬜ CI/CD pipeline passed
   All tests green ✅

⬜ Code owner approved
   If needed

⬜ No conflicts with main
   Merge without conflicts

⬜ Delete source branch
   Keep repo clean
```

---

## 🎓 Quick Reference Tables

### Git Commands at a Glance

```
📝 MAKING CHANGES
git status          Show what changed
git add .           Stage all changes
git commit -m "msg" Create commit
git push origin br  Upload to GitLab

🔄 BRANCH OPERATIONS
git branch          List branches
git checkout -b new Create & switch
git checkout name   Switch branch
git pull            Download updates

🔀 MERGING & REBASING
git merge branch    Merge into current
git rebase main     Update with latest
git pull --rebase   Pull & rebase

🔍 VIEWING HISTORY
git log             See commits
git diff            See changes
git show COMMIT     See commit details
```

### GitLab CLI Cheat Sheet

```
📦 PROJECTS
glab repo list              List projects
glab repo view              Project details
glab repo clone PROJECT     Clone project

📋 ISSUES
glab issue create           New issue
glab issue list             List issues
glab issue view 42          See issue #42
glab issue update 42        Update issue

🔀 MERGE REQUESTS
glab mr create              New MR
glab mr list                List MRs
glab mr approve 10          Approve MR
glab mr merge 10            Merge MR

⚙️ PIPELINES
glab ci run                 Trigger pipeline
glab ci list                View pipelines
glab ci trace JOB_ID        View job logs
glab ci retry JOB_ID        Retry job
```

### Common File Locations

```
📁 Project Root
├─ 📄 README.md              ← Project documentation
├─ 📄 .gitignore             ← Files to ignore
├─ 📄 .gitlab-ci.yml         ← Pipeline config
├─ 📄 CODEOWNERS             ← Code ownership
├─ 🔧 .gitlab
│  └─ agents
│     └─ k8s-agent           ← K8s config
└─ 📁 src/                   ← Your code
   ├─ main.js
   ├─ utils.js
   └─ styles.css
```

---

## 🎯 Workflow Summary: From Start to Finish

```
┌─────────────────────────────────────────────────────────┐
│ 1️⃣ START A FEATURE                                      │
│  git checkout -b feature/dark-mode                     │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 2️⃣ MAKE CHANGES                                         │
│  • Edit files                                           │
│  • Test locally                                         │
│  • Make commits                                         │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 3️⃣ PUSH TO GITLAB                                      │
│  git push origin feature/dark-mode                     │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 4️⃣ CREATE MERGE REQUEST                                │
│  • Fill in title & description                         │
│  • Assign to reviewer                                  │
│  • Add labels                                          │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 5️⃣ PIPELINE RUNS (AUTOMATIC)                           │
│  🔨 Build → 🧪 Test → 🚀 Deploy (to preview)           │
└─────────────────────────────────────────────────────────┘
                        ↓
        ✅ Pass?        ❌ Fail?
           ↓               ↓
        Continue       Fix issues
           ↓           git push
           ↓               ↓
           └────────┬───────┘
                    ↓
┌─────────────────────────────────────────────────────────┐
│ 6️⃣ CODE REVIEW                                          │
│  👀 Team reviews your code                             │
│  💬 Ask questions, request changes                     │
│  ✅ Eventually approve                                  │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 7️⃣ MERGE TO MAIN                                        │
│  Click [Merge] button                                  │
│  Your code is live! 🎉                                 │
└─────────────────────────────────────────────────────────┘
                        ↓
┌─────────────────────────────────────────────────────────┐
│ 8️⃣ CELEBRATE! 🎉                                        │
│  Your feature is in production                         │
│  Users can see it now                                  │
│  Great job! 🚀                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🆘 Troubleshooting (Common Problems & Solutions)

### 🔴 "Permission Denied" Error

```
❌ Problem:
   git push origin main
   → Permission denied (publickey)

✅ Solution:
   1. Check SSH key is added:
      ssh -T git@gitlab.com
   
   2. If not working, generate new key:
      ssh-keygen -t ed25519 -C "your@email.com"
   
   3. Add public key to GitLab
      Settings → SSH Keys
   
   4. Try again:
      git push origin main
```

### 🔴 "Merge Conflict"

```
❌ Problem:
   You and teammate edited same file
   Git doesn't know which version to keep

✅ Solution:
   1. Pull latest:
      git pull origin main
   
   2. Fix conflicts manually
      Edit the file, choose which version
   
   3. Stage & commit:
      git add .
      git commit -m "Resolve conflicts"
   
   4. Push:
      git push origin your-branch
```

### 🔴 "Pipeline Failed"

```
❌ Problem:
   Your tests failed or build error

✅ Solution:
   1. Click failed job in pipeline
   2. Read error message carefully
   3. Fix code locally
   4. Run tests:
      npm test
   5. Push again:
      git push origin your-branch
   6. Pipeline runs automatically
```

### 🔴 "Can't Merge"

```
❌ Problem:
   Merge button is gray/disabled

Possible reasons:
   ❌ Pipeline still running
      ↳ Wait for ✅ green checkmark
   
   ❌ Not enough approvals
      ↳ Ask more reviewers
   
   ❌ Code owner approval needed
      ↳ Ask @codeowner
   
   ❌ Merge conflicts
      ↳ Resolve conflicts first

✅ Solution:
   Check which requirement failed,
   fix it, then try again!
```

---

## 🎓 Learning Path

### 🟢 Beginner (First Week)
```
Day 1-2:  Learn Git basics
          git clone, add, commit, push
          
Day 3-4:  Create first branch
          Make changes, create MR
          
Day 5:    Get code reviewed
          Learn from feedback
          
Day 6-7:  Merge your changes
          See your code in production!
```

### 🟡 Intermediate (Weeks 2-4)
```
Week 2:   Learn CI/CD pipelines
          Create .gitlab-ci.yml
          Run tests automatically
          
Week 3:   Advanced branching
          Protect main branch
          Use branch rules
          
Week 4:   Team workflows
          Code reviews
          Issue management
```

### 🔴 Advanced (Month 2+)
```
Month 2:  API & automation
          GraphQL queries
          Webhook integration
          
Month 3:  Advanced deployment
          Blue-green deployments
          Kubernetes integration
          
Month 4:  Security
          Secret management
          Access control
```

---

## 💡 Pro Tips & Tricks

### ⭐ Tip #1: Use Aliases

```bash
# Add to ~/.bashrc or ~/.zshrc
alias gs='git status'
alias gc='git commit -m'
alias gp='git push'
alias gpl='git pull'
alias gb='git branch'

# Now type:
gs              # Instead of: git status
gc "Fix bug"    # Instead of: git commit -m "Fix bug"
gp              # Instead of: git push
```

### ⭐ Tip #2: Save Credentials

```bash
# Don't type password every time
git config --global credential.helper store

# Or for more security:
git config --global credential.helper cache
```

### ⭐ Tip #3: Atomic Commits

```bash
# Bad: One commit for everything
git commit -m "Fixed bug, added feature, updated docs"

# Good: Separate logical changes
git commit -m "🐛 Fix login button"
git commit -m "✨ Add dark mode"
git commit -m "📝 Update docs"

Benefits: ✅ Easier to review, revert, understand
```

### ⭐ Tip #4: Meaningful Commit Messages

```
❌ Bad:
"fix"
"update"
"lol"

✅ Good:
"🐛 Fix mobile layout on button"
"✨ Add dark mode toggle to settings"
"📝 Update API documentation"
"🚀 Optimize image loading"

Format: [emoji] [action] [detail]
```

### ⭐ Tip #5: Use Emoji in Messages

```
🐛 Bug fix
✨ New feature
📝 Documentation
🚀 Performance
🔒 Security
🧪 Tests
🔄 Refactor
🎨 UI/Style changes
⚙️ Configuration
🗑️ Remove code
```

---

## 📚 Additional Resources

### 📖 Official Docs
```
📘 GitLab Documentation
   https://docs.gitlab.com

🔧 GitLab CLI (glab)
   https://docs.gitlab.com/cli

🌐 REST API
   https://docs.gitlab.com/api

📊 GraphQL API
   https://docs.gitlab.com/graphql
```

### 🎥 Learning Resources
```
YouTube: "GitLab Tutorial"
Udemy: GitLab courses
Coursera: DevOps with GitLab
LinkedIn Learning: Git & GitLab
```

### 👥 Community
```
💬 GitLab Forum: forum.gitlab.com
📞 Stack Overflow: tag "gitlab"
🐦 Twitter: @gitlab
💬 Discord: GitLab community
```

---

## 🎯 Summary Checklist

### ✅ You've Learned:
- [ ] Create GitLab account
- [ ] Generate SSH key
- [ ] Clone repository
- [ ] Create branches
- [ ] Make commits
- [ ] Create merge requests
- [ ] Code review process
- [ ] Pipelines & CI/CD
- [ ] Issue tracking
- [ ] Secret management
- [ ] Team workflows
- [ ] Deployment basics

### 🎓 Next Steps:
1. **Create a real project**
2. **Invite a friend to review code**
3. **Set up a pipeline**
4. **Deploy something real**
5. **Learn from mistakes**
6. **Help others learn**

---

# 🎉 You're Ready!

```
    You now know GitLab! 🦊
    
┌──────────────────────────────┐
│                              │
│   Keep practicing!           │
│   Make projects!             │
│   Help your team!            │
│                              │
│   You've got this! 💪        │
│                              │
└──────────────────────────────┘

Questions? 
→ Check docs: docs.gitlab.com
→ Ask community: forum.gitlab.com
→ Google it! 🔍

Happy coding! 🚀
```

---

**Last Updated**: December 2025  
**Perfect For**: Beginners to Intermediate Users  
**Time to Learn**: 4 weeks (practicing daily)  
**Difficulty**: ⭐⭐ Easy to Medium

---

## 📊 Visual Legend

```
Symbols Used:
✅ = Do this
❌ = Don't do this
⚠️  = Warning
💡 = Tip
📌 = Important
🔑 = Security
⏱️  = Time-related
📝 = Documentation
🚀 = Performance/Deploy
🐛 = Bug
✨ = New feature
```

---

Made with ❤️ for developers who want to learn GitLab easily! 🦊
