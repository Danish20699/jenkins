# Lab 68: Jenkins Introduction & Distributed Controller-Agent Architecture

![Jenkins](https://img.shields.io/badge/Jenkins-2.580%2B-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Architecture](https://img.shields.io/badge/Architecture-Controller--Agent-blue?style=for-the-badge)
![Docker](https://img.shields.io/badge/Docker-Engine-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

---

## 📌 Lab Overview & Real-World DevOps Context

In **Lab 67**, we established the foundational principles of Continuous Integration and Continuous Delivery (CI/CD). In this lab, we dive deep into the industry's most widely adopted open-source automation engine: **Jenkins**.

Throughout our recent multi-tier projects (such as our **Restaurant Bistro** and **Travel Application** deployments), we utilized Jenkins running as a containerized service on port `9090` to automate build validation, Docker Hub publishing, and remote SSH deployments across our VirtualBox VMs (**Staging** at `192.168.56.101` and **Production** at `192.168.56.102`).

This lab covers:
1. **What Jenkins Is:** The core automation engine behind modern DevOps workflows.
2. **Controller-Agent Distributed Architecture:** How Jenkins decouples orchestration from execution.
3. **Pipeline as Code (`Jenkinsfile`):** Managing build lifecycles using version-controlled declarative pipelines.
4. **Jenkins Internals & Directory Hierarchy:** Understanding `/var/jenkins_home`, workspace locks, and credential storage.
5. **Security & Role-Based Access:** Protecting sensitive build credentials and securing the automation plane.

---

## 🏗️ Jenkins Distributed Architecture (Controller vs Agent)

In enterprise environments, running heavy compilation, Docker builds, and tests directly on the central Jenkins server causes resource exhaustion and single-point-of-failure risks. Jenkins solves this through a distributed **Controller-Agent (Master-Worker)** model:

```text
                                 ┌────────────────────────────────────────┐
                                 │     Jenkins Controller (Master Node)   │
                                 │       http://localhost:9090            │
                                 ├────────────────────────────────────────┤
                                 │ • Web UI Dashboard & REST API          │
                                 │ • Job Scheduling & Build Queue         │
                                 │ • Plugin Manager (Docker, SSH Agent)   │
                                 │ • Credential Vault (Encrypted)         │
                                 │ • Pipeline Orchestration Engine        │
                                 └───────────────────┬────────────────────┘
                                                     │
                             ┌───────────────────────┴───────────────────────┐
                             │ SSH / Inbound JNLP (Encrypted TCP)            │
                             ▼                                               ▼
             ┌───────────────────────────────┐               ┌───────────────────────────────┐
             │       Build Agent Node 1      │               │       Build Agent Node 2      │
             │     (Docker Engine / Linux)   │               │     (Windows / Specialized)   │
             ├───────────────────────────────┤               ├───────────────────────────────┤
             │ • Executes build steps        │               │ • Executes cross-platform     │
             │ • Runs `docker build` & push  │               │   test suites                 │
             │ • Isolated workspace per job  │               │ • Reports stdout/stderr       │
             │ • Ephemeral / scalable nodes  │               │   back to controller          │
             └───────────────────────────────┘               └───────────────────────────────┘
```

### Roles and Responsibilities

| Component | Primary Function | What It Should NEVER Do |
| :--- | :--- | :--- |
| **Jenkins Controller** | Serves the Web UI, schedules builds, stores configurations, securely manages credentials, orchestrates pipeline stages. | Run intensive build scripts, compile large codebases, or run heavy Docker builds directly. |
| **Jenkins Agent** | Listens for task assignments from the controller, checks out code into an isolated workspace, executes shell commands, and streams console logs back. | Store permanent configuration files, host user databases, or store shared master credentials unencrypted. |

---

## 📚 Core Jenkins Vocabulary Reference

| Concept | Definition & Purpose | Real-World Role in Our Labs |
| :--- | :--- | :--- |
| **`Controller`** | The central managing node coordinating the entire Jenkins infrastructure. | The LTS instance serving on `http://localhost:9090`. |
| **`Agent (Node)`** | A separate physical machine, VM, or container that executes build jobs. | Remote workers provisioned to build container images. |
| **`Executor`** | A single computing thread / slot on a node capable of running a build job. | Dictates how many jobs can execute simultaneously on a node. |
| **`Pipeline`** | An automated workflow composed of discrete stages (Build, Test, Deploy). | Our multi-stage pipeline defined in `Jenkinsfile`. |
| **`Jenkinsfile`** | A text file checked into Git defining the complete pipeline logic as code. | The declarative file inside `restaurant-docker` and `travel-app-compose`. |
| **`Workspace`** | A dedicated directory on the agent filesystem where source code is checked out. | Temporary disk space created at `/var/jenkins_home/workspace/<job-name>`. |
| **`Plugin`** | Modular extensions adding integrations (Docker, Git, SSH, Slack, Kubernetes). | `SSH Agent`, `Docker Pipeline`, and `Git` plugins. |
| **`Credentials Vault`** | Encrypted key storage protecting secrets from appearing in console logs. | `dockerhub-creds` and `vm-ssh-key`. |

---

## 🛠️ Pipeline as Code: Declarative vs Scripted Pipelines

Jenkins provides two syntaxes for writing pipelines:

### 1. Declarative Pipeline (Industry Standard & Recommended)
Introduced to provide a structured, opinionated, and readable syntax. It enforces strict syntax rules with pre-defined blocks (`pipeline`, `stages`, `stage`, `steps`, `post`):

```groovy
pipeline {
    agent any

    environment {
        APP_NAME = "restaurant-app"
        STAGING_IP = "192.168.56.101"
    }

    stages {
        stage('Build & Test') {
            steps {
                sh 'echo "Compiling and validating application code..."'
            }
        }
        stage('Deploy Staging') {
            steps {
                sh 'echo "Deploying container to ${STAGING_IP}..."'
            }
        }
    }

    post {
        always { echo "Pipeline finished execution." }
        success { echo "Build succeeded!" }
        failure { echo "Build failed! Triggering notifications." }
    }
}
```

### 2. Scripted Pipeline (Legacy / Complex Logic)
Built on Groovy syntax with fewer structure constraints. Offers unlimited flexibility at the cost of readability and maintenance difficulty:

```groovy
node {
    stage('Build') {
        sh 'echo "Building..."'
    }
    stage('Deploy') {
        sh 'echo "Deploying..."'
    }
}
```

---

## 📁 Jenkins File System Hierarchy (`/var/jenkins_home`)

Understanding the internal storage structure of Jenkins is essential for troubleshooting and disaster recovery:

```text
/var/jenkins_home/
├── config.xml                      # Global Jenkins server configuration
├── credentials.xml                 # Encrypted credentials database
├── plugins/                        # Installed plugin archive files (.jpi / .hpi)
├── secrets/
│   ├── initialAdminPassword        # One-time bootstrap password
│   └── master.key                  # Master encryption key for secrets
├── jobs/
│   └── restaurant-pipeline/
│       ├── config.xml              # Pipeline job definition
│       └── builds/
│           ├── 45/                 # Build #45 metadata & build.xml
│           ├── 46/                 # Build #46 metadata & build.xml
│           └── 47/                 # Build #47 metadata & build.xml
└── workspace/
    └── restaurant-pipeline/        # Temporary checkout directory where commands run
```

---

## 🔒 Jenkins Security & Enterprise Hardening

Securing a CI/CD server is critical because Jenkins has root-level deployment authority over production environments:

1. **Role-Based Access Control (RBAC):** Restrict who can create jobs, trigger builds, or approve production deployments.
2. **Credential Masking:** Jenkins automatically obscures passwords and API tokens in console logs with `****`.
3. **CSRF Protection & Safe Directory Checks:** Enforce Git ownership validation (`safe.directory = *`) and prevent cross-site request forgery attacks.
4. **Least-Privilege Agent Access:** Agents should run with restricted service accounts rather than full root access.

---

## 🧠 Key Takeaways & Architectural Best Practices

- **Never Build on the Controller:** Always offload resource-intensive builds to dedicated agents or dynamic Docker containers.
- **Store Pipelines in Git:** Never write pipelines directly in the Jenkins web textarea; always use a version-controlled `Jenkinsfile`.
- **Use `post` Blocks for Cleanup:** Ensure containers, temporary files, and Docker credentials are removed via `post { always { sh 'docker logout || true' } }`.
- **Implement Approval Gates for Production:** Always separate automated staging deployments from production promotions using interactive `input` stages.