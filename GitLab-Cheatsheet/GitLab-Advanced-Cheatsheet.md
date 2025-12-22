# GitLab Complete Cheatsheet: From Beginner to Advanced (Extended Edition)

A comprehensive guide covering GitLab Web UI, CLI (glab), REST & GraphQL APIs, and advanced features for developers and DevOps professionals.

---
**"Made With ❤️ from [@mannankazi](https://github.com/mannankazi)"**
## Table of Contents
1. [Getting Started with GitLab](#getting-started)
2. [Authentication & Setup](#authentication--setup)
3. [Project & Repository Management](#project--repository-management)
4. [Branching & Merging](#branching--merging)
5. [Issues & Project Management](#issues--project-management)
6. [Merge Requests (MR)](#merge-requests)
7. [CI/CD Pipelines](#cicd-pipelines)
8. [Advanced CI/CD Configuration](#advanced-cicd-configuration)
9. [GitLab REST API](#gitlab-rest-api)
10. [GitLab GraphQL API](#gitlab-graphql-api)
11. [GitLab CLI (glab) Commands](#gitlab-cli-glab-commands)
12. [Groups & Permissions](#groups--permissions)
13. [Advanced Deployment Strategies](#advanced-deployment-strategies)
14. [Kubernetes Integration](#kubernetes-integration)
15. [Advanced Features](#advanced-features)
16. [Security & Best Practices](#security--best-practices)

---

## GETTING STARTED

### What is GitLab?
GitLab is a complete DevOps platform that allows teams to collaborate on code, manage CI/CD pipelines, and deploy applications. It combines source code management, issue tracking, CI/CD automation, and deployment tools in one platform.

### Available Tiers
- **Community Edition (Free)**: Basic features for individual developers and small teams
- **Paid (Premium/Ultimate)**: Advanced security, compliance, and enterprise features
- **Self-Managed**: Host GitLab on your own servers for maximum control

### Key Concepts
- **Project**: A repository containing your code and configurations
- **Group**: An organization unit containing multiple projects
- **Repository**: Git-based storage for your code files
- **Branch**: Parallel development path within a repository
- **Merge Request (MR)**: Proposal to integrate changes from one branch to another
- **Pipeline**: Automated workflow for testing, building, and deploying code
- **Runner**: Compute resource that executes CI/CD jobs
- **Environment**: Deployment target (development, staging, production)
- **Artifact**: Build output or compiled code stored by pipeline jobs

---

## AUTHENTICATION & SETUP

### Web UI Authentication

#### Log In to GitLab
1. Visit `https://gitlab.com` (for cloud) or your self-hosted instance URL
2. Click **Sign in**
3. Enter credentials or use OAuth/SSO options
4. Complete 2FA if enabled

#### Create a Personal Access Token (Web UI)
1. Click your **Avatar** (top-right) → **Settings**
2. Select **Access Tokens** (left sidebar)
3. Click **Add new token**
4. Enter **Token name**
5. Select **Scopes** (api, read_api, write_repository, etc.)
6. Set **Expiration date** (recommended)
7. Click **Create personal access token**
8. **Copy and save** the token securely

#### Generate SSH Keys (Web UI)
1. Go to **Settings** → **SSH Keys**
2. Click **Add new SSH key**
3. Paste your public key (from `cat ~/.ssh/id_rsa.pub`)
4. Click **Add key**

### API Token Scopes (Advanced)

Limit token permissions using scopes:

| Scope | Permissions | Use Case |
|-------|-------------|----------|
| `api` | Full API access (read/write) | General automation |
| `read_api` | Read-only API access | Monitoring, reporting |
| `read_user` | Read user profile | Basic authentication |
| `read_repository` | Clone/fetch private repos via HTTPS | CI/CD pull only |
| `write_repository` | Push code to private repos via HTTPS | CI/CD deployment |
| `read_registry` | Read container registry images | Image pulling |
| `write_registry` | Push container registry images | Image publishing |
| `sudo` | Admin impersonation (admin only) | Advanced administration |
| `openid` | OpenID Connect authentication | SSO/OIDC |

**Best Practice**: Use minimal scopes per token (principle of least privilege).

### CLI (glab) Installation & Authentication

#### Install glab
```bash
# macOS (Homebrew)
brew install glab

# Linux (using apt on Ubuntu/Debian)
sudo apt-get install glab

# Windows (Scoop)
scoop install glab

# Docker
docker run --rm -it gitpod/workspace-full glab
```

#### Authenticate with glab
```bash
# Interactive authentication
glab auth login

# Non-interactive with token
glab auth login --token YOUR_TOKEN

# For self-hosted GitLab
glab auth login --hostname gitlab.example.com

# Verify authentication
glab auth status
```

#### Configure Multiple Hosts
```bash
# Login to secondary host
glab auth login -h gitlab.company.com --token TOKEN

# Switch default host
glab config set gitlab.com

# Use specific host for command
glab -h gitlab.company.com repo list
```

---

## PROJECT & REPOSITORY MANAGEMENT

### Creating Projects (Web UI)

#### Create a New Project
1. Click **+** icon (top-left) → **New project**
2. Select **Create blank project**
3. Enter **Project name**
4. Choose **Visibility** (Private/Internal/Public)
5. Click **Create project**

#### Import Existing Repository
1. Click **+** → **New project** → **Import project**
2. Select source (GitHub, Bitbucket, etc.)
3. Authenticate with source
4. Select repository to import
5. Configure project details
6. Click **Import**

### Project Management (Web UI)

#### Access Project Settings
1. Go to **Settings** (left sidebar)
2. **General**: Rename, transfer, archive project
3. **Repository**: Default branch, mirroring, protected branches
4. **CI/CD**: Variables, runners, schedules
5. **Merge requests**: Approval rules, templates
6. **Integrations**: Webhooks, third-party services

#### Change Project Visibility
1. **Settings** → **General**
2. Expand **Visibility, project features, permissions**
3. Select **Project visibility** (Private/Internal/Public)
4. Click **Save changes**

### Cloning & Remote Management

#### Clone Repository (Web UI/CLI)
```bash
# Via Web UI: Click Clone button, copy HTTPS/SSH URL
git clone https://gitlab.com/owner/project.git
git clone git@gitlab.com:owner/project.git

# Via CLI
glab repo clone owner/project
glab repo clone https://gitlab.com/owner/project.git
```

#### Manage Multiple Remotes
```bash
# Add additional remote
git remote add upstream https://gitlab.com/upstream/project.git

# View all remotes
git remote -v

# Fetch from all remotes
git fetch --all

# Push to specific remote
git push origin main
git push upstream main
```

---

## BRANCHING & MERGING

### Branch Management (Web UI)

#### Create a New Branch
1. Go to **Repository** → **Branches**
2. Click **New branch**
3. Enter **Branch name**
4. Select **Create from** (which branch to base it on)
5. Click **Create branch**

#### Branch Protection Rules (Web UI)

1. **Settings** → **Repository** → **Branch rules**
2. Click **Add branch rule**
3. Configure:
   - **Branch name or pattern**: Exact name or wildcard (e.g., `main`, `release-*`)
   - **Allowed to push**: Who can push (No one/Developers/Maintainers)
   - **Allowed to merge**: Who can merge (Developers/Maintainers)
   - **Require approvals**: Number of MR approvals needed
   - **Code owner approvals**: Require CODEOWNERS review
   - **Status checks**: Require pipeline to pass
4. Click **Create branch rule**

#### Branch Management (CLI)
```bash
glab repo branch list
glab repo branch list --remote
glab repo branch create new-feature
glab repo branch create new-feature --source=develop
glab repo branch delete branch-name
```

---

## ISSUES & PROJECT MANAGEMENT

### Creating & Managing Issues (Web UI)

#### Create an Issue
1. Go to **Issues** (left sidebar)
2. Click **New issue**
3. Enter **Title** and **Description**
4. Configure: **Assignee**, **Labels**, **Milestone**, **Due date**
5. Click **Create issue**

#### Link Issues to Commits/MRs
- In **Commit message**: Type `#issue-number`
- Use closing keywords: `Closes #issue-number`, `Fixes #issue-number`
- Auto-closes issue when MR is merged

#### Quick Actions in Issues
```
/assign @username          - Assign user
/unassign @username        - Unassign user
/milestone v1.0            - Set milestone
/label bug, feature        - Add labels
/unlabel bug               - Remove label
/due YYYY-MM-DD            - Set due date
/weight 5                  - Set weight (if enabled)
/subscribe                 - Get notifications
/unsubscribe               - Stop notifications
/relate #issue-number      - Link related issue
/close                     - Close issue
/reopen                    - Reopen issue
/move owner/project        - Move to another project
/estimate 3h               - Set time estimate
/spend 2h                  - Log time spent
```

### Advanced Issue Management

#### Bulk Operations on Issues
```bash
# List issues with filters
glab issue list --assigned-to me
glab issue list --state closed
glab issue list --label bug --label security
glab issue list --search "login"
glab issue list --milestone v1.0

# Update multiple issues
glab issue update 42 --title "New title"
glab issue update 42 --state closed
glab issue update 42 --assignee @user
```

### Labels & Milestones (Web UI)

#### Create Labels
1. **Labels** (left sidebar, under Issues)
2. Click **New label**
3. Enter **Title** and choose **Color**
4. Click **Create label**

#### Create Milestones
1. **Milestones** (left sidebar, under Issues)
2. Click **New milestone**
3. Enter **Milestone name**
4. Set **Start date** and **Due date**
5. Click **Create milestone**

---

## MERGE REQUESTS

### Creating Merge Requests (Web UI)

#### Create MR from Branch
1. After pushing changes: Click **Create merge request** (notification banner)
2. Or go to **Merge requests** → **New merge request**
3. Select **Source branch** (your feature branch)
4. Select **Target branch** (usually `main`/`develop`)
5. Click **Compare branches**
6. Fill in **Title**, **Description**, **Assignee**, **Labels**
7. Click **Create merge request**

#### Link Issues to MR
In **Description**, use: `Closes #issue-number`

#### Draft MRs
- Click **Mark as draft** to prevent accidental merging
- Click **Mark as ready** when complete

### Advanced MR Features

#### Merge Request Approvals
```yaml
# In .gitlab-ci.yml for approval rules
deployment_approvals:
  stage: deploy
  needs:
    - job: test_job
      optional: false  # Must complete before this job can run
  when: manual  # Requires manual trigger
  environment:
    name: production
```

#### MR Dependencies
```bash
# View MR discussion
glab mr discussion 10

# Add review
glab mr review 10

# Review changes
glab mr diff 10

# Approve MR
glab mr approve 10
glab mr approve 10 --comment "Looks good!"

# Merge MR
glab mr merge 10
glab mr merge 10 --squash
glab mr merge 10 --delete-branch
```

---

## CI/CD PIPELINES

### .gitlab-ci.yml Basics

#### Create Pipeline Configuration
```yaml
# Basic pipeline structure
stages:
  - build
  - test
  - deploy

variables:
  DOCKER_DRIVER: overlay2

build_job:
  stage: build
  script:
    - echo "Building application..."
    - npm install
    - npm run build
  artifacts:
    paths:
      - build/
    expire_in: 1 week
  only:
    - merge_requests
    - main

test_job:
  stage: test
  script:
    - npm test
  coverage: '/Coverage: \d+\.\d+%/'
  allow_failure: false

deploy_job:
  stage: deploy
  script:
    - ./deploy.sh
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

### Key Pipeline Concepts

#### Stages
```yaml
stages:
  - build       # Runs first
  - test        # Runs after build
  - deploy      # Runs after test
```

#### Triggers & Conditions

**Rules** (Modern approach - replaces only/except):
```yaml
job:
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
      when: always
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
      when: always
    - when: never
```

#### Workflows (Control entire pipeline)
```yaml
workflow:
  rules:
    - if: $CI_COMMIT_BRANCH && $CI_OPEN_MERGE_REQUESTS
      when: never
    - if: $CI_COMMIT_BRANCH
      when: always
  auto_cancel:
    on_new_commit: interruptible
    on_job_failure: all
```

### Cache & Artifacts

#### Cache (Persist between jobs)
```yaml
job:
  cache:
    key: ${CI_COMMIT_REF_SLUG}
    paths:
      - node_modules/
      - .m2/
  script:
    - npm install
```

#### Artifacts (Store job outputs)
```yaml
build_job:
  artifacts:
    paths:
      - build/
      - dist/
    name: "build-$CI_COMMIT_SHA"
    expire_in: 30 days
    when: always  # Save even if job fails

test_job:
  dependencies:
    - build_job  # Download artifacts from build_job
  script:
    - npm test
```

#### Artifact Reports
```yaml
test_job:
  script:
    - npm test
  artifacts:
    reports:
      junit: test-results.xml
      coverage_report:
        coverage_format: cobertura
        path: coverage/cobertura-coverage.xml
      dotenv: env.txt  # Load variables from file
```

### CI/CD Variables

#### Add CI/CD Variables (Web UI)
1. **Settings** → **CI/CD** → **Variables**
2. Click **Add variable**
3. Enter **Key** and **Value**
4. Select options:
   - **Protect variable**: Only on protected branches
   - **Mask variable**: Hide in logs
5. Click **Add variable**

#### Use Variables in Pipeline
```yaml
job:
  script:
    - echo $MY_VARIABLE
    - echo "$USERNAME:$PASSWORD"
  variables:
    LOCAL_VAR: "local value"
```

#### Predefined CI/CD Variables
```
$CI_COMMIT_SHA          - Full commit hash
$CI_COMMIT_SHORT_SHA    - Short commit hash
$CI_COMMIT_REF_NAME     - Branch/tag name
$CI_COMMIT_BRANCH       - Branch name (if commit is on branch)
$CI_COMMIT_MESSAGE      - Full commit message
$CI_COMMIT_TITLE        - First line of commit message
$CI_PIPELINE_ID         - Unique pipeline ID
$CI_PIPELINE_IID        - Pipeline number
$CI_PIPELINE_SOURCE     - How pipeline was triggered
$CI_JOB_ID              - Unique job ID
$CI_JOB_NAME            - Job name
$CI_JOB_STATUS          - Job status (success/failed)
$CI_MERGE_REQUEST_IID   - MR number
$CI_MERGE_REQUEST_TITLE - MR title
$CI_PROJECT_NAME        - Project name
$CI_PROJECT_PATH        - Group/project path
$CI_PROJECT_NAMESPACE   - Project namespace
$GITLAB_USER_LOGIN      - User who triggered pipeline
$GITLAB_USER_EMAIL      - Triggering user's email
$CI_NODE_INDEX          - Parallel job index (starts at 1)
$CI_NODE_TOTAL          - Total parallel job count
$CI_ENVIRONMENT_NAME    - Environment name
$CI_ENVIRONMENT_SLUG    - URL-safe environment name
```

---

## ADVANCED CI/CD CONFIGURATION

### Parallel Jobs & Matrix Builds

#### Basic Parallel Jobs
```yaml
test:
  stage: test
  parallel: 5  # Run 5 instances of this job
  script:
    - npm test --shard=$CI_NODE_INDEX/$CI_NODE_TOTAL
```

#### Parallel Matrix Builds
```yaml
build:
  stage: build
  parallel:
    matrix:
      - PYTHON_VERSION: ["3.8", "3.9", "3.10", "3.11"]
        OS: ["ubuntu", "fedora"]
        # Creates 8 jobs (4 versions × 2 OSes)
  image: python:${PYTHON_VERSION}
  script:
    - echo "Building for $OS with Python $PYTHON_VERSION"
    - python setup.py build
  tags:
    - $OS  # Tags must match runner configuration
```

#### Matrix with Multiple Variables
```yaml
deploy:
  stage: deploy
  parallel:
    matrix:
      - ENVIRONMENT: ["dev", "staging"]
        REGION: ["us-east", "eu-west"]
        # Creates 4 jobs
  script:
    - echo "Deploying to $ENVIRONMENT in $REGION"
    - ./deploy.sh $ENVIRONMENT $REGION
```

#### Reference !reference Keyword (Reduce Duplication)
```yaml
.common_script:
  script:
    - npm install
    - npm test

build:
  script:
    - !reference [.common_script, script]
    - npm run build

test:
  extends: .common_script

deploy:
  script:
    - !reference [.common_script, script]
    - npm run deploy
```

### Job Dependencies & Ordering

#### Using `needs` (Ignore stage ordering)
```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script: echo "Building..."

test-fast:
  stage: test
  needs: ["build"]  # Run immediately after build, skip stage wait
  script: echo "Testing..."

test-slow:
  stage: test
  needs: ["build"]
  script: sleep 10 && echo "Testing..."

deploy:
  stage: deploy
  needs:
    - job: test-fast
      optional: false  # This job MUST run
    - job: test-slow
      optional: true   # This job is OPTIONAL
  script: echo "Deploying..."
```

#### DAG Pipelines (Directed Acyclic Graph)
```yaml
# Complex dependency chain
build:
  stage: build
  script: echo "Build"

unit-test:
  stage: test
  needs: ["build"]
  script: echo "Unit tests"

integration-test:
  stage: test
  needs: ["build"]
  script: echo "Integration tests"

deploy-staging:
  stage: deploy
  needs: ["unit-test", "integration-test"]
  script: echo "Deploy to staging"

deploy-production:
  stage: deploy
  needs: ["deploy-staging"]
  when: manual
  script: echo "Deploy to production"
```

### Advanced Job Configuration

#### Job Retries with Conditions
```yaml
job:
  retry:
    max: 3  # Retry up to 3 times
    when:
      - runner_system_failure
      - api_failure
      - stuck_or_timeout_failure
      - scheduler_failure
      - unknown_failure
  script:
    - echo "This job will retry on specific failures"
```

#### Exit Code-Based Retries
```yaml
job:
  retry:
    max: 3
    exit_codes:
      - 137  # SIGKILL
      - 2    # Custom exit code
  script:
    - ./flaky-script.sh
```

#### Job Timeouts
```yaml
job:
  timeout: 1 hour  # Job timeout
  script:
    - echo "This job will timeout after 1 hour"
```

#### When Conditions
```yaml
job:
  when: always       # Always run
  when: on_success   # Only if previous jobs succeeded
  when: on_failure   # Only if previous jobs failed
  when: never        # Never run
  when: manual       # Manual trigger required
  when: delayed      # Delayed execution
  start_in: 1 hour   # Used with 'when: delayed'
```

#### Job Interruptibility
```yaml
job:
  interruptible: true   # Cancel when newer pipeline starts
  script:
    - npm test
```

### File Inclusion & Imports

#### Local File Inclusion
```yaml
include:
  - local: .gitlab/ci/build.yml
  - local: .gitlab/ci/test.yml
  - local: .gitlab/ci/deploy.yml
```

#### Include from Another Project
```yaml
include:
  - project: 'my-group/my-project'
    ref: 'main'
    file: 'templates/.gitlab-ci-template.yml'
```

#### Include with Conditions
```yaml
include:
  - local: builds.yml
    rules:
      - if: $INCLUDE_BUILDS == "true"
      
  - local: builds.yml
    rules:
      - exists:
          - Dockerfile
      
  - local: builds.yml
    rules:
      - changes:
          paths:
            - Dockerfile
          compare_to: 'refs/heads/main'
```

#### Include from Remote URL
```yaml
include:
  - remote: 'https://gitlab.com/awesome-project/raw/main/.gitlab-ci.yml'
```

### YAML Anchors (DRY Configuration)

#### Basic Anchors
```yaml
.shared_config: &shared
  image: node:18
  cache:
    paths:
      - node_modules/

build:
  <<: *shared
  stage: build
  script: npm install && npm run build

test:
  <<: *shared
  stage: test
  script: npm test
```

#### Anchor Limitations
⚠️ **Note**: YAML anchors DON'T work across included files. Use `!reference` instead:

```yaml
# This works within the same file:
.template: &my_template
  image: node:18

job:
  <<: *my_template

# This DOESN'T work across files (anchors are document-scoped):
include:
  - local: template.yml

job:
  <<: *my_template  # ERROR: Unknown alias
```

**Solution**: Use `!reference` for cross-file inclusion:
```yaml
include:
  - local: template.yml

job:
  image: !reference [.template, image]
```

### Container Registry & Docker

#### Build Docker Images
```yaml
build_docker:
  stage: build
  image: docker:24
  services:
    - docker:24-dind
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - docker push $CI_REGISTRY_IMAGE:latest
  only:
    - main
```

#### Use Docker Image from Registry
```yaml
job:
  image: $CI_REGISTRY_IMAGE:latest
  script:
    - ./my-script.sh
```

### Pipeline Schedules

#### Create Scheduled Pipeline (Web UI)
1. **CI/CD** → **Schedules**
2. Click **New schedule**
3. Enter **Description**
4. Set **Cron syntax** (e.g., `0 2 * * 0` = Sunday 2 AM)
5. Select **Branch** and **Variables**
6. Click **Create pipeline schedule**

#### Common Cron Expressions
```
0 2 * * *       - Daily at 2 AM
0 * * * *       - Every hour
0 0 * * 0       - Weekly (Sunday midnight)
0 0 1 * *       - Monthly (1st of month)
0 0 1 0 *       - Yearly (Jan 1st)
*/15 * * * *    - Every 15 minutes
```

---

## GITLAB REST API

### Authentication Methods

#### Personal Access Token
```bash
curl --header "PRIVATE-TOKEN: YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects"

# Or using Authorization header
curl --header "Authorization: Bearer YOUR_TOKEN" \
  "https://gitlab.com/api/v4/projects"
```

#### OAuth 2.0 Token
```bash
curl --url "https://gitlab.com/api/v4/projects?access_token=YOUR_TOKEN"
```

#### Sudo (Admin Impersonation)
```bash
# As admin, execute request as another user
curl --header "PRIVATE-TOKEN: ADMIN_TOKEN" \
  --header "Sudo: username" \
  "https://gitlab.com/api/v4/user"

# Or using query parameter
curl "https://gitlab.com/api/v4/user?private_token=ADMIN_TOKEN&sudo=123"
```

### Project Management API

#### List Projects
```bash
# All projects
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects"

# With filtering
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects?owned=true&archived=false&order_by=last_activity_at"

# Pagination
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects?page=2&per_page=50"
```

#### Get Project Details
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID"

# Or by path
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/group%2Fproject"
```

#### Create Project
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"name":"new-project","visibility":"private"}' \
  "https://gitlab.com/api/v4/projects"
```

#### Update Project
```bash
curl --request PUT \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"description":"Updated description"}' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID"
```

### Issues API

#### Create Issue
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "title":"New Issue",
    "description":"Issue description",
    "assignee_ids":[1,2],
    "labels":"bug,urgent",
    "milestone_id":1
  }' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/issues"
```

#### List Issues
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/issues?state=opened&labels=bug"
```

#### Get Single Issue
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/issues/ISSUE_IID"
```

#### Update Issue
```bash
curl --request PUT \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"state_event":"close"}' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/issues/ISSUE_IID"
```

### Merge Requests API

#### List Merge Requests
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/merge_requests?state=opened"
```

#### Create Merge Request
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "source_branch":"feature-branch",
    "target_branch":"main",
    "title":"Add new feature",
    "assignee_ids":[1]
  }' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/merge_requests"
```

#### Approve Merge Request
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/merge_requests/MR_IID/approve"
```

#### Merge Merge Request
```bash
curl --request PUT \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"should_remove_source_branch":true}' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/merge_requests/MR_IID/merge"
```

### Pipelines API

#### Trigger Pipeline
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  --form "ref=main" \
  --form "token=TRIGGER_TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/trigger/pipeline"
```

#### List Pipelines
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/pipelines"
```

#### Get Pipeline Details
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/pipelines/PIPELINE_ID"
```

#### List Pipeline Jobs
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/pipelines/PIPELINE_ID/jobs"
```

### Repositories API

#### List Branches
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/repository/branches"
```

#### Create Branch
```bash
curl --request POST \
  --header "PRIVATE-TOKEN: TOKEN" \
  --header "Content-Type: application/json" \
  --data '{"branch":"new-branch","ref":"main"}' \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/repository/branches"
```

#### Get Commits
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects/PROJECT_ID/repository/commits?ref=main"
```

### Users API

#### Get Current User
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/user"
```

#### List Users
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/users"
```

#### Get Specific User
```bash
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/users/USER_ID"
```

### Pagination

#### Handle Pagination
```bash
# Page 1, 20 items per page
curl --header "PRIVATE-TOKEN: TOKEN" \
  "https://gitlab.com/api/v4/projects?page=1&per_page=20"

# Response headers include:
# X-Page: current page
# X-Per-Page: items per page
# X-Total: total items
# X-Total-Pages: total pages
# Link: next/prev/last links (RFC 5988)
```

#### Use Link Header for Pagination
```bash
# The Link header provides next/prev URLs
Link: <https://gitlab.com/api/v4/projects?page=2&per_page=20>; rel="next", \
      <https://gitlab.com/api/v4/projects?page=1&per_page=20>; rel="prev", \
      <https://gitlab.com/api/v4/projects?page=5&per_page=20>; rel="last"
```

---

## GITLAB GRAPHQL API

### Query Basics

#### Simple Query
```graphql
query {
  currentUser {
    name
    email
    username
  }
}
```

#### Query with Variables
```bash
curl --request POST \
  --url "https://gitlab.com/api/graphql" \
  --header "Authorization: Bearer YOUR_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "query": "query($fullPath: ID!) { project(fullPath: $fullPath) { name } }",
    "variables": {"fullPath": "group/project"}
  }'
```

### Project Queries

#### Get Project Details
```graphql
query {
  project(fullPath: "group/project") {
    name
    description
    visibility
    createdAt
    defaultBranch
  }
}
```

#### List Project Issues
```graphql
query {
  project(fullPath: "group/project") {
    issues(first: 10) {
      edges {
        node {
          id
          iid
          title
          state
          createdAt
        }
      }
      pageInfo {
        endCursor
        hasNextPage
      }
    }
  }
}
```

#### List Project Merge Requests
```graphql
query {
  project(fullPath: "group/project") {
    mergeRequests(first: 10, state: OPENED) {
      edges {
        node {
          id
          iid
          title
          sourceBranch
          targetBranch
          state
          approvedBy {
            nodes {
              username
            }
          }
        }
      }
    }
  }
}
```

### Mutations

#### Create Issue
```graphql
mutation {
  createIssue(input: {
    projectPath: "group/project"
    title: "New Issue"
    description: "Issue description"
    assigneeIds: ["gid://gitlab/User/1", "gid://gitlab/User/2"]
    labelIds: ["gid://gitlab/Label/1"]
  }) {
    issue {
      id
      iid
      title
    }
    errors
  }
}
```

#### Update Issue
```graphql
mutation {
  updateIssue(input: {
    id: "gid://gitlab/Issue/123"
    title: "Updated title"
    stateEvent: CLOSED
  }) {
    issue {
      id
      title
      state
    }
    errors
  }
}
```

#### Create Note (Comment)
```graphql
mutation {
  createNote(input: {
    noteableId: "gid://gitlab/Issue/123"
    body: "This is a comment"
  }) {
    note {
      id
      body
      author {
        username
      }
    }
    errors
  }
}
```

#### Approve Merge Request
```graphql
mutation {
  mergeRequestSetApproval(input: {
    projectPath: "group/project"
    mergeRequestIid: "1"
    approved: true
  }) {
    mergeRequest {
      id
      approved
    }
    errors
  }
}
```

### Pagination in GraphQL

#### Cursor-Based Pagination
```graphql
query {
  project(fullPath: "group/project") {
    issues(first: 5) {  # Get first 5
      edges {
        cursor
        node {
          title
        }
      }
      pageInfo {
        endCursor
        hasNextPage
        hasPreviousPage
      }
    }
  }
}

# Next page using cursor
query {
  project(fullPath: "group/project") {
    issues(first: 5, after: "CURSOR_VALUE") {
      edges {
        node {
          title
        }
      }
    }
  }
}
```

### Introspection Queries

#### Explore Schema
```graphql
query {
  __schema {
    types {
      name
      kind
      description
    }
  }
}
```

#### Get Type Details
```graphql
query {
  __type(name: "Issue") {
    name
    kind
    fields {
      name
      type {
        name
      }
    }
  }
}
```

---

## GITLAB CLI (glab) COMMANDS

### Project Commands

#### List Projects
```bash
glab repo list
glab repo list --archived
glab repo list --owned
glab repo list --public
glab repo list --search my-project
glab repo list --sort stars
glab repo list --order desc
```

#### View Project Details
```bash
glab repo view
glab repo view owner/project
glab repo view --web
```

#### Clone Repository
```bash
glab repo clone project-name
glab repo clone owner/project-name
glab repo clone https://gitlab.com/owner/project.git
```

### Issue Commands

#### Create Issue
```bash
glab issue create
glab issue create --title "Fix login bug"
glab issue create --title "Feature" --description "Add dark mode"
glab issue create --assignee @user --label bug --milestone v1.0
glab issue create --due-date 2024-12-31
```

#### List Issues
```bash
glab issue list
glab issue list --assigned-to me
glab issue list --state closed
glab issue list --label bug --label security
glab issue list --search "login"
glab issue list --milestone v1.0
```

#### View Issue
```bash
glab issue view 42
glab issue view 42 --web
```

#### Update Issue
```bash
glab issue update 42 --title "New title"
glab issue update 42 --state closed
glab issue update 42 --assignee @user
glab issue update 42 --label new-label
```

#### Comment on Issue
```bash
glab issue note 42 "Great work!"
```

### Merge Request Commands

#### Create MR
```bash
glab mr create
glab mr create --title "Add feature X"
glab mr create --target-branch develop
glab mr create --assignee @reviewer --labels enhancement
glab mr create --source-branch feature --target-branch main
```

#### List MRs
```bash
glab mr list
glab mr list --state opened
glab mr list --assigned-to me
glab mr list --assignee @user
glab mr list --milestone v1.0
```

#### View MR
```bash
glab mr view 10
glab mr view 10 --web
glab mr view 10 --changes
```

#### Approve MR
```bash
glab mr approve 10
glab mr approve 10 --comment "Looks good!"
```

#### Merge MR
```bash
glab mr merge 10
glab mr merge 10 --squash
glab mr merge 10 --delete-branch
```

### Pipeline Commands

#### Trigger Pipeline
```bash
glab ci run
glab ci run -b develop
glab ci run --variables KEY:value,API:token
```

#### View Pipelines
```bash
glab ci list
glab ci list --status failed
glab ci list --branch main
glab ci list --limit 20
```

#### Get Pipeline Status
```bash
glab ci status
glab ci view PIPELINE_ID
glab ci view PIPELINE_ID --web
```

#### View Job Logs
```bash
glab ci trace JOB_ID
glab ci trace JOB_ID --follow
glab ci trace JOB_ID --tail 50
```

#### Retry Job
```bash
glab ci retry JOB_ID
glab ci retry PIPELINE_ID  # Retry all failed jobs
```

#### Download Artifacts
```bash
glab ci artifacts download PIPELINE_ID
glab ci artifacts download PIPELINE_ID --job-name test
glab ci artifacts list PIPELINE_ID
```

### Release Commands

#### Create Release
```bash
glab release create v1.0.0
glab release create v1.0.0 --notes "New features and fixes"
glab release create v1.0.0 --asset my-app.zip
```

#### List Releases
```bash
glab release list
glab release view v1.0.0
```

---

## GROUPS & PERMISSIONS

### Group Management (Web UI)

#### Create a Group
1. Click **+** icon → **New group**
2. Enter **Group name**
3. Set **Group slug** (URL identifier)
4. Choose **Visibility** (Private/Internal/Public)
5. Click **Create group**

#### Add Members to Group
1. Go to **Group** → **Members**
2. Click **Invite members**
3. Search for users
4. Select **Role**:
   - **Guest**: Minimal access
   - **Reporter**: Can view and report issues
   - **Developer**: Can commit code
   - **Maintainer**: Can manage group/projects
   - **Owner**: Full control
5. Click **Invite**

### Permissions Model

#### Permission Levels
| Role | Issues | Code | Merge | Admin |
|------|--------|------|-------|-------|
| **Guest** | View public | No | No | No |
| **Reporter** | Create | View | No | No |
| **Developer** | Create | Commit | Create | No |
| **Maintainer** | Manage | Full | Approve | Limited |
| **Owner** | Manage | Full | Full | Full |

---

## ADVANCED DEPLOYMENT STRATEGIES

### Blue-Green Deployment

#### Concept
- **Blue**: Current production environment
- **Green**: New deployment environment
- Switch traffic instantly between environments
- Zero downtime, easy rollback

#### Implementation in .gitlab-ci.yml
```yaml
deploy_green:
  stage: deploy
  script:
    - echo "Deploying to green environment..."
    - ./deploy.sh green
  environment:
    name: production-green
    url: https://green.example.com
  only:
    - main
  when: manual

switch_to_green:
  stage: deploy
  script:
    - echo "Switching traffic from blue to green..."
    - ./switch-traffic.sh green
  environment:
    name: production
    url: https://example.com
    deployment_tier: production
  needs: ["deploy_green"]
  only:
    - main
  when: manual

rollback_to_blue:
  stage: deploy
  script:
    - echo "Rolling back to blue..."
    - ./switch-traffic.sh blue
  environment:
    name: production
    action: rollback
  needs: ["switch_to_green"]
  when: manual
```

### Canary Deployment

#### Concept
- Deploy new version to small percentage of users
- Monitor metrics and user feedback
- Gradually increase traffic percentage
- Minimize risk with controlled rollout

#### Implementation
```yaml
canary_deploy:
  stage: deploy
  script:
    - echo "Deploying canary (5% traffic)..."
    - kubectl set image deployment/app app=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - kubectl patch virtualservice/app --type merge -p '{"spec":{"hosts":[{"name":"example.com","http":[{"match":[{"uri":{"prefix":"/api"}}],"route":[{"destination":{"host":"app","subset":"canary"},"weight":5},{"destination":{"host":"app","subset":"stable"},"weight":95}]}]}]}}'
  environment:
    name: production-canary
  needs: ["build-image"]
  only:
    - main
  when: manual

promote_canary:
  stage: deploy
  script:
    - echo "Promoting canary to 100%..."
    - kubectl patch virtualservice/app --type merge -p '{"spec":{"hosts":[{"name":"example.com","http":[{"match":[{"uri":{"prefix":"/api"}}],"route":[{"destination":{"host":"app","subset":"stable"},"weight":100}]}]}]}}'
  environment:
    name: production
  needs: ["canary_deploy"]
  when: manual
```

### Rolling Deployment

#### Concept
- Deploy to subset of instances at a time
- Maintain availability during deployment
- Health checks ensure only healthy instances
- Gradual traffic migration

#### Implementation
```yaml
rolling_deploy:
  stage: deploy
  script:
    - echo "Starting rolling deployment..."
    - kubectl set image deployment/app app=$CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - kubectl rollout status deployment/app --timeout=10m
  environment:
    name: production
    deployment_tier: production
  needs: ["test"]
  only:
    - main

rollback_deployment:
  stage: deploy
  script:
    - echo "Rolling back deployment..."
    - kubectl rollout undo deployment/app
    - kubectl rollout status deployment/app --timeout=10m
  environment:
    name: production
    action: rollback
  when: manual
```

---

## KUBERNETES INTEGRATION

### GitLab Kubernetes Agent Setup

#### Register Agent
1. **Operate** → **Kubernetes clusters** → **Connect a cluster**
2. Enter **Agent name** (e.g., `k8s-agent`)
3. Create agent configuration file: `.gitlab/agents/k8s-agent/config.yaml`
4. Click **Register** to get agent access token

#### Configure Agent Config
```yaml
# .gitlab/agents/k8s-agent/config.yaml
gitops:
  manifest_projects:
    - paths:
        glob: 'k8s/**/*.yaml'
      reconcile_mode: auto_create_namespace

observability:
  logging:
    level: info
```

#### Install Agent with Helm
```bash
# Add GitLab Helm repository
helm repo add gitlab https://charts.gitlab.io
helm repo update

# Install agent
helm install k8s-agent gitlab/gitlab-agent \
  --namespace gitlab-agent-k8s-agent \
  --create-namespace \
  --set gitlabAddress=https://gitlab.com \
  --set agentToken=YOUR_AGENT_TOKEN
```

### GitOps with Agent

#### Deploy via GitOps
```yaml
# .gitlab/agents/k8s-agent/config.yaml
gitops:
  manifest_projects:
    - id: group/project
      paths:
        glob: 'manifests/**/*.yaml'
        exclude: 'manifests/excluded/**'
      reconcile_mode: auto_create_namespace
```

#### Manual Deployment in Pipeline
```yaml
deploy_with_agent:
  stage: deploy
  image: alpine/helm
  script:
    - helm repo add myrepo $HELM_REPO_URL
    - helm repo update
    - helm upgrade --install myapp myrepo/mychart
      --kubeconfig=$KUBECONFIG
      --namespace=production
  environment:
    name: production
    kubernetes:
      namespace: production
  needs: ["build"]
```

---

## ADVANCED FEATURES

### Merge Request Approvals (Web UI)

#### Configure Approval Rules
1. **Settings** → **Merge requests** → **Approval rules**
2. Click **Create new rule**
3. Enter **Rule name**
4. Set **Number of approvals**
5. Select **Eligible approvers** (users/groups)
6. Optionally **Require code owner approval**
7. Click **Create**

### Code Owners

Create `CODEOWNERS` file in repository root:
```
# Syntax: path  @owner

/src/auth/     @security-team
/docs/         @documentation-team
/infrastructure/ @devops-team

*.py           @python-team
.gitlab-ci.yml @devops-team
```

### Webhooks (Web UI)

#### Add Project Webhook
1. **Settings** → **Integrations** → **Webhooks**
2. Enter **URL** (where to send events)
3. Select **Trigger events**:
   - Push events
   - Issues events
   - Merge request events
   - Wiki page events
   - Deployment events
4. Click **Add webhook**

#### Webhook Payload Example
```json
{
  "object_kind": "push",
  "event_name": "push",
  "before": "previous_commit_sha",
  "after": "new_commit_sha",
  "ref": "refs/heads/main",
  "user_id": 123,
  "user_name": "username",
  "user_email": "user@example.com",
  "project_id": 456,
  "repository": {
    "name": "project-name",
    "url": "https://gitlab.com/group/project.git"
  },
  "commits": [
    {
      "id": "commit_sha",
      "message": "Commit message",
      "timestamp": "2024-01-01T12:00:00Z",
      "author": {
        "name": "Author Name",
        "email": "author@example.com"
      }
    }
  ]
}
```

### Repository Mirroring (Web UI)

#### Push Mirror (GitLab → External)
1. **Settings** → **Repository** → **Mirroring repositories**
2. Enter **Git repository URL** (e.g., GitHub)
3. Select **Mirror direction**: **Push**
4. Click **Mirror repository**

This automatically pushes changes from GitLab to external repo.

### Container Registry (Web UI)

#### Push Docker Image
```bash
# Log in
docker login registry.gitlab.com -u username -p token

# Tag image
docker tag my-app:latest registry.gitlab.com/group/project:latest

# Push
docker push registry.gitlab.com/group/project:latest
```

#### Clean Up Old Images
1. **Settings** → **CI/CD** → **Container Registry**
2. Configure **Expiration policy**
3. Set **Keep images** and **Remove images older than**
4. Click **Save expiration policy**

### Environments & Deployments (Web UI)

#### Create Environment
```yaml
# In .gitlab-ci.yml
deploy_prod:
  stage: deploy
  environment:
    name: production
    url: https://example.com
    deployment_tier: production
    auto_stop_in: 1 week
  script:
    - ./deploy-to-prod.sh
```

#### Environment Variables
```yaml
# Environment-specific variables
deploy_staging:
  environment:
    name: staging
    deployment_tier: staging
  variables:
    DATABASE_URL: "postgres://staging-db:5432/app"
  script:
    - ./deploy.sh
```

#### Dynamic Environment Names
```yaml
deploy_review:
  environment:
    name: review/$CI_COMMIT_REF_SLUG
    url: https://$CI_COMMIT_REF_SLUG.example.com
    deployment_tier: testing
  script:
    - ./deploy-review.sh
  artifacts:
    reports:
      dotenv: deploy.env
```

#### Protected Environments (Premium/Ultimate)
1. **Deployments** → **Environments**
2. Click environment → **Edit**
3. Add **Required approvals** to deploy
4. Specify **Eligible approvers**
5. Save

### Feature Flags (Web UI)

#### Create Feature Flag
1. **Deployments** → **Feature flags**
2. Click **Create feature flag**
3. Enter **Name** and **Description**
4. Add **Strategies** (when flag is active)
   - **All users**: Always enabled
   - **Percent of users**: Percentage rollout
   - **User IDs**: Specific users
5. Click **Create**

### Advanced Search

#### Search Syntax
```
# Code search
filename:*.js              # Search in JS files
path:src/components/       # Search in specific path
extension:ts               # Filter by extension

# Fuzzy search
J~ kazi                     # Similar to "mannan kazi"

# Exclude
display -banner            # Exclude "banner"

# Logical operators
bug | (feature +urgent)    # bug OR (feature AND urgent)

# Exact match
"exact phrase"
```

### Wiki (Web UI)

#### Create Wiki Page
1. **Wiki** (left sidebar)
2. Click **New page**
3. Enter **Page title**
4. Write content in **Markdown**
5. Click **Create page**

---

## SECURITY & BEST PRACTICES

### Protecting Sensitive Data

#### Use CI/CD Variables for Secrets
```yaml
✅ Correct:
deploy:
  script:
    - ./deploy.sh $API_KEY
  variables:
    API_KEY: $SECRET_API_KEY

❌ Wrong:
deploy:
  script:
    - export API_KEY="hardcoded-secret"
```

#### Mark Variables as Protected
1. **Settings** → **CI/CD** → **Variables**
2. Check **Protect variable**
3. Only available on protected branches

#### Mask Variables
1. Check **Mask variable** option
2. Value hidden in job logs

### Code Review Best Practices

#### Require Approvals
1. **Settings** → **Merge requests**
2. Set **Approval rules**
3. Require minimum approvals
4. Optionally require **Code owner approval**

#### Protect Main Branch
1. **Settings** → **Repository** → **Branch rules**
2. Add rule for `main`
3. Set **Allowed to merge**: Maintainers only
4. Set **Allowed to push**: No one (force MR review)

### Access Control

#### Personal Access Token Best Practices
- ✅ Use for CI/CD and API automation
- ✅ Set expiration dates
- ✅ Use minimal scopes
- ✅ Rotate regularly
- ❌ Never commit tokens to repository
- ❌ Never hardcode in application

#### SSH Keys
```bash
# Generate key
ssh-keygen -t ed25519 -C "your@email.com"

# Add to GitLab Settings → SSH Keys
cat ~/.ssh/id_ed25519.pub
```

### Pipeline Security

#### Secure Runners
- Use **private runners** for sensitive projects
- Isolate runners from production network
- Limit runner permissions (non-root user)
- Regularly update runner software

#### Secure CI/CD Configuration
```yaml
job:
  # Run only on protected branches
  rules:
    - if: $CI_COMMIT_REF_PROTECTED == "true"
  
  # Use specific image version (not latest)
  image: node:18.19.0
  
  # Mask sensitive output
  script:
    - echo $SECRET | base64 > secret.b64
  
  # Store artifacts securely
  artifacts:
    expire_in: 1 day
```

---

## TROUBLESHOOTING & QUICK REFERENCE

### Common Issues

#### Cannot Push to Repository
```bash
# Problem: Permission denied
# Solution: Check SSH key is added to GitLab
ssh -T git@gitlab.com

# Or use HTTPS with token
git remote set-url origin \
  https://oauth2:YOUR_TOKEN@gitlab.com/group/project.git
```

#### Pipeline Not Triggering
```yaml
# Check: workflow rules allow this branch
workflow:
  rules:
    - if: $CI_COMMIT_BRANCH

# Check: job rules don't exclude it
job:
  rules:
    - if: $CI_COMMIT_BRANCH == "main"
```

#### API Rate Limiting
```
429 Too Many Requests - Wait before retrying
429 responses include:
  RateLimit-Limit: 600
  RateLimit-Remaining: 0
  RateLimit-Reset: 1234567890
```

### Quick Reference Tables

| Command | Purpose |
|---------|---------|
| `glab auth login` | Authenticate |
| `glab repo clone` | Clone repo |
| `glab issue create` | Create issue |
| `glab mr create` | Create MR |
| `glab ci run` | Trigger pipeline |
| `glab ci trace` | View logs |
| `glab api` | API requests |

### Predefined CI Variables

| Variable | Value | Availability |
|----------|-------|--------------|
| `$CI_COMMIT_SHA` | Commit hash | All jobs |
| `$CI_COMMIT_BRANCH` | Branch name | All jobs |
| `$CI_PIPELINE_ID` | Pipeline ID | All jobs |
| `$CI_JOB_ID` | Job ID | All jobs |
| `$CI_ENVIRONMENT_NAME` | Env name | Deploy jobs |
| `$CI_NODE_INDEX` | Parallel index | Parallel jobs |

---

## ADDITIONAL RESOURCES

### Official Documentation
- https://docs.gitlab.com
- https://docs.gitlab.com/cli
- https://docs.gitlab.com/api
- https://docs.gitlab.com/graphql

### Community & Support
- GitLab Forum: https://forum.gitlab.com
- Stack Overflow: Tag `gitlab`
- GitLab Discord: Community chat

---

**Covers**: GitLab Free to Ultimate Tiers
**Audience**: Beginners to Advanced Users
