# 🚀 End-to-End GitOps CI/CD Pipeline using Jenkins, Docker, Kubernetes & Argo CD

![Jenkins](https://img.shields.io/badge/Jenkins-CI-red?logo=jenkins)
![Docker](https://img.shields.io/badge/Docker-Container-blue?logo=docker)
![Kubernetes](https://img.shields.io/badge/Kubernetes-Orchestration-blue?logo=kubernetes)
![ArgoCD](https://img.shields.io/badge/ArgoCD-GitOps-orange)
![SonarQube](https://img.shields.io/badge/SonarQube-Code%20Quality-blue)
![Trivy](https://img.shields.io/badge/Trivy-Security-green)
![Java](https://img.shields.io/badge/Java-21-red)

---

# 📖 Project Overview

This project demonstrates a complete GitOps-based CI/CD pipeline for deploying a Java Tomcat web application on Kubernetes.

The pipeline automates:

- Source Code Management using GitHub
- Continuous Integration using Jenkins
- Maven Build
- SonarQube Code Analysis
- Docker Image Creation
- Trivy Image Security Scan
- Docker Image Push to Docker Hub
- Kubernetes Deployment using Argo CD

---

# 🛠 Technologies Used

| Tool | Purpose |
|------|---------|
| GitHub | Source Code Repository |
| Jenkins | CI/CD Automation |
| Maven | Build Tool |
| Java 21 | Application Runtime |
| Docker | Containerization |
| SonarQube | Code Quality |
| Trivy | Security Scan |
| Docker Hub | Image Repository |
| Kubernetes | Container Orchestration |
| Argo CD | GitOps Continuous Deployment |

---

# 🏗 Architecture

```

Developer

↓

GitHub Repository

↓

GitHub Webhook

↓

Jenkins Pipeline

├── Checkout

├── Maven Build

├── SonarQube Analysis

├── Docker Build

├── Trivy Scan

├── Push Image to Docker Hub

└── Update Kubernetes Manifest

↓

GitHub Manifest Repository

↓

Argo CD

↓

Kubernetes Cluster

↓

Tomcat Application

```

---

# 📂 Project Structure

```

argocd-tomcat-app/

├── Dockerfile

├── Jenkinsfile

├── pom.xml

├── README.md

├── kubernetes/

│ ├── deployment.yaml

│ └── service.yaml

└── src/

```

---

# Prerequisites

- Ubuntu 24.04 / 26.04
- Java 21
- Maven
- Docker
- Jenkins
- SonarQube
- Trivy
- Kubernetes Cluster
- Argo CD
- Git
- Docker Hub Account

---

# Step 1 : Install Java

```bash
sudo apt update
sudo apt install fontconfig openjdk-21-jre -y
java -version
```

---

# Step 2 : Install Jenkins

```bash
wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list

sudo apt update

sudo apt install jenkins -y

sudo systemctl enable jenkins

sudo systemctl start jenkins
```

Unlock Jenkins

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Open

```

http://SERVER-IP:8080

```

Install Suggested Plugins and create the Admin user.

---

# Step 3 : Install Docker

```bash
curl -fsSL https://get.docker.com | sudo sh

sudo usermod -aG docker jenkins

sudo systemctl restart jenkins
```

Verify

```bash
docker version
```

---

# Step 4 : Configure Maven

Manage Jenkins

↓

Tools

↓

Maven

Name

```

maven-3.5.0

```

---

# Step 5 : Install SonarQube

```bash
docker run -d \
--name sonarqube \
-p 9000:9000 \
sonarqube:lts-community
```

Open

```

http://SERVER-IP:9000

```

Username

```

admin

```

Password

```

admin

```

Generate a Sonar Token.

---

# Step 6 : Install Trivy

```bash
sudo apt install wget apt-transport-https gnupg lsb-release -y

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | sudo apt-key add -

echo "deb https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee /etc/apt/sources.list.d/trivy.list

sudo apt update

sudo apt install trivy -y
```

Verify

```bash
trivy --version
```
