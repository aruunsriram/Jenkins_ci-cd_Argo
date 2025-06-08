
# DevOps Project Pipeline: CI/CD with Spring Boot, Jenkins, SonarQube, ArgoCD, and Kubernetes

This project demonstrates a complete DevOps pipeline for a **Spring Boot Application** using the following tools:

- **Docker** for containerization  
- **Jenkins** for Continuous Integration  
- **SonarQube** for code quality analysis  
- **ArgoCD** for Continuous Deployment  
- **Minikube** for local Kubernetes cluster  
- **AWS EC2** instance for hosting the stack  
- **Kubernetes Manifests** (without Helm) for deployment

---

## 📦 Project Structure

```bash
.
├── spring-boot-app
│   ├── Dockerfile
│   ├── Jenkinsfile
│   ├── spring-boot-app-manifests
│   │   ├── deployment.yaml
│   │   ├── service.yaml
│   └── src/...
└── README.md
```

---

## 🔧 Prerequisites

- Java 11+
- Docker
- Jenkins
- SonarQube
- Minikube
- kubectl
- ArgoCD CLI
- AWS EC2 instance (Ubuntu preferred)

---

## 🚀 Setup Instructions

### 1. **Spin Up EC2 Instance**
```bash
# Choose Ubuntu 22.04 LTS
# Allocate at least t3.large (if using Jenkins + SonarQube)
# Add necessary security groups (SSH, HTTP, HTTPS, and custom ports)
```

---

### 2. **Install Dependencies on EC2**
```bash
# Install Docker
sudo apt update && sudo apt install -y docker.io

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Install kubectl
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl && sudo mv kubectl /usr/local/bin/

# Start Minikube
minikube start --driver=docker
```

---

### 3. **Set Up Jenkins (CI)**
```bash
# Run Jenkins in Docker
docker run -d --name jenkins -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts

# Access Jenkins on http://<EC2-Public-IP>:8080
# Install recommended plugins
# Configure a pipeline using the provided Jenkinsfile
```

---

### 4. **Set Up SonarQube**
```bash
# Run SonarQube in Docker
docker run -d --name sonarqube -p 9000:9000 sonarqube

# Access SonarQube on http://<EC2-Public-IP>:9000
# Configure SonarQube scanner in Jenkins
```

---

### 5. **Build and Push Docker Image**
```bash
# From Spring Boot app root
docker build -t <your-dockerhub-username>/spring-boot-app .

# Push to DockerHub
docker push <your-dockerhub-username>/spring-boot-app
```

---

### 6. **Deploy to Kubernetes (Minikube)**
```bash
# Apply Kubernetes manifests
kubectl apply -f k8s-manifests/deployment.yaml
kubectl apply -f k8s-manifests/service.yaml
```

---

### 7. **Set Up ArgoCD (CD)**
```bash
# Install ArgoCD
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# Access ArgoCD UI (port-forward or NodePort)
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Or expose via NodePort

# Login
argocd login localhost:8080
argocd app create spring-app \
  --repo https://github.com/<your-repo> \
  --path spring-boot-app/k8s-manifests \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

---

## 🌐 Accessing the Application

```bash
# Get service URL
minikube service spring-boot-app

# Or expose via NodePort manually in the service.yaml
```

---

## ✅ Tools Used

| Tool        | Purpose                     |
|-------------|-----------------------------|
| Spring Boot | Web Application             |
| Docker      | Containerization            |
| Jenkins     | CI/CD Pipeline              |
| SonarQube   | Code Quality & Analysis     |
| ArgoCD      | GitOps-based Deployment     |
| Minikube    | Local Kubernetes Cluster    |
| Kubernetes  | Orchestration               |
| AWS EC2     | Hosting environment         |

---

## 📓 Conclusion

This project successfully demonstrates a complete DevOps workflow from code commit to deployment using GitOps, CI/CD, container orchestration, and cloud hosting. It showcases real-world automation with Jenkins, quality checks with SonarQube, and robust delivery using ArgoCD.
