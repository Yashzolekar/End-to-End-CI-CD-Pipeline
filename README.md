🛠️ Technologies Used
Technology	Purpose
☁️ AWS EC2	Jenkins Server
☸️ AWS EKS	Kubernetes Cluster
🔧 Jenkins	CI/CD Automation
🐳 Docker	Containerization
📦 Docker Hub	Container Registry
☸️ Kubernetes	Container Orchestration
🐙 GitHub	Source Code Management
🐧 Linux	Server Operating System
⚙️ kubectl	Kubernetes CLI
🔀 Git	Version Control
☕ Maven	Application Build Tool
📁 Project Structure
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
✅ Prerequisites

Before starting this project, make sure you have:

AWS Account
GitHub Account
Docker Hub Account
Basic Linux knowledge
Basic Git knowledge
Basic Docker knowledge
Basic Kubernetes knowledge
AWS IAM permissions
Java / Maven application
🚀 Setup Guide
Step 1 — Launch Jenkins EC2 Instance

Launch an EC2 instance in AWS.

Recommended:

AMI: Ubuntu
Instance Type: t3.medium
Storage: 20 GB+

Configure the Security Group.

Allow:

SSH     → TCP 22
Jenkins → TCP 8080
HTTP    → TCP 80

For production environments, restrict access to trusted IP addresses.

Connect to the EC2 instance:

ssh -i "your-key.pem" ubuntu@YOUR_EC2_PUBLIC_IP
Step 2 — Update Ubuntu
sudo apt update
sudo apt upgrade -y
Step 3 — Install Java

Jenkins requires Java.

sudo apt install fontconfig openjdk-17-jre -y

Verify:

java -version
Step 4 — Install Jenkins

Add the Jenkins repository:

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | \
  sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

Install Jenkins:

sudo apt update
sudo apt install jenkins -y

Start Jenkins:

sudo systemctl enable jenkins
sudo systemctl start jenkins

Check Jenkins:

sudo systemctl status jenkins
Step 5 — Access Jenkins

Open your browser:

http://YOUR_EC2_PUBLIC_IP:8080

Get the initial Jenkins password:

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

Copy the password and complete the Jenkins setup wizard.

Install the suggested plugins.

Step 6 — Install Git
sudo apt install git -y

Verify:

git --version
Step 7 — Install Docker
sudo apt install docker.io -y

Start Docker:

sudo systemctl enable docker
sudo systemctl start docker

Verify:

docker --version

Allow Jenkins to use Docker:

sudo usermod -aG docker jenkins

Restart Jenkins:

sudo systemctl restart jenkins
Step 8 — Install Maven

If your application uses Maven:

sudo apt install maven -y

Verify:

mvn -version
Step 9 — Install kubectl

Install the Kubernetes CLI:

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

Make it executable:

chmod +x kubectl

Move it:

sudo mv kubectl /usr/local/bin/

Verify:

kubectl version --client
🔐 Step 10 — Configure Docker Hub

Create a Docker Hub repository.

Example:

YOUR_DOCKER_USERNAME/cicd-app

Create a Docker Hub access token.

In Jenkins go to:

Jenkins Dashboard
        ↓
Manage Jenkins
        ↓
Credentials
        ↓
Global
        ↓
Add Credentials

Select:

Kind: Username with password
Username: YOUR_DOCKER_USERNAME
Password: YOUR_DOCKER_TOKEN
ID: dockerhub-credentials

Use a Docker Hub access token instead of your Docker Hub password.

☁️ Step 11 — Create IAM Role for EKS

Create an IAM role for the EKS cluster.

Trusted entity:

AWS Service → EKS

Attach the appropriate EKS cluster policy.

Example:

AmazonEKSClusterPolicy

Role name:

eks-cluster-role
☁️ Step 12 — Create IAM Role for EKS Nodes

Create another IAM role for the worker nodes.

Trusted entity:

AWS Service → EC2

Attach the required policies.

Common policies include:

AmazonEKSWorkerNodePolicy
AmazonEKS_CNI_Policy
AmazonEC2ContainerRegistryPullOnly

Role name:

eks-node-role

Use the current AWS-recommended permissions for your EKS setup.

☸️ Step 13 — Create EKS Cluster

Go to:

AWS Console
    ↓
EKS
    ↓
Create Cluster

Configure:

Cluster Name:
cicd-eks-cluster

Cluster IAM Role:
eks-cluster-role

Networking:
Select your VPC and subnets

Select a currently supported Kubernetes version.

Create the cluster.

Wait until:

Status: ACTIVE
Step 14 — Create EKS Node Group

Go to:

EKS
 ↓
cicd-eks-cluster
 ↓
Compute
 ↓
Add Node Group

Example:

Node Group Name:
eks-node-group

Node IAM Role:
eks-node-role

Instance Type:
t3.medium

Configure the desired capacity based on your requirements and AWS budget.

Create the node group.

Wait until the nodes become ready.

Step 15 — Configure kubectl for EKS

Configure AWS CLI if required.

Then run:

aws eks update-kubeconfig \
  --region YOUR_AWS_REGION \
  --name cicd-eks-cluster

Example:

aws eks update-kubeconfig \
  --region ap-southeast-2 \
  --name cicd-eks-cluster

Check the cluster:

kubectl get nodes

Expected:

NAME              STATUS   ROLES
ip-xxx-xxx-xxx    Ready    <none>
ip-xxx-xxx-xxx    Ready    <none>
🐳 Step 16 — Create Dockerfile

Create a file named:

Dockerfile

Example:

FROM eclipse-temurin:17-jdk

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
☸️ Step 17 — Create Kubernetes Deployment

Create:

k8s/deployment.yaml
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

Replace:

YOUR_DOCKER_USERNAME

with your Docker Hub username.

🌐 Step 18 — Create Kubernetes Service

Create:

k8s/service.yaml
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
🔧 Step 19 — Create Jenkinsfile

Create a file named:

Jenkinsfile
pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "YOUR_DOCKER_USERNAME/cicd-app"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPOSITORY.git'
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

Replace:

YOUR_DOCKER_USERNAME
YOUR_GITHUB_USERNAME
YOUR_REPOSITORY

with your actual values.

🔗 Step 20 — Connect GitHub with Jenkins

Open Jenkins:

http://YOUR_EC2_PUBLIC_IP:8080

Create:

New Item
    ↓
Pipeline

Under Pipeline configuration:

Definition:
Pipeline script from SCM

SCM:
Git

Repository URL:
https://github.com/YOUR_GITHUB_USERNAME/YOUR_REPOSITORY.git

Branch:
*/main

Script Path:
Jenkinsfile

Save the pipeline.

🚀 Step 21 — Run CI/CD Pipeline

Click:

Build Now

Pipeline flow:

GitHub
   ↓
Checkout
   ↓
Build
   ↓
Docker Build
   ↓
Docker Push
   ↓
Kubernetes Deploy
   ↓
AWS EKS
🔍 Step 22 — Verify Deployment

Check Pods:

kubectl get pods

Check Deployments:

kubectl get deployments

Check Services:

kubectl get svc

Example:

NAME               TYPE           EXTERNAL-IP
cicd-app-service   LoadBalancer   xxx.amazonaws.com

Copy the LoadBalancer address and open it in your browser:

http://EXTERNAL-LOAD-BALANCER

🎉 Your application is deployed!

🔄 CI/CD Workflow
             Developer
                  |
                  ↓
              GitHub Push
                  |
                  ↓
               Jenkins
                  |
        +---------+---------+
        |                   |
        ↓                   ↓
      Build             Docker Build
        |                   |
        ↓                   ↓
      Test              Docker Image
                            |
                            ↓
                       Docker Hub
                            |
                            ↓
                         AWS EKS
                            |
                   +--------+--------+
                   |                 |
                   ↓                 ↓
              Deployment          Service
                   |                 |
                   +--------+--------+
                            |
                            ↓
                       Application
📊 Useful Commands
Jenkins
sudo systemctl status jenkins
Docker
docker ps
docker images
Kubernetes Nodes
kubectl get nodes
Kubernetes Pods
kubectl get pods
Kubernetes Deployment
kubectl get deployments
Kubernetes Service
kubectl get svc
Pod Logs
kubectl logs POD_NAME
🧹 Cleanup

Delete Kubernetes resources:

kubectl delete -f k8s/

Delete the EKS cluster from:

AWS Console
    ↓
EKS
    ↓
Clusters
    ↓
cicd-eks-cluster
    ↓
Delete

Also remove unused AWS resources:

EC2
EKS Node Groups
EKS Cluster
Load Balancers
Elastic IPs
Unused Storage

⚠️ Check your AWS account after completing the project to avoid unexpected charges.

🎯 Project Outcomes

Through this project I practiced:

✅ Git & GitHub
✅ Jenkins CI/CD
✅ Docker
✅ Docker Hub
✅ Kubernetes
✅ Amazon EKS
✅ AWS EC2
✅ AWS IAM
✅ Linux
✅ Maven
✅ CI/CD Automation
✅ Containerization
✅ Kubernetes Deployment
✅ Cloud Deployment
🌐 Portfolio

🚀 Portfolio:
https://yashz-portfolio.vercel.app/

🤝 Connect With Me

💼 LinkedIn:
https://www.linkedin.com/in/yash-zolekar-8b9366270

📧 Email:
yashzolekar2003@gmail.com

🌐 Portfolio:
https://yashz-portfolio.vercel.app/

💬 DevOps Quote

🚀 "Build it. Automate it. Deploy it. Monitor it. Improve it."

⭐ Thanks for Visiting!

If you found this project useful, please consider giving the repository a ⭐ star.

<p align="center"> 🚀 <b>Cloud • DevOps • Automation • Docker • Kubernetes • AWS</b> ☁️ </p> ```



