# CI/CD — Continuous Integration and Continuous Delivery/Deployment

## 1. What is CI/CD?

**CI/CD** stands for:

- **CI** — Continuous Integration
- **CD** — Continuous Delivery
- **CD** — Continuous Deployment

CI/CD is a software development and DevOps practice that automates the process of building, testing, and delivering software.

A typical CI/CD workflow looks like:

```text
Developer
    ↓
Git Repository
    ↓
Continuous Integration
    ↓
Build
    ↓
Test
    ↓
Dev Environment
    ↓
Staging Environment
    ↓
Production Environment
```

The main goal of CI/CD is to make software delivery:

- Faster
- More reliable
- Repeatable
- Automated
- Easier to monitor and maintain

---

# 2. CI vs Continuous Delivery vs Continuous Deployment

The term **CD** can refer to two different concepts:

1. Continuous Delivery
2. Continuous Deployment

Although they are related, they are not exactly the same.

---

## 2.1 Continuous Integration (CI)

**Continuous Integration** is the practice of frequently integrating code changes into a shared repository and automatically building and testing those changes.

### Basic CI Flow

```text
Developer writes code
        ↓
git push
        ↓
CI Pipeline starts
        ↓
Checkout code
        ↓
Build
        ↓
Run tests
        ↓
Code quality checks
        ↓
Build Docker image / artifact
```

### Example

A developer modifies a PHP application and pushes the changes to GitHub.

The CI pipeline automatically:

1. Downloads the latest code.
2. Installs dependencies.
3. Runs tests.
4. Checks code quality.
5. Builds the application.
6. Builds a Docker image.

If any step fails, the pipeline stops and reports the failure.

### Main Purpose of CI

```text
Find problems early
        +
Automatically test code
        +
Keep the main branch stable
```

---

# 3. Continuous Delivery

**Continuous Delivery** means that code which successfully passes CI is automatically prepared and made ready for deployment.

The deployment to production usually requires a **manual approval**.

### Continuous Delivery Flow

```text
Developer
    ↓
Git Push
    ↓
CI
    ↓
Build
    ↓
Test
    ↓
Docker Image / Artifact
    ↓
Deploy to Dev
    ↓
Deploy to Staging
    ↓
Manual Approval
    ↓
Production
```

### Important Point

With Continuous Delivery:

> The application is always in a deployable state, but production deployment may require human approval.

### Example

Suppose version `v1.4` passes all tests.

The pipeline automatically:

```text
Build v1.4
   ↓
Test v1.4
   ↓
Deploy to Dev
   ↓
Deploy to Staging
   ↓
Wait for approval
   ↓
Deploy to Production
```

A developer, DevOps engineer, or release manager can approve the production deployment.

---

# 4. Continuous Deployment

**Continuous Deployment** goes one step further.

If the code passes all automated checks, it is automatically deployed to production without requiring manual approval.

### Continuous Deployment Flow

```text
Developer
    ↓
Git Push
    ↓
CI
    ↓
Build
    ↓
Automated Tests
    ↓
Deploy to Dev
    ↓
Deploy to Staging
    ↓
Automated Tests
    ↓
Production
```

### Important Point

With Continuous Deployment:

> Every change that successfully passes the required automated checks can be automatically released to production.

---

# 5. CI vs Continuous Delivery vs Continuous Deployment

| Feature | Continuous Integration | Continuous Delivery | Continuous Deployment |
|---|---|---|---|
| Code integration | Yes | Yes | Yes |
| Automated build | Yes | Yes | Yes |
| Automated testing | Yes | Yes | Yes |
| Deploy to Dev | Optional | Usually | Usually |
| Deploy to Staging | Optional | Usually | Usually |
| Production deployment | No | Manual approval | Automatic |
| Human approval | Not required | Usually required | Usually not required |
| Main goal | Validate code | Keep software deployable | Automatically release software |

### Simple Difference

```text
CI
↓
Build + Test


Continuous Delivery
↓
Build + Test + Prepare Release
                    ↓
              Manual Approval
                    ↓
                Production


Continuous Deployment
↓
Build + Test + Release
                    ↓
              Production
```

---

# 6. CI/CD Pipeline

A CI/CD pipeline is a sequence of automated steps used to build, test, and deploy an application.

A typical pipeline can look like:

```text
        Git Push
           ↓
    Source Code Checkout
           ↓
       Build Code
           ↓
     Run Unit Tests
           ↓
    Code Quality Check
           ↓
   Build Docker Image
           ↓
 Push Image to Registry
           ↓
      Deploy to Dev
           ↓
     Integration Tests
           ↓
   Deploy to Staging
           ↓
   Acceptance Tests
           ↓
     Manual Approval
           ↓
   Deploy to Production
```

---

# 7. Common CI/CD Pipeline Stages

## Stage 1 — Source

The pipeline gets the latest source code from a Git repository.

Common Git platforms:

- GitHub
- GitLab
- Bitbucket
- Azure Repos

Example:

```bash
git clone https://github.com/example/project.git
```

---

## Stage 2 — Build

The application is compiled or packaged.

For example:

```bash
npm install
npm run build
```

For a Docker application:

```bash
docker build -t my-app:v1 .
```

---

## Stage 3 — Test

Automated tests are executed.

Examples:

```text
Unit Tests
Integration Tests
API Tests
Security Tests
```

Example:

```bash
npm test
```

---

## Stage 4 — Code Quality

The pipeline can check code quality and security.

Examples:

- SonarQube
- SonarCloud
- ESLint
- Trivy
- Snyk

---

## Stage 5 — Package / Artifact

The application is packaged into an artifact.

Examples:

```text
JAR
WAR
ZIP
Docker Image
Binary
```

For Docker:

```bash
docker build -t my-app:v1 .
```

---

## Stage 6 — Push

The Docker image or artifact is pushed to a registry.

Example:

```bash
docker push username/my-app:v1
```

Possible registries:

- Docker Hub
- Amazon ECR
- GitHub Container Registry
- GitLab Container Registry
- Azure Container Registry

---

## Stage 7 — Deployment

The application is deployed to an environment.

For example:

```text
Dev
 ↓
Staging
 ↓
Production
```

Deployment can be performed using:

- Docker
- Docker Compose
- Kubernetes
- Ansible
- Terraform
- Cloud deployment services

---

# 8. CI/CD Tools

There are many tools used to implement CI/CD pipelines.

## Popular CI/CD Tools

| Tool | Purpose |
|---|---|
| Jenkins | CI/CD automation |
| GitHub Actions | CI/CD integrated with GitHub |
| GitLab CI/CD | CI/CD integrated with GitLab |
| Azure DevOps | Microsoft CI/CD platform |
| CircleCI | Cloud-based CI/CD |
| Travis CI | CI/CD automation |
| Argo CD | GitOps continuous delivery |
| AWS CodePipeline | AWS CI/CD |
| AWS CodeBuild | Build and test automation |
| AWS CodeDeploy | Application deployment |

---

# 9. Jenkins

**Jenkins** is an open-source automation server widely used for CI/CD.

A Jenkins pipeline can perform tasks such as:

```text
Git Checkout
     ↓
Build
     ↓
Test
     ↓
Docker Build
     ↓
Docker Push
     ↓
Deploy
```

A Jenkins pipeline can be defined using a `Jenkinsfile`.

Example:

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/example/project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'docker build -t my-app:latest .'
            }
        }

        stage('Test') {
            steps {
                sh 'echo Running tests'
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose up -d'
            }
        }
    }
}
```

---

# 10. GitHub Actions

GitHub Actions is a CI/CD automation service provided by GitHub.

Workflow files are stored inside:

```text
.github/workflows/
```

Example:

```text
.github/
└── workflows/
    └── ci.yml
```

A GitHub Actions workflow can:

```text
Push Code
    ↓
Trigger Workflow
    ↓
Build
    ↓
Test
    ↓
Build Docker Image
    ↓
Push Image
    ↓
Deploy
```

---

# 11. GitLab CI/CD

GitLab provides built-in CI/CD functionality.

The pipeline configuration is commonly defined in:

```text
.gitlab-ci.yml
```

Example:

```yaml
stages:
  - build
  - test
  - deploy

build:
  stage: build
  script:
    - echo "Building application"

test:
  stage: test
  script:
    - echo "Running tests"

deploy:
  stage: deploy
  script:
    - echo "Deploying application"
```

---

# 12. Dev, Staging and Production Environments

Software applications are commonly separated into different environments.

The three common environments are:

```text
Development
     ↓
Staging
     ↓
Production
```

Each environment has a different purpose.

---

# 13. Development Environment

The **Development environment**, commonly called **Dev**, is where developers develop and test new features.

### Purpose

- Write new code
- Develop features
- Fix bugs
- Perform initial testing
- Experiment with changes

Example:

```text
Developer
    ↓
Code Change
    ↓
Git Push
    ↓
CI Pipeline
    ↓
Dev Environment
```

The Dev environment can contain:

```text
Application
Database
API
Docker Containers
Testing Services
```

### Example

A developer creates a new login feature.

The feature is first deployed to:

```text
Dev
```

The developer verifies that it works correctly.

---

# 14. Staging Environment

The **Staging environment** is an environment used to test the application before production.

It should be as similar to production as practical.

### Purpose

- Final testing
- Integration testing
- User acceptance testing
- Performance testing
- Release validation

Example:

```text
Dev
 ↓
Staging
 ↓
Production
```

The staging environment helps identify problems before they affect real users.

### Example

Suppose the production server uses:

```text
Ubuntu
Docker
PostgreSQL
Nginx
```

The staging environment should use a similar configuration.

This helps ensure that an application that works in staging is more likely to work in production.

---

# 15. Production Environment

The **Production environment** is the live environment used by actual users.

### Purpose

- Serve real users
- Run the live application
- Store production data
- Provide business services

Example:

```text
Users
  ↓
Load Balancer
  ↓
Application
  ↓
Database
```

Production requires additional attention to:

- Security
- Availability
- Monitoring
- Backups
- Performance
- Logging
- Disaster recovery

---

# 16. Dev vs Staging vs Production

| Environment | Main Purpose | Users | Data | Stability |
|---|---|---|---|---|
| Dev | Development | Developers | Test data | Low |
| Staging | Final testing | Developers / QA | Test or sanitized data | High |
| Production | Live application | Real users | Real data | Very High |

### Simple Understanding

```text
DEV
↓
"Is the developer's change working?"


STAGING
↓
"Is the complete release ready for production?"


PRODUCTION
↓
"Is the application serving real users correctly?"
```

---

# 17. Example CI/CD Environment Architecture

A practical architecture can look like:

```text
                 GitHub
                    |
                    ↓
              Jenkins / CI
                    |
          +---------+---------+
          |                   |
          ↓                   ↓
        Build               Test
          |
          ↓
    Docker Image
          |
          ↓
    Docker Registry
          |
          ↓
        DEV
          |
          ↓
      STAGING
          |
          ↓
   Manual Approval
          |
          ↓
     PRODUCTION
```

For Continuous Deployment:

```text
                 GitHub
                    |
                    ↓
                 CI/CD
                    |
                    ↓
                  Build
                    |
                    ↓
                  Test
                    |
                    ↓
                  Dev
                    |
                    ↓
                Staging
                    |
                    ↓
             Automated Tests
                    |
                    ↓
               Production
```

---

# 18. Docker in CI/CD

Docker is commonly used in CI/CD because it packages an application together with its dependencies.

Example:

```text
Source Code
     ↓
Dockerfile
     ↓
Docker Image
     ↓
Container
```

The same Docker image can be promoted through environments:

```text
Docker Image
     ↓
    Dev
     ↓
  Staging
     ↓
Production
```

This helps reduce environment-related problems.

---

# 19. Docker Registry

A Docker registry stores Docker images.

Common registries include:

```text
Docker Hub
Amazon ECR
GitHub Container Registry
GitLab Container Registry
Azure Container Registry
```

Example:

```bash
docker build -t myapp:v1 .
```

Login:

```bash
docker login
```

Push:

```bash
docker push username/myapp:v1
```

Then another server can pull the image:

```bash
docker pull username/myapp:v1
```

---

# 20. CI/CD with Docker Example

A typical DevOps workflow can look like:

```text
Developer
    |
    | git push
    ↓
GitHub
    |
    ↓
Jenkins
    |
    +---- Checkout
    |
    +---- Build
    |
    +---- Test
    |
    +---- Docker Build
    |
    ↓
Docker Registry
    |
    ↓
Dev VM
    |
    ↓
Staging VM
    |
    ↓
Production VM
```

---

# 21. Configuration Between Environments

Different environments normally require different configuration.

For example:

### Dev

```env
APP_ENV=dev
DB_HOST=dev-db
DB_NAME=travel_dev
```

### Staging

```env
APP_ENV=staging
DB_HOST=staging-db
DB_NAME=travel_staging
```

### Production

```env
APP_ENV=production
DB_HOST=production-db
DB_NAME=travel_production
```

The application code can remain the same while environment-specific configuration changes.

---

# 22. Secrets in CI/CD

Sensitive information should not normally be hard-coded inside source code.

Examples of secrets:

```text
Database passwords
API keys
Cloud credentials
SSH private keys
GitHub tokens
Docker registry credentials
```

CI/CD platforms provide secret-management features.

Examples:

```text
Jenkins Credentials
GitHub Secrets
GitLab CI/CD Variables
AWS Secrets Manager
HashiCorp Vault
```

Avoid:

```yaml
POSTGRES_PASSWORD: mypassword123
```

Instead, use securely managed environment variables or secrets.

---

# 23. Deployment Strategies

CI/CD pipelines can use different deployment strategies.

## 23.1 Rolling Deployment

New versions are gradually deployed while existing instances continue serving users.

```text
Old Version
Old Version
Old Version

        ↓

New Version
Old Version
Old Version

        ↓

New Version
New Version
Old Version

        ↓

New Version
New Version
New Version
```

---

## 23.2 Blue-Green Deployment

Two production environments are maintained:

```text
Blue → Current Version
Green → New Version
```

Traffic initially goes to Blue.

After testing Green:

```text
Users
  ↓
Green
```

If there is a problem, traffic can be switched back to Blue.

---

## 23.3 Canary Deployment

The new version is initially released to a small percentage of users.

Example:

```text
95% → Old Version
5%  → New Version
```

If everything works correctly:

```text
75% → New Version
25% → Old Version
```

Eventually:

```text
100% → New Version
```

---

# 24. CI/CD Best Practices

## 1. Keep builds automated

Avoid unnecessary manual steps.

## 2. Run tests automatically

Tests should run whenever code changes.

## 3. Keep environments consistent

Dev, staging, and production should be as similar as practical.

## 4. Store secrets securely

Never commit passwords, tokens, or private keys to Git.

## 5. Use versioned artifacts

For example:

```text
myapp:v1.0
myapp:v1.1
myapp:v1.2
```

Avoid relying only on:

```text
latest
```

## 6. Monitor production

Use monitoring and logging to detect problems.

## 7. Make deployments repeatable

The same deployment process should work consistently.

## 8. Use Git

Keep application and infrastructure changes version controlled.

---

# 25. Complete CI/CD Example

Consider a web application built using:

```text
Frontend
Backend
PostgreSQL
Docker
Jenkins
GitHub
```

The workflow could be:

```text
Developer
    |
    | git push
    ↓
GitHub
    |
    ↓
Jenkins
    |
    +---- Checkout
    |
    +---- Build
    |
    +---- Unit Tests
    |
    +---- Security Scan
    |
    +---- Docker Build
    |
    ↓
Docker Registry
    |
    ↓
DEV
    |
    ↓
Integration Tests
    |
    ↓
STAGING
    |
    ↓
Acceptance Testing
    |
    ↓
Approval
    |
    ↓
PRODUCTION
```

---

# 26. CI/CD in Simple Terms

The easiest way to remember CI/CD is:

```text
CI
↓
"Does my code work?"


Continuous Delivery
↓
"Is my application ready to be released?"

Manual approval
        ↓
Production


Continuous Deployment
↓
"Is my application ready to be released?"

Automatic
        ↓
Production
```

And the environments:

```text
DEV
↓
Develop and test


STAGING
↓
Test before release


PRODUCTION
↓
Serve real users
```

---

# 27. Final Summary

CI/CD is an important part of modern DevOps.

### Continuous Integration

```text
Code → Build → Test
```

Focuses on integrating and validating code changes frequently.

### Continuous Delivery

```text
Code → Build → Test → Dev → Staging → Approval → Production
```

Keeps software ready for production while typically requiring approval before release.

### Continuous Deployment

```text
Code → Build → Test → Dev → Staging → Production
```

Automatically releases successful changes to production.

### Environments

```text
Development
    ↓
Staging
    ↓
Production
```

### Common Tools

```text
GitHub
GitLab
Jenkins
GitHub Actions
Docker
Docker Hub
AWS
Kubernetes
Ansible
Terraform
SonarQube
Trivy
Argo CD
```

### Overall DevOps Flow

```text
             PLAN
               ↓
             CODE
               ↓
             BUILD
               ↓
             TEST
               ↓
            RELEASE
               ↓
            DEPLOY
               ↓
            OPERATE
               ↓
            MONITOR
               ↓
            FEEDBACK
               ↓
             CODE
```

This continuous cycle is the foundation of modern **DevOps and CI/CD practices**.