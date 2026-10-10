# Lab 69: Automated Jenkins Installation on Virtual Machines Using Ansible Playbook

![Ansible](https://img.shields.io/badge/Ansible-Automation-EE0000?style=for-the-badge&logo=ansible&logoColor=white)
![Jenkins](https://img.shields.io/badge/Jenkins-LTS-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20LTS-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)

---

## 1. Objective & Overview

The objective of this lab is to automate the installation and configuration of a **Jenkins LTS Automation Controller** on remote Linux virtual machines using an **Ansible Playbook** (`69-Ansible-playbook-to-install-jenkins-on-vm.yml`).

Rather than manually running commands on each server (updating apt, installing Java OpenJDK 17, adding external GPG keys, configuring APT repositories, and enabling systemd daemons), Ansible enforces **idempotent Infrastructure as Code (IaC)**.

---

## 2. Infrastructure & Target Architecture

| Component | Target Specification |
| :--- | :--- |
| **Control Node** | WSL / Ubuntu Workstation running Ansible 2.14+ |
| **Managed Node** | Linux Virtual Machine (`Ubuntu 22.04` / `Debian 12`) |
| **Target Port** | `8080` (Default Jenkins Web UI) |
| **Runtime Dependency** | OpenJDK 17 (`openjdk-17-jdk`) |
| **Repository Source** | Official Jenkins Debian-stable repository (`pkg.jenkins.io`) |

---

## 3. Playbook Workflow Breakdown

```text
┌────────────────────────────────────────────────────────┐
│  Ansible Control Node (ansible-playbook -i hosts.ini)  │
└───────────────────────────┬────────────────────────────┘
                            │ SSH (Port 22, become: yes)
                            ▼
┌────────────────────────────────────────────────────────┐
│  Target Virtual Machine                                │
│                                                        │
│  [Step 1] apt update                                   │
│     │                                                  │
│  [Step 2] apt install openjdk-17-jdk curl gnupg        │
│     │                                                  │
│  [Step 3] Download official Jenkins GPG Key            │
│     │     (/etc/apt/keyrings/jenkins-keyring.asc)      │
│     │                                                  │
│  [Step 4] Add Jenkins APT Source Repository            │
│     │     (deb [signed-by=...] pkg.jenkins.io/...)     │
│     │                                                  │
│  [Step 5] apt install jenkins                          │
│     │                                                  │
│  [Step 6] systemd: enable & start jenkins.service      │
│     │                                                  │
│  [Step 7] wait_for port 8080 listening                 │
│     │                                                  │
│  [Step 8] Read initial administrator bootstrap token   │
│           (/var/lib/jenkins/secrets/initialAdminPassword)
└────────────────────────────────────────────────────────┘
```

---

## 4. Complete Ansible Playbook Source (`69-Ansible-playbook-to-install-jenkins-on-vm.yml`)

```yaml
---
- name: Ansible Playbook to Install Jenkins on Virtual Machine
  hosts: all
  become: yes

  vars:
    jenkins_java_package: openjdk-17-jdk
    jenkins_port: 8080
    jenkins_keyring_dir: /etc/apt/keyrings
    jenkins_gpg_key_url: https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    jenkins_gpg_dest: /etc/apt/keyrings/jenkins-keyring.asc
    jenkins_apt_repo: "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/"

  tasks:
    - name: 1. Update apt repository cache
      apt:
        update_cache: yes
        cache_valid_time: 3600

    - name: 2. Install required prerequisite packages
      apt:
        name:
          - "{{ jenkins_java_package }}"
          - fontconfig
          - curl
          - gnupg
          - ca-certificates
        state: present

    - name: 3. Create keyrings directory
      file:
        path: "{{ jenkins_keyring_dir }}"
        state: directory
        mode: '0755'

    - name: 4. Download Jenkins official ASCII-armored GPG key
      get_url:
        url: "{{ jenkins_gpg_key_url }}"
        dest: "{{ jenkins_gpg_dest }}"
        mode: '0644'

    - name: 5. Add Jenkins Debian-stable repository source list
      apt_repository:
        repo: "{{ jenkins_apt_repo }}"
        state: present
        filename: jenkins

    - name: 6. Update apt cache after adding repository
      apt:
        update_cache: yes

    - name: 7. Install Jenkins package
      apt:
        name: jenkins
        state: present

    - name: 8. Ensure Jenkins service is started and enabled on boot
      systemd:
        name: jenkins
        state: started
        enabled: yes

    - name: 9. Wait for Jenkins to start listening on port {{ jenkins_port }}
      wait_for:
        port: "{{ jenkins_port }}"
        delay: 5
        timeout: 120

    - name: 10. Read Jenkins initial administrator password
      slurp:
        src: /var/lib/jenkins/secrets/initialAdminPassword
      register: initial_admin_password
      ignore_errors: yes

    - name: 11. Print setup completion summary
      debug:
        msg:
          - "=========================================================="
          - "Jenkins has been successfully installed and started!"
          - "Jenkins Web UI: http://{{ ansible_host | default(inventory_hostname) }}:{{ jenkins_port }}"
          - "Initial Admin Password: {{ (initial_admin_password.content | b64decode | trim) if initial_admin_password.content is defined else 'Check /var/lib/jenkins/secrets/initialAdminPassword' }}"
          - "=========================================================="
```

---

## 5. Execution & Verification

### Running the Playbook
To execute against managed inventory hosts:

```bash
ansible-playbook -i hosts.ini 69-Ansible-playbook-to-install-jenkins-on-vm.yml
```

### Verifying Service Health
Once complete, verify that the Jenkins daemon is active and listening:

```bash
# Check systemd status
systemctl status jenkins --no-pager

# Check listening port
ss -tulpn | grep 8080

# HTTP probe
curl -I http://localhost:8080/login
# Expected: HTTP/1.1 200 OK
```

---

## 6. Key Takeaways
- **Declarative State Enforcement:** If Jenkins is already installed and running, re-running the playbook reports `ok=... changed=0` without disrupting running builds.
- **Dependency Sequencing:** Java 17 must be installed prior to Jenkins execution; the playbook strictly sequences dependencies.
- **Secure Repository Signing:** Utilizes modern ASCII keyring files in `/etc/apt/keyrings` conforming to Debian security best practices.
