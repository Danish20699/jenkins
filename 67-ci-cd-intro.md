# Lab 67: CI/CD Introduction — Continuous Integration & Continuous Delivery Architecture

![CI/CD](https://img.shields.io/badge/CI%2FCD-Pipelines-007ACC?style=for-the-badge&logo=githubactions&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-2.580%2B-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Containers-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

---

## 📌 Lab Overview & Real-World DevOps Context

In our previous container labs (**Labs 48–66**), we built images, configured Dockerfiles, orchestrated multi-container stacks with Docker Compose, and deployed applications like the **Personal Portfolio**, the **Travel App**, and the **Gourmet Bistro App**.

However, each deployment required manual intervention:
1. Writing code locally in VS Code / WSL.
2. Manually running `docker build` and `docker tag`.
3. Manually pushing images to Docker Hub.
4. Manually opening SSH sessions to remote Linux virtual machines (**Staging** at `192.168.56.101` and **Production** at `192.168.56.102`).
5. Manually pulling images and executing `docker compose up -d`.

### The Critical Drawbacks of Manual Deployments

| Problem | Production Impact |
| :--- | :--- |
| **Human Error** | Typing mistakes in commands, incorrect tags, or forgotten environment variables can take services down. |
| **Deployment Drift** | Staging and Production configurations diverge over time when manual changes are made directly on servers. |
| **Slow Release Velocity** | Features take hours or days to ship because manual testing and server coordination act as bottlenecks. |
| **No Audit Trail** | Nobody knows who deployed what version, when it went live, or which Git commit corresponds to the running container. |
| **Fear of Releasing** | Deployments become risky events done late at night, rather than routine automated background tasks. |

### The DevOps Solution: CI/CD Pipelines

**CI/CD** is the foundational engineering discipline of DevOps. It automates every step between a developer typing `git push` and the updated code safely serving live end users.

---

## 🏗️ End-to-End CI/CD Architecture Workflow

Here is how our automated CI/CD pipeline connects developer code to our target VirtualBox infrastructure:

```text
  Developer Machine
 (VS Code / Ubuntu WSL)
          │
          │ 1. git push origin main
          ▼
┌────────────────────────────────────────────────────────┐
│               GitHub Remote Repository                 │
│         (Source Code, Dockerfile, Compose, Tests)      │
└───────────────────────────┬────────────────────────────┘
                            │
                            │ 2. Webhook / SCM Polling Trigger
                            ▼
┌────────────────────────────────────────────────────────┐
│           Jenkins Automation Controller                │
│ ┌────────────────────────────────────────────────────┐ │
│ │ Stage 1: Checkout SCM (Fetch latest commit)        │ │
│ ├────────────────────────────────────────────────────┤ │
│ │ Stage 2: Code Linting & Automated Unit Tests       │ │
│ ├────────────────────────────────────────────────────┤ │
│ │ Stage 3: Build Docker Image (Tag: v${BUILD_NUMBER})│ │
│ ├────────────────────────────────────────────────────┤ │
│ │ Stage 4: Push Immutable Image to Docker Hub        │ │
│ └─────────────────────────┬──────────────────────────┘ │
└───────────────────────────┼────────────────────────────┘
                            │
                            │ 3. Automated SSH Deployment
                            ▼
┌────────────────────────────────────────────────────────┐
│             Staging VM (192.168.56.101:8081)           │
│   • Runs docker compose pull && compose up -d          │
│   • Full replica of production environment             │
│   • Automated health checks & QA validation            │
└───────────────────────────┬────────────────────────────┘
                            │
                            │ 4. Jenkins Approval Gate (Manual Sign-off)
                            ▼
┌────────────────────────────────────────────────────────┐
│            Production VM (192.168.56.102:8081)         │
│   • Zero-downtime rolling update                       │
│   • Live customer traffic                              │
│   • Persistent production database volume              │
└────────────────────────────────────────────────────────┘
```

---

## 📚 Core CI/CD Concepts & Detailed Breakdown

The abbreviation **CI/CD** represents three interrelated practices:

### 1. Continuous Integration (CI)
**Continuous Integration** is the engineering practice where team members integrate their work into a shared mainline repository multiple times a day.

Every commit triggers an automated pipeline that:
- Clones the latest code branch.
- Installs dependencies cleanly in an isolated workspace.
- Runs static code analysis, style linters, and security checks.
- Executes automated test suites (unit tests, integration tests).
- Verifies that the new code does not break existing functionality.

> **Key Rule of CI:** *Fail fast, fail early.* If a test fails, the build breaks immediately, alerting the developer before faulty code touches any server.

---

### 2. Continuous Delivery (CD)
**Continuous Delivery** extends Continuous Integration by ensuring that every change passing the test suite can be safely released to production **at any moment**.

- Code is automatically packaged into an immutable release artifact (e.g., a version-tagged Docker image).
- The artifact is automatically deployed to a **Staging environment** (`192.168.56.101`).
- The transition from Staging to Production requires a **manual human approval gate** (e.g., clicking *"Proceed to Production"* in Jenkins).
- The business decides when to release, but the technical deployment is 100% automated.

---

### 3. Continuous Deployment (CD)
**Continuous Deployment** is the most advanced tier of automation.

- There is **no manual approval gate**.
- Every single commit that passes all automated CI stages and staging health checks is deployed directly to the live **Production environment** (`192.168.56.102`) completely unattended.
- Requires rigorous test coverage, automated rollbacks, and advanced canary monitoring.

---

### 📊 Comparison Reference: CI vs Continuous Delivery vs Continuous Deployment

| Capability | Continuous Integration (CI) | Continuous Delivery (CD) | Continuous Deployment (CD) |
| :--- | :---: | :---: | :---: |
| **Code Integration** | Automated | Automated | Automated |
| **Automated Testing** | Automated | Automated | Automated |
| **Build Artifact Creation** | Automated | Automated | Automated |
| **Staging Deployment** | Manual / Optional | Automated | Automated |
| **Production Deployment** | Manual | **Manual Approval Gate** | **Fully Automated** |
| **Release Frequency** | Internal to Devs | On Business Demand | Continuous (Multiple/Day) |

---

## 🔄 The 7 Essential CI/CD Pipeline Stages

A production-grade Jenkins declarative pipeline executes through distinct, ordered stages:

```text
┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐    ┌───────────┐
│  STAGE 1  │───▶│  STAGE 2  │───▶│  STAGE 3  │───▶│  STAGE 4  │───▶│  STAGE 5  │───▶│  STAGE 6  │───▶│  STAGE 7  │
│  Source   │    │   Build   │    │   Test    │    │  Quality  │    │  Package  │    │ Registry  │    │  Deploy   │
│ Checkout  │    │  Compile  │    │ Unit/Int. │    │ & Security│    │ Container │    │   Push    │    │ Stg / Prod│
└───────────┘    └───────────┘    └───────────┘    └───────────┘    └───────────┘    └───────────┘    └───────────┘
```

### Stage 1: Source Code Checkout
- Jenkins checks out code from GitHub via SSH or HTTPS.
- Pulls the exact commit SHA triggered by the webhook or build request.
- Ensures workspace cleanliness before starting.

### Stage 2: Build & Dependency Resolution
- Downloads and resolves external packages (e.g., `composer install` for PHP, `npm install` for frontend, `pip install` for Python).
- Validates syntax and verifies that all required modules are present.

### Stage 3: Automated Testing
- **Unit Tests:** Tests individual functions in isolation.
- **Integration Tests:** Tests interactions between modules (e.g., PHP connecting to PostgreSQL).
- **Smoke Tests:** Quick verification that basic endpoints respond with HTTP 200.

### Stage 4: Code Quality & Security Scanning
- Runs static analysis tools (e.g., SonarQube, PHPStan, ESLint).
- Scans dependencies for known CVE vulnerabilities (e.g., Trivy, Snyk).
- Ensures code adheres to team style guides and security baselines.

### Stage 5: Container Packaging (`docker build`)
- Packages the application into an immutable Docker image using `Dockerfile`.
- Uses deterministic build tags instead of mutable tags:
  ```bash
  # Best Practice: Versioned Immutable Tag
  docker build -t danishdopsa/restaurant-app:v${BUILD_NUMBER} .
  ```

### Stage 6: Registry Storage (`docker push`)
- Logs into Docker Hub using encrypted credentials stored in Jenkins.
- Pushes the image tag to the centralized container registry.
- Ensures the exact same image built and tested in CI is what gets deployed to servers.

### Stage 7: Automated Deployment
- Connects to target virtual machines over SSH using private keys (`vm-ssh-key`).
- Uses `docker compose -p <app-name> up -d` to pull and launch updated containers.
- First deploys to **Staging** (`192.168.56.101`), waits for approval, then promotes to **Production** (`192.168.56.102`).

---

## 🌐 Deployment Environments: Dev vs Staging vs Production

In professional software development, code must never jump directly from a laptop to live customers. Organizations maintain isolated deployment tiers:

| Attribute | Development (Dev) | Staging (Pre-Prod) | Production (Prod) |
| :--- | :--- | :--- | :--- |
| **Host in Our Lab** | Windows 11 / Ubuntu WSL | Staging VM (`192.168.56.101`) | Production VM (`192.168.56.102`) |
| **Target Audience** | Software Developers | QA, Product Managers, Testers | Real Paying Users / Customers |
| **Data Source** | Mock / Seed Data | Sanitized Copy of Prod Data | Real Live Persistent Database |
| **Error Logging** | Verbose / Debug on Screen | Detailed Server Logs | Clean User Pages + Telemetry |
| **Uptime SLA** | Best Effort | High | 99.9% High Availability |
| **Resource Limits** | Minimal | Matched to Production Specs | Scaled CPU / RAM / Storage |

---

## 🚀 Enterprise Deployment Strategies

When deploying container updates in production, teams choose deployment patterns based on tolerance for downtime and risk:

```text
1. Rolling Update
   [ Old v1 ] [ Old v1 ]  ──▶  [ New v2 ] [ Old v1 ]  ──▶  [ New v2 ] [ New v2 ]
   (Gradually replaces old instances with new ones; zero downtime)

2. Blue-Green Deployment
   Router ──▶ [ Blue Environment: v1 (Active) ]
              [ Green Environment: v2 (Idle / Test) ] ──▶ Switch router to Green

3. Canary Deployment
   Router ──▶ 95% traffic ──▶ [ v1 Existing Stable ]
          ──▶  5% traffic ──▶ [ v2 Canary Release ]   (Validate metrics before 100%)
```

---

## 🔒 Security & Secret Management in CI/CD

Automated pipelines require access to sensitive credentials (Docker Hub access tokens, SSH private keys, database passwords). Hardcoding these credentials in Git is a major security breach.

### Best Practices Followed in Our Labs:
1. **Never Commit Secrets:** Passwords and private keys are excluded via `.gitignore` and `.env.example`.
2. **Jenkins Credentials Store:** Secrets are stored securely in Jenkins credentials manager (`dockerhub-creds` and `vm-ssh-key`) with encryption at rest.
3. **Environment Variable Injection:** Credentials are only injected in memory during build steps via `withCredentials([...])` and wiped when the pipeline completes:
   ```groovy
   withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
       sh 'echo "$PASS" | docker login -u "$USER" --password-stdin'
   }
   ```
4. **Project Isolation:** Using `docker compose -p restaurant-app` prevents conflicting container names and network overlap between projects on shared VMs.

---

## 🧠 Key Takeaways & Enterprise Best Practices

1. **Pipeline as Code:** Store your deployment process in a `Jenkinsfile` committed alongside your application source code.
2. **Build Once, Deploy Anywhere:** The exact same Docker image binary tested on Staging is deployed to Production. Never rebuild an image for production.
3. **Immutability:** Tag container images with incrementing build numbers (`v45`, `v46`, `v47`) or Git commit SHAs, never relying solely on the ambiguous `:latest` tag.
4. **Automated Health Checks:** Define container healthchecks (`pg_isready`, HTTP probes) so Docker Compose and Jenkins only route traffic when databases and web servers are fully initialized.
5. **Fast Feedback Loops:** Keep CI build times short so developers discover errors within minutes of pushing code.