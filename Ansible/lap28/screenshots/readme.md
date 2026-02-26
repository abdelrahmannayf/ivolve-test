
🚀 DevOps Automation Project — Lab 28
Structured Configuration Management Using Ansible Roles
📌 Project Overview

This project demonstrates Infrastructure Automation using Ansible Roles to deploy and configure DevOps tools automatically.

The following tools were automated:

🐳 Docker

☸️ Kubernetes CLI (kubectl)

☕ Jenkins

The goal is to build scalable and reusable configuration management using DevOps best practices.

🏗 Architecture
                 GitHub / Source Code
                          ↓
                   Ansible Control Node
                        (REDHAT1)
                          ↓ SSH
                -------------------------
                |                       |
        Docker + Jenkins + Kubectl Installation
                |                       |
         Managed Node (Servers / VMs)
🖥 Lab Environment
Component	Description
Control Node	REDHAT1
Managed Node	192.168.86.136
Automation Tool	Ansible Core
OS	RHEL / Rocky Linux
🟢 Prerequisites

2 Virtual Machines

SSH Passwordless Authentication

Ansible Installed

🟢 Install Ansible
sudo dnf install ansible-core -y

Verify:

ansible --version
🟢 Create Inventory File
nano inventory.ini

Example:

[myservers]
192.168.86.136 ansible_user=nayf

Test connection:

ansible -i inventory.ini myservers -m ping

Expected Output:

pong
🟢 Create Roles Structure
ansible-galaxy init roles/docker
ansible-galaxy init roles/kubectl
ansible-galaxy init roles/jenkins

Project Structure:

roles/
 ├── docker/
 ├── kubectl/
 └── jenkins/
🐳 Docker Role

Install Docker CE:

- name: Install Docker
  dnf:
    name:
      - docker-ce
      - docker-ce-cli
      - containerd.io
    state: present

- name: Start Docker Service
  service:
    name: docker
    state: started
    enabled: yes
☸️ Kubectl Role
- name: Add Kubernetes Repository
  copy:
    dest: /etc/yum.repos.d/kubernetes.repo
    content: |
      [kubernetes]
      name=Kubernetes
      baseurl=https://pkgs.k8s.io/core:/stable:/v1.29/rpm/
      enabled=1
      gpgcheck=1

- name: Install kubectl
  dnf:
    name: kubectl
    state: present
☕ Jenkins Role

Install Java:

- name: Install Java
  dnf:
    name: java-11-openjdk
    state: present

Add Jenkins Repo:

- name: Add Jenkins Repository
  get_url:
    url: https://pkg.jenkins.io/redhat-stable/jenkins.repo
    dest: /etc/yum.repos.d/jenkins.repo

Install Jenkins:

- name: Install Jenkins
  dnf:
    name: jenkins
    state: present

- name: Start Jenkins
  service:
    name: jenkins
    state: started
    enabled: yes
🟢 Main Playbook
---
- name: Configure DevOps Tools
  hosts: myservers
  become: yes

  roles:
    - docker
    - kubectl
    - jenkins

Save as:

site.yml
🟢 Run Automation
ansible-playbook -i inventory.ini site.yml --ask-become-pass
🟢 Verification Commands

Check Docker:

docker --version

Check Kubectl:

kubectl version --client

Check Jenkins:

systemctl status jenkins

Open Jenkins UI:

http://MANAGED_NODE_IP:8080
🟢 Key DevOps Concepts Learned

✅ Infrastructure as Code
✅ Automation using Ansible Roles
✅ Idempotent Configuration
✅ Scalable Deployment Structure

🟢 Troubleshooting
If Package Not Found

Check repositories:

dnf repolist
If Jenkins Failed

Verify Java installation:

java -version
🏁 Conclusion

This lab demonstrates professional DevOps automation using Ansible Roles to manage infrastructure services in a structured and scalable way.
