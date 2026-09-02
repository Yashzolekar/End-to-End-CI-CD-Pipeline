
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


# 🛠️ Project Structure

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

```

# ✅ Prerequisites

Before starting this project, make sure you have:

1. ☁️ **AWS Account**
2. 🐙 **GitHub Account**
3. 🐳 **Docker Hub Account**
4. 🐧 **Basic Linux Knowledge**
5. 🔀 **Basic Git Knowledge**
6. 🐳 **Basic Docker Knowledge**
7. ☸️ **Basic Kubernetes Knowledge**
8. 🔐 **AWS IAM Permissions**

# 🚀 Setup Guide
Step 1 — Launch Jenkins EC2 Instance

Launch an EC2 instance in AWS.

Recommended:

```text
AMI: Ubuntu
Instance Type: t3.medium
Storage: 20 GB+
```
Configure the Security Group.

Allow:
```text
SSH     → TCP 22
Jenkins → TCP 8080
HTTP    → TCP 80
```
For production environments, restrict access to trusted IP addresses instead of allowing unrestricted access.

Connect to the EC2 instance:
```text
ssh -i "your-key.pem" ubuntu@YOUR_EC2_PUBLIC_IP
```
Step 2 — Update Ubuntu
```text
sudo apt update
sudo apt upgrade -y
```
Step 3 — Install Java

Jenkins requires Java.
```text
sudo apt install fontconfig openjdk-17-jre -y
```
Verify:
```text
java -version
```
Step 4 — Install Jenkins

Add the Jenkins repository and install Jenkins.
```text
sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```
```text
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```
```text
sudo apt update
sudo apt install jenkins -y
```
Start Jenkins:
```text
sudo systemctl enable jenkins
sudo systemctl start jenkins
```
Check status:
```text
sudo systemctl status jenkins
```
Step 5 — Install Git
```text
sudo apt install git -y
```
Verify:
```text
git --version
```

Step 6 — Install Docker
```text
sudo apt install docker.io -y
```
Start Docker:
```text
sudo systemctl enable docker
sudo systemctl start docker
```
Check:
```text
docker --version
```
Allow Jenkins to use Docker:
```text
sudo usermod -aG docker jenkins
```
Restart Jenkins:
```text
sudo systemctl restart jenkins
```
You may need to log out and back in for group membership changes to take effect.

Step 7 — Install Maven

If your application uses Maven:

```text
sudo apt install maven -y
```
Verify:
```text
mvn -version
```

Step 8 — Install kubectl

Install the Kubernetes command-line tool.
```text
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```
Verify:
```text
kubectl version --client
```

🔐 Step 9 — Configure Docker Hub

Create a Docker Hub account.

Then create a repository, for example:
```text
yashzolekar/cicd-app
```

In Jenkins:
```text
Dashboard
   ↓
Manage Jenkins
   ↓
Credentials
   ↓
Global
   ↓
Add Credentials
```

Add Docker Hub credentials.

Use:
```text
Kind: Username with password
Username: YOUR_DOCKER_USERNAME
Password: YOUR_DOCKER_TOKEN
ID: dockerhub-credentials
```

Use a Docker Hub access token rather than storing your account password in Jenkins.


☁️ Step 10 — Create IAM Roles for EKS

Create the required IAM roles for Amazon EKS.

A. EKS Cluster Role

Trusted entity:
```text
AWS Service → EKS
```
Attach the appropriate EKS cluster policy.

Example:
```text
AmazonEKSClusterPolicy
```
Role name:
```text
eks-cluster-role
```


B. EKS Node Group Role

Trusted entity:
```text
AWS Service → EC2
```
Attach the required worker-node policies.

Common policies include:
```text
AmazonEKSWorkerNodePolicy
AmazonEC2ContainerRegistryPullOnly
AmazonEKS_CNI_Policy
```
Role name:
```text
eks-node-role
```
Use the current AWS-recommended policies and permissions for your EKS configuration.
