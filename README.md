
# 🚀 End-to-End CI/CD Pipeline with Docker, Jenkins & Kubernetes

![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?style=for-the-badge&logo=jenkins&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-Container-2496ED?style=for-the-badge&logo=docker&logoColor=white)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-326CE5?style=for-the-badge&logo=kubernetes&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-Cloud-232F3E?style=for-the-badge&logo=amazonaws&logoColor=white)
![EKS](https://img.shields.io/badge/Amazon-EKS-FF9900?style=for-the-badge&logo=amazoneks&logoColor=white)
![GitHub](https://img.shields.io/badge/GitHub-Repository-181717?style=for-the-badge&logo=github&logoColor=white)

---

## 📌 Project Overview

This project demonstrates an end-to-end CI/CD pipeline that automatically builds, containerizes, and deploys an application.

The pipeline uses:

- 🐙 GitHub for source code management
- 🔧 Jenkins for CI/CD automation
- 🐳 Docker for containerization
- 📦 Docker Hub as the container registry
- ☸️ Kubernetes for container orchestration
- ☁️ Amazon EKS for managed Kubernetes
- 🖥️ AWS EC2 as the Jenkins server

Whenever new code is pushed to GitHub, Jenkins can automatically trigger the pipeline.

---

# 🏗️ Architecture Overview

                    👨‍💻 Developer
                         |
                         | Git Push
                         ↓
                    🐙 GitHub
                         |
                         | Webhook
                         ↓
                   🔧 Jenkins
                    (AWS EC2)
                         |
              +----------+----------+
              |                     |
              ↓                     ↓
          Build/Test          Docker Build
                                    |
                                    ↓
                              🐳 Docker Image
                                    |
                                    ↓
                              Docker Hub
                                    |
                                    ↓
                              Amazon EKS
                                    |
                         +----------+----------+
                         |                     |
                         ↓                     ↓
                   Kubernetes            Kubernetes
                    Deployment             Service
                         |                     |
                         +----------+----------+
                                    |
                                    ↓
                              🌐 Application

---
## 🛠️ Technologies Used


| Technology    | Purpose                 |
| ------------- | ----------------------- |
| ☁️ AWS EC2    | Jenkins Server          |
| ☸️ AWS EKS    | Kubernetes Cluster      |
| 🔧 Jenkins    | CI/CD Automation        |
| 🐳 Docker     | Containerization        |
| 📦 Docker Hub | Container Registry      |
| ☸️ Kubernetes | Container Orchestration |
| 🐙 GitHub     | Source Code Management  |
| 🐧 Linux      | Server Operating System |
| ⚙️ kubectl    | Kubernetes CLI          |
| 🔀 Git        | Version Control         |
| ☕ Maven       | Application Build Tool  |

## 🛠️ Project Structure
```text
End-to-End-CI-CD-Pipeline/
│
├── src/
│   └── main/
│       └── ...
│
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
│
├── Dockerfile
├── Jenkinsfile
├── pom.xml
├── .gitignore
└── README.md
