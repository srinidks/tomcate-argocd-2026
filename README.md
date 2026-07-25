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
---

# ☸️ Step 7: Install Kubernetes Cluster

This project uses a Kubernetes cluster with:

- 3 Control Plane Nodes
- 2 Worker Nodes
- Container Runtime: containerd
- CNI Plugin: Calico
- Kubernetes Version: v1.33.x

Verify cluster status:

```bash
kubectl get nodes
```

Example output:

```text
NAME          STATUS   ROLES           AGE   VERSION
k8s-master    Ready    control-plane   20d   v1.33.x
k8s-master2   Ready    control-plane   20d   v1.33.x
k8s-master3   Ready    control-plane   20d   v1.33.x
k8s-worker1   Ready    <none>          20d   v1.33.x
k8s-worker2   Ready    <none>          20d   v1.33.x
```

---

# 📦 Step 8: Install Argo CD

Create the Argo CD namespace.

```bash
kubectl create namespace argocd
```

Install Argo CD.

```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Verify the pods.

```bash
kubectl get pods -n argocd
```

Expected output:

```text
NAME                                                READY   STATUS
argocd-application-controller-xxxxx                 1/1     Running
argocd-applicationset-controller-xxxxx              1/1     Running
argocd-dex-server-xxxxx                             1/1     Running
argocd-notifications-controller-xxxxx               1/1     Running
argocd-redis-xxxxx                                  1/1     Running
argocd-repo-server-xxxxx                            1/1     Running
argocd-server-xxxxx                                 1/1     Running
```

---

# 🌐 Step 9: Expose the Argo CD Server

Change the service type to NodePort.

```bash
kubectl patch svc argocd-server \
-n argocd \
-p '{"spec":{"type":"NodePort"}}'
```

Verify the service.

```bash
kubectl get svc -n argocd
```

Example:

```text
NAME              TYPE       CLUSTER-IP      PORT(S)
argocd-server     NodePort   10.96.10.10     80:30007/TCP,443:30008/TCP
```

Open the Argo CD UI.

```
https://NODE-IP:30008
```

---

# 🔑 Step 10: Login to Argo CD

Retrieve the initial admin password.

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d

echo
```

If the initial secret is unavailable (Argo CD v3.x), reset the password.

Generate a bcrypt password hash.

```bash
argocd account bcrypt --password NewPassword@123
```

Update the Argo CD secret.

```bash
kubectl -n argocd patch secret argocd-secret \
-p '{"stringData":{
"admin.password":"<BCRYPT_HASH>",
"admin.passwordMtime":"'$(date -u +%FT%TZ)'"
}}'
```

Restart the Argo CD server.

```bash
kubectl rollout restart deployment argocd-server -n argocd
```

Login with:

```
Username: admin
Password: NewPassword@123
```

---

# 📁 Step 11: Create Kubernetes Namespace

```bash
kubectl create namespace tomcat
```

Verify:

```bash
kubectl get ns
```

---

# 📄 Step 12: Create Deployment Manifest

File:

```
kubernetes/deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: argocd-tomcat-app
  namespace: tomcat

spec:
  replicas: 2

  selector:
    matchLabels:
      app: argocd-tomcat-app

  template:
    metadata:
      labels:
        app: argocd-tomcat-app

    spec:
      containers:
      - name: tomcat
        image: srinidks/argocd-tomcat-app:latest

        ports:
        - containerPort: 8080

        imagePullPolicy: Always
```

---

# 🌐 Step 13: Create Service Manifest

File:

```
kubernetes/service.yaml
```

```yaml
apiVersion: v1
kind: Service

metadata:
  name: tomcat-app-svc
  namespace: tomcat

spec:
  type: NodePort

  selector:
    app: argocd-tomcat-app

  ports:
  - port: 8080
    targetPort: 8080
    nodePort: 30080
```

Apply the manifests.

```bash
kubectl apply -f kubernetes/deployment.yaml

kubectl apply -f kubernetes/service.yaml
```

Verify the deployment.

```bash
kubectl get all -n tomcat
```

---

# 🔗 Step 14: Create Argo CD Application

You can create the application using the Argo CD UI or the CLI.

Using the CLI:

```bash
argocd app create argocd-tomcat-app \
--repo https://github.com/<your-github-username>/<your-manifest-repo>.git \
--path kubernetes \
--dest-server https://kubernetes.default.svc \
--dest-namespace tomcat
```

Sync the application.

```bash
argocd app sync argocd-tomcat-app
```

Verify the application status.

```bash
argocd app get argocd-tomcat-app
```

The application should show:

```text
Sync Status : Synced
Health      : Healthy
```

---

# ✅ Step 15: Verify the Deployment

Check the pods.

```bash
kubectl get pods -n tomcat
```

Check the service.

```bash
kubectl get svc -n tomcat
```

Check the endpoints.

```bash
kubectl get endpoints -n tomcat
```

Expected output:

```text
NAME             ENDPOINTS
tomcat-app-svc   10.244.x.x:8080,10.244.x.x:8080
```

Access the application.

```
http://NODE-IP:30080/argocd-tomcat-app/
```

If using HTTPS ingress instead of a NodePort, use your ingress URL instead.
