
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
☸️ Step 11 — Create EKS Cluster

Go to:
```text
AWS Console
     ↓
EKS
     ↓
Create Cluster
```
Configure:
```text
Cluster Name:
cicd-eks-cluster

Kubernetes:
Select a currently supported version

Cluster IAM Role:
eks-cluster-role

Networking:
Select your VPC and subnets
```
Create the cluster.

Wait until the cluster becomes:
```text
ACTIVE
```
Step 12 — Create EKS Node Group

Open:
```text
EKS
 ↓
Your Cluster
 ↓
Compute
 ↓
Add Node Group
```
Example:
```text
Node Group Name:
eks-node-group

IAM Role:
eks-node-role

AMI:
Amazon Linux

Instance Type:
t3.medium
```
Configure the desired capacity according to your AWS budget.

Create the node group.

Wait until the nodes become ready


Step 13 — Configure kubectl

On the Jenkins server, configure access to your EKS cluster.

Install/configure AWS CLI if required, then run:
```text
aws eks update-kubeconfig \
  --region YOUR_AWS_REGION \
  --name cicd-eks-cluster
```
Example:
```text
aws eks update-kubeconfig \
  --region ap-southeast-2 \
  --name cicd-eks-cluster
```

Check the connection:
```text
kubectl get nodes
```
Expected:
```text
NAME              STATUS   ROLES
ip-xxx-xxx-xxx    Ready    <none>
ip-xxx-xxx-xxx    Ready    <none>
```
🐳 Step 14 — Create Dockerfile

Example:
```text
FROM eclipse-temurin:17-jdk

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]

```

☸️ Step 15 — Create Kubernetes Deployment

Create:
```text
k8s/deployment.yaml
```
Example:
```text
apiVersion: apps/v1
kind: Deployment

metadata:
  name: cicd-app

spec:
  replicas: 2

  selector:
    matchLabels:
      app: cicd-app

  template:
    metadata:
      labels:
        app: cicd-app

    spec:
      containers:
        - name: cicd-app

          image: YOUR_DOCKER_USERNAME/cicd-app:latest

          ports:
            - containerPort: 8080
```

🌐 Step 16 — Create Kubernetes Service

Create:
```text
k8s/service.yaml

```
```text
apiVersion: v1
kind: Service

metadata:
  name: cicd-app-service

spec:
  type: LoadBalancer

  selector:
    app: cicd-app

  ports:
    - port: 80
      targetPort: 8080
```

🔧 Step 17 — Create Jenkinsfile

Create:
```text
Jenkinsfile
```
Example:
```text
pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "YOUR_DOCKER_USERNAME/cicd-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .'
            }
        }

        stage('Docker Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin

                        docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}

                        docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} \
                        ${DOCKER_IMAGE}:latest

                        docker push ${DOCKER_IMAGE}:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml

                    kubectl rollout status deployment/cicd-app
                '''
            }
        }
    }

    post {
        success {
            echo '🚀 Deployment Successful!'
        }

        failure {
            echo '❌ Pipeline Failed!'
        }
    }
}
```
⚠️ Replace the username, repository name, Docker image name, AWS region, and Jenkins credential IDs with your own values.

🔗 Step 18 — Connect GitHub to Jenkins

Open Jenkins:
```text
http://YOUR_EC2_PUBLIC_IP:8080
```
Create a new Pipeline job.

Select:
```text
New Item
 ↓
Pipeline
```

Under:
```text
Pipeline
 ↓
Definition
```
Select:
```text
Pipeline script from SCM
```
Choose:
```text
SCM:
Git
```
Add your GitHub repository:
```text
https://github.com/YOUR_USERNAME/YOUR_REPOSITORY.git
```
Branch:
```text
*/main
```
Script Path:
```text
Jenkinsfile
```
Save the job.

🚀 Step 19 — Run the Pipeline

Click:
```text
Build Now
```
The pipeline should execute:
```text
Checkout
   ↓
Build
   ↓
Docker Build
   ↓
Docker Push
   ↓
Kubernetes Deployment
```

🔍 Step 20 — Verify Kubernetes Deployment

Check pods:
```text
kubectl get pods
```
Check deployment:
```text
kubectl get deployments
```
Check service:
```text
kubectl get svc
```
Example:
```text
NAME               TYPE           EXTERNAL-IP
cicd-app-service   LoadBalancer   xxx.amazonaws.com
```
Copy the LoadBalancer address and open it in your browser.
```text
http://EXTERNAL-LOAD-BALANCER
```
🎉 Your application is now deployed

🔄 CI/CD Workflow

Whenever the developer pushes new code:
```text
Developer
    ↓
GitHub Push
    ↓
Jenkins Trigger
    ↓
Checkout Code
    ↓
Build Application
    ↓
Run Tests
    ↓
Build Docker Image
    ↓
Push Image to Docker Hub
    ↓
Deploy to Kubernetes
    ↓
AWS EKS
    ↓
Application Updated 🚀
```




📊 Useful Commands
Check Jenkins
```text
sudo systemctl status jenkins
```
Check Docker
```text
docker ps
```
Check Kubernetes Nodes
```text
kubectl get nodes
```
Check Pods
```text
kubectl get pods
```
Check Services
```text
kubectl get svc
```
Check Deployment
```text
kubectl get deployment
```
View Pod Logs
```text
kubectl logs POD_NAME
```



🧹 Cleanup

To remove the Kubernetes application:
```text
kubectl delete -f k8s/
```
To delete the EKS cluster:
```text
AWS Console
    ↓
EKS
    ↓
Clusters
    ↓
Delete
```

Also remove unused:
```text
EC2 instances
Load Balancers
EKS node groups
EKS cluster
Elastic IPs
Other billable AWS resources
```
⚠️ Always check your AWS resources after completing the project to avoid unexpected charges.


# 🎯 Project Outcomes

Through this project, I practiced:

- ✅ Git & GitHub
- ✅ Jenkins CI/CD
- ✅ Docker
- ✅ Docker Hub
- ✅ Kubernetes
- ✅ Amazon EKS
- ✅ AWS EC2
- ✅ IAM
- ✅ Linux
- ✅ CI/CD Automation
- ✅ Container Deployment
- ✅ Kubernetes Application Management

---

# 🌐 Portfolio

🚀 **Portfolio:**  
https://yashz-portfolio.vercel.app/

---

# 🤝 Connect With Me

💼 **LinkedIn:**  
https://www.linkedin.com/in/yash-zolekar-8b9366270

📧 **Email:**  
yashzolekar2003@gmail.com

⭐ Thanks for Visiting!

If you found this project useful, please consider giving the repository a ⭐ star.

<p align="center"> 🚀 <b>Cloud • DevOps • Automation • Docker • Kubernetes • AWS</b> ☁️ </p> ```⭐ Thanks for Visiting!

