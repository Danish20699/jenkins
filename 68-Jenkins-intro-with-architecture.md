# 68 — Jenkins Introduction with Architecture

## 1. What is Jenkins?

**Jenkins** is an open-source automation server used to automate software development and DevOps processes.

Jenkins is commonly used for:

- Continuous Integration (CI)
- Continuous Delivery (CD)
- Continuous Deployment
- Automated testing
- Application building
- Docker image building
- Application deployment
- Infrastructure automation

Jenkins helps automate repetitive tasks so that developers and DevOps engineers do not have to perform them manually.

---

# 2. Why Jenkins?

Without Jenkins, a developer may need to manually perform:

```text
Write Code
    ↓
Push Code
    ↓
Build Application
    ↓
Run Tests
    ↓
Build Docker Image
    ↓
Push Docker Image
    ↓
Deploy Application
```

With Jenkins, these steps can be automated:

```text
Developer
    ↓
Git Push
    ↓
Jenkins
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

This makes the software delivery process faster and more consistent.

---

# 3. Jenkins in CI/CD

Jenkins can act as the automation engine in a CI/CD pipeline.

Example:

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
    +---- Checkout Code
    |
    +---- Build
    |
    +---- Test
    |
    +---- Code Quality Check
    |
    +---- Build Docker Image
    |
    +---- Push Image
    |
    +---- Deploy
    ↓
Application
```

---

# 4. Jenkins Architecture

Jenkins follows a controller-agent architecture.

The major components are:

```text
                 Jenkins Controller
                        |
          +-------------+-------------+
          |             |             |
          ↓             ↓             ↓
       Agent 1        Agent 2       Agent 3
          |             |             |
          ↓             ↓             ↓
       Linux          Windows        Linux
```

The Jenkins controller manages the overall CI/CD process, while agents execute the actual tasks.

---

# 5. Jenkins Controller

The **Jenkins Controller** is the central component of Jenkins.

It is responsible for managing Jenkins and coordinating pipeline execution.

The controller can:

- Manage Jenkins configuration
- Manage jobs and pipelines
- Schedule builds
- Store pipeline configuration
- Manage credentials
- Manage plugins
- Assign work to agents
- Monitor build status
- Provide the Jenkins web interface

### Simple Example

```text
             Jenkins Controller
                     |
             Schedules Build
                     |
          Assigns Work to Agent
                     |
                     ↓
                  Agent
                     |
               Runs Commands
```

---

# 6. Jenkins Agent

A **Jenkins Agent** is a machine that performs the actual build, test, and deployment tasks assigned by the controller.

Agents can be:

- Linux servers
- Windows servers
- Cloud instances
- Virtual machines
- Docker containers
- Kubernetes pods

Example:

```text
Jenkins Controller
        |
        +------ Linux Agent
        |
        +------ Windows Agent
        |
        +------ Docker Agent
```

---

# 7. Controller vs Agent

| Component | Responsibility |
|---|---|
| Jenkins Controller | Manages and schedules jobs |
| Jenkins Agent | Executes jobs |
| Controller | Stores configuration |
| Agent | Performs build/test/deployment work |
| Controller | Assigns tasks |
| Agent | Runs commands |

### Simple Explanation

```text
Controller = Brain

Agent = Worker
```

The controller decides **what needs to be done**, while the agent performs the actual work.

---

# 8. Jenkins Architecture Diagram

A basic Jenkins architecture can be represented as:

```text
                         Developer
                             |
                             |
                         git push
                             |
                             ↓
                    +----------------+
                    |    GitHub      |
                    +----------------+
                             |
                             | Webhook / Polling
                             ↓
                 +------------------------+
                 |   Jenkins Controller   |
                 |                        |
                 |  - Jobs                |
                 |  - Pipelines           |
                 |  - Credentials         |
                 |  - Scheduling          |
                 |  - Plugins             |
                 +------------------------+
                       /     |      \
                      /      |       \
                     ↓       ↓        ↓
              +---------+ +---------+ +---------+
              | Agent 1 | | Agent 2 | | Agent 3 |
              |  Linux  | | Windows | | Docker  |
              +---------+ +---------+ +---------+
                   |          |           |
                   ↓          ↓           ↓
                Build       Test       Deploy
```

---

# 9. Jenkins CI/CD Architecture

A more complete architecture can look like:

```text
                         Developer
                             |
                             ↓
                         GitHub
                             |
                          Webhook
                             |
                             ↓
                  +----------------------+
                  | Jenkins Controller   |
                  +----------------------+
                             |
                    Pipeline Execution
                             |
          +------------------+------------------+
          |                  |                  |
          ↓                  ↓                  ↓
     Build Agent        Test Agent         Deploy Agent
          |                  |                  |
          ↓                  ↓                  ↓
       Build App          Run Tests         Deploy App
          |                  |                  |
          +------------------+------------------+
                             |
                             ↓
                     Docker Registry
                             |
                             ↓
                       Application
```

---

# 10. Jenkins Workflow

A typical Jenkins workflow is:

```text
1. Developer writes code
          ↓
2. Developer pushes code to GitHub
          ↓
3. GitHub sends webhook to Jenkins
          ↓
4. Jenkins starts pipeline
          ↓
5. Jenkins checks out code
          ↓
6. Application is built
          ↓
7. Tests are executed
          ↓
8. Docker image is created
          ↓
9. Image is pushed to registry
          ↓
10. Application is deployed
```

---

# 11. Jenkins Job

A **Jenkins Job** is a task configured inside Jenkins.

A job can perform actions such as:

```text
Build an application
Run tests
Execute shell commands
Build Docker images
Deploy applications
Run Ansible playbooks
```

Example:

```text
Job Name:
travel-app-build
```

The job could execute:

```bash
docker build -t travel-app:v1 .
```

---

# 12. Jenkins Pipeline

A **Jenkins Pipeline** defines the complete CI/CD workflow as code.

Pipeline configuration is commonly stored in a file called:

```text
Jenkinsfile
```

Example:

```text
Project
│
├── src/
├── Dockerfile
├── docker-compose.yml
└── Jenkinsfile
```

The Jenkinsfile can define:

```text
Checkout
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

---

# 13. Jenkinsfile

A `Jenkinsfile` is a text file that defines the Jenkins pipeline.

Example:

```groovy
pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

---

# 14. Jenkins Pipeline Stages

A pipeline is usually divided into multiple stages.

Example:

```text
Pipeline
   |
   +--- Checkout
   |
   +--- Build
   |
   +--- Test
   |
   +--- Docker Build
   |
   +--- Docker Push
   |
   +--- Deploy
```

Each stage represents a specific part of the CI/CD process.

---

# 15. Jenkins and GitHub

Jenkins can integrate with GitHub.

The basic workflow is:

```text
Developer
    |
    ↓
git push
    |
    ↓
GitHub Repository
    |
    ↓
Webhook
    |
    ↓
Jenkins
    |
    ↓
Pipeline
```

A webhook allows GitHub to notify Jenkins when changes occur.

---

# 16. Jenkins Webhook

A webhook can automatically trigger Jenkins when code is pushed.

Example:

```text
Developer
    |
    | git push
    ↓
GitHub
    |
    | HTTP Webhook
    ↓
Jenkins
    |
    ↓
Pipeline Starts
```

Without a webhook, Jenkins can also be configured to periodically check the repository for changes.

---

# 17. Jenkins and Docker

Jenkins can be used to automate Docker workflows.

Example:

```text
GitHub
   ↓
Jenkins
   ↓
Docker Build
   ↓
Docker Image
   ↓
Docker Registry
   ↓
Deployment Server
   ↓
Docker Container
```

Example commands:

```bash
docker build -t myapp:v1 .
```

```bash
docker login
```

```bash
docker push username/myapp:v1
```

On the deployment server:

```bash
docker pull username/myapp:v1
```

```bash
docker run -d -p 8080:80 username/myapp:v1
```

---

# 18. Jenkins and Ansible

Jenkins can also work with Ansible.

For example:

```text
Jenkins
   |
   ↓
Ansible Playbook
   |
   ↓
Deployment Server
   |
   ↓
Application
```

Jenkins can trigger commands such as:

```bash
ansible-playbook deploy.yml
```

This allows Jenkins to act as the CI/CD automation engine while Ansible handles configuration and deployment tasks.

---

# 19. Jenkins and Kubernetes

Jenkins can also be integrated with Kubernetes.

A possible workflow is:

```text
Developer
    ↓
GitHub
    ↓
Jenkins
    ↓
Build
    ↓
Test
    ↓
Docker Image
    ↓
Container Registry
    ↓
Kubernetes
    ↓
Application Pods
```

Jenkins can trigger Kubernetes deployments using:

```text
kubectl
```

or through Kubernetes-related Jenkins integrations.

---

# 20. Jenkins Plugins

Jenkins provides a large plugin ecosystem.

Plugins allow Jenkins to integrate with external tools and services.

Examples:

```text
Git
GitHub
Docker
Pipeline
Credentials
SSH
Kubernetes
Ansible
SonarQube
Slack
```

Plugins extend Jenkins functionality without requiring Jenkins itself to implement every integration.

---

# 21. Jenkins Credentials

Jenkins can securely store credentials used by pipelines.

Examples:

```text
Username / Password
SSH Private Key
API Token
Secret Text
Certificates
```

Example use cases:

```text
Jenkins → GitHub
Jenkins → Docker Hub
Jenkins → Linux Server
Jenkins → AWS
Jenkins → Kubernetes
```

Credentials should not be directly written inside the Jenkinsfile.

Avoid:

```groovy
password = "mypassword123"
```

Instead, use the Jenkins Credentials system.

---

# 22. Jenkins Workspace

A Jenkins **workspace** is a directory where Jenkins performs work for a job.

For example:

```text
/var/lib/jenkins/workspace/my-project/
```

A workspace can contain:

```text
Source Code
Dockerfile
Jenkinsfile
Configuration Files
Build Files
```

When Jenkins checks out source code, it normally places the files inside the job's workspace.

---

# 23. Jenkins Home Directory

The Jenkins home directory stores important Jenkins data.

On many Linux installations, it is:

```text
/var/lib/jenkins
```

It can contain:

```text
jobs/
plugins/
workspace/
credentials/
logs/
configurations
```

The exact location can vary depending on how Jenkins was installed and configured.

To check the Jenkins home directory:

```bash
echo $JENKINS_HOME
```

If Jenkins is running as a service, you can also inspect its service configuration.

---

# 24. Jenkins Port

Jenkins commonly runs on:

```text
8080
```

You can access Jenkins using:

```text
http://SERVER_IP:8080
```

For example:

```text
http://192.168.1.100:8080
```

The port can be changed according to the Jenkins configuration.

To check whether port `8080` is listening on Linux:

```bash
sudo ss -tulpn | grep :8080
```

Another command:

```bash
sudo lsof -i :8080
```

---

# 25. Jenkins Service Commands

On systems using systemd:

### Check Jenkins status

```bash
sudo systemctl status jenkins
```

### Start Jenkins

```bash
sudo systemctl start jenkins
```

### Stop Jenkins

```bash
sudo systemctl stop jenkins
```

### Restart Jenkins

```bash
sudo systemctl restart jenkins
```

### Enable Jenkins at boot

```bash
sudo systemctl enable jenkins
```

---

# 26. Jenkins Logs

To view Jenkins service logs:

```bash
sudo journalctl -u jenkins
```

To follow logs in real time:

```bash
sudo journalctl -u jenkins -f
```

This can be useful when troubleshooting Jenkins startup or pipeline-related issues.

---

# 27. Jenkins Build Lifecycle

A simplified Jenkins build lifecycle is:

```text
Trigger
  ↓
Queue
  ↓
Agent Allocation
  ↓
Workspace
  ↓
Checkout
  ↓
Build
  ↓
Test
  ↓
Package
  ↓
Deploy
  ↓
Result
```

The result can be:

```text
SUCCESS
FAILURE
ABORTED
UNSTABLE
```

---

# 28. Jenkins Architecture with Dev, Staging and Production

Jenkins can manage deployments across multiple environments.

```text
                         GitHub
                            |
                            ↓
                   Jenkins Controller
                            |
                  +---------+---------+
                  |         |         |
                  ↓         ↓         ↓
               Dev Agent  Stage Agent Prod Agent
                  |         |         |
                  ↓         ↓         ↓
                 DEV     STAGING   PRODUCTION
                  |         |         |
                  +---------+---------+
                            |
                            ↓
                       Monitoring
```

A more practical deployment flow:

```text
Developer
    ↓
GitHub
    ↓
Jenkins
    ↓
Build
    ↓
Test
    ↓
Docker Image
    ↓
Docker Registry
    ↓
DEV
    ↓
STAGING
    ↓
Approval
    ↓
PRODUCTION
```

---

# 29. Jenkins Controller-Agent Communication

The controller needs to communicate with agents.

Depending on the setup, Jenkins agents can connect using different methods.

Common approaches include:

```text
SSH
Inbound Agent Connection
WebSocket
Cloud/Kubernetes integrations
```

Example using SSH:

```text
Jenkins Controller
        |
        | SSH
        ↓
Linux Jenkins Agent
        |
        ↓
Execute Build
```

---

# 30. Why Use Jenkins Agents?

Using agents provides several advantages.

### 1. Different operating systems

```text
Controller
    |
    +--- Linux Agent
    +--- Windows Agent
```

### 2. Different environments

```text
Controller
    |
    +--- Docker Agent
    +--- Kubernetes Agent
```

### 3. Distributed workloads

Different builds can run on different machines.

```text
             Controller
            /     |     \
           ↓      ↓      ↓
        Agent1  Agent2  Agent3
```

This reduces the workload on the controller.

---

# 31. Recommended Jenkins Architecture

For a larger environment, it is generally better to avoid using the controller as the main build machine.

A better architecture is:

```text
                    Developers
                        |
                        ↓
                     GitHub
                        |
                        ↓
               Jenkins Controller
                        |
          +-------------+-------------+
          |             |             |
          ↓             ↓             ↓
     Linux Agent   Docker Agent   Windows Agent
          |             |             |
          ↓             ↓             ↓
       Build/Test    Build Image     Testing
                        |
                        ↓
                 Docker Registry
                        |
                        ↓
                 Deployment Server
```

---

# 32. Example Real-World Jenkins Pipeline

Suppose we have a travel application.

The architecture could be:

```text
Developer
    |
    ↓
GitHub Repository
    |
    ↓
Jenkins Controller
    |
    ↓
Jenkins Agent
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
Docker Hub
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

The Docker image could be tagged:

```text
travel-app:v1
```

Then Jenkins can deploy the same image across environments.

---

# 33. Jenkins Pipeline Example

A basic pipeline:

```groovy
pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }

        stage('Test') {
            steps {
                echo 'Running tests...'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t travel-app:${BUILD_NUMBER} .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
            }
        }
    }
}
```

---

# 34. Jenkins Environment Variables

Jenkins provides built-in environment variables.

Some commonly used variables are:

```text
BUILD_NUMBER
BUILD_ID
BUILD_TAG
JOB_NAME
WORKSPACE
JENKINS_HOME
```

Example:

```groovy
echo "Build Number: ${BUILD_NUMBER}"
```

This can be useful for creating versioned Docker images.

Example:

```bash
docker build -t travel-app:v${BUILD_NUMBER} .
```

---

# 35. Jenkins Build Number

Jenkins automatically assigns build numbers.

Example:

```text
Build #1
Build #2
Build #3
Build #4
```

These can be used to identify pipeline executions.

For example:

```text
travel-app:v1
travel-app:v2
travel-app:v3
```

This is useful for tracking application versions.

---

# 36. Jenkins Advantages

### Automation

Reduces repetitive manual work.

### CI/CD Support

Supports continuous integration and continuous delivery/deployment.

### Large Plugin Ecosystem

Can integrate with many DevOps tools.

### Pipeline as Code

Jenkinsfiles allow pipelines to be stored in Git.

### Distributed Builds

Jobs can run on multiple agents.

### Flexible

Can be used with:

```text
Docker
Kubernetes
Ansible
AWS
GitHub
GitLab
Terraform
```

---

# 37. Jenkins Limitations

Jenkins is powerful but also has some challenges.

### 1. Maintenance

Jenkins itself needs to be maintained.

### 2. Plugin Management

A large number of plugins can increase management complexity.

### 3. Configuration

Large Jenkins installations can become complicated.

### 4. Security

Credentials, agents, plugins, and access permissions need to be properly secured.

### 5. Infrastructure

Self-hosted Jenkins requires infrastructure and resources.

---

# 38. Jenkins vs GitHub Actions

| Feature | Jenkins | GitHub Actions |
|---|---|---|
| Hosting | Usually self-hosted | GitHub-hosted or self-hosted |
| Pipeline | Jenkinsfile | YAML workflow |
| Plugins | Large ecosystem | Actions marketplace |
| GitHub integration | Supported | Native |
| Infrastructure management | Usually required | Less infrastructure management |
| Customization | Very high | High |
| CI/CD | Yes | Yes |

Both can be used to implement CI/CD pipelines.

---

# 39. Jenkins Security Best Practices

Important Jenkins security practices include:

- Use strong authentication.
- Use role-based access control.
- Store credentials in Jenkins Credentials.
- Keep Jenkins and plugins updated.
- Do not expose Jenkins unnecessarily to the public internet.
- Use HTTPS where appropriate.
- Restrict agent access.
- Use least-privilege permissions.
- Protect Jenkins credentials.
- Regularly back up Jenkins configuration.

---

# 40. Jenkins Architecture — Final Overview

```text
                         DEVELOPER
                             |
                             ↓
                          GitHub
                             |
                         Webhook
                             |
                             ↓
                +-------------------------+
                |   JENKINS CONTROLLER    |
                |                         |
                |  Pipeline Management    |
                |  Job Scheduling         |
                |  Credentials            |
                |  Plugins                |
                +-------------------------+
                    /        |        \
                   /         |         \
                  ↓          ↓          ↓
             Linux Agent  Docker Agent  Windows Agent
                  |          |          |
                  ↓          ↓          ↓
                Build      Docker      Testing
                Test       Build
                  |          |
                  +----------+
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
                 APPROVAL
                       |
                       ↓
                  PRODUCTION
                       |
                       ↓
                    USERS
```

---

# 41. Key Jenkins Concepts to Remember

```text
Jenkins
↓
Automation Server

Controller
↓
Manages Jenkins and schedules work

Agent
↓
Executes the work

Job
↓
A task configured in Jenkins

Pipeline
↓
Automated CI/CD workflow

Jenkinsfile
↓
Pipeline as Code

Webhook
↓
Automatically triggers Jenkins from Git events

Workspace
↓
Directory where Jenkins performs job work

Credentials
↓
Securely stores secrets

Plugin
↓
Extends Jenkins functionality
```

---

# 42. Final Jenkins CI/CD Flow

The complete concept can be summarized as:

```text
Developer
    ↓
Write Code
    ↓
Git Push
    ↓
GitHub
    ↓
Webhook
    ↓
Jenkins Controller
    ↓
Jenkins Agent
    ↓
Checkout
    ↓
Build
    ↓
Test
    ↓
Security / Quality Checks
    ↓
Docker Build
    ↓
Docker Registry
    ↓
DEV
    ↓
STAGING
    ↓
Approval
    ↓
PRODUCTION
    ↓
Users
```

Jenkins acts as the **automation engine** that connects source control, build systems, testing, containers, infrastructure, and deployment into a repeatable CI/CD workflow.