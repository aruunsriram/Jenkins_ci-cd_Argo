
# 🚀 Spring Boot App Deployment with ArgoCD on Minikube

## 📦 Project Overview

This project demonstrates how to deploy a Spring Boot application on a Kubernetes cluster using ArgoCD for GitOps-based continuous delivery. It runs locally via Minikube.

---

## ⚙️ Prerequisites

Make sure the following tools are installed:

- [Minikube](https://minikube.sigs.k8s.io/docs/start/)
- [kubectl](https://kubernetes.io/docs/tasks/tools/)
- [Docker](https://docs.docker.com/get-docker/)
- [ArgoCD CLI (optional)](https://argo-cd.readthedocs.io/en/stable/cli_installation/)

---

## 🧱 Minikube Cluster Setup

Start a Minikube cluster with appropriate resources and a working CNI:

```bash
minikube start --cpus=4 --memory=8192 --addons=ingress
```

> 🔥 **Do not specify `--cni` manually unless you know what you're doing.** The default setup is usually enough.

---

## 🐳 Build and Push Docker Image

Build your Spring Boot app Docker image and load it into Minikube:

```bash
eval $(minikube docker-env)
docker build -t spring-boot-app:latest .
```

> This allows you to use local images inside Minikube without pushing to a remote registry.

---

## ☸️ Deploy to Kubernetes

Apply your Kubernetes manifests:

```bash
kubectl apply -f k8s/
```

Make sure the pod is running:

```bash
kubectl get pods
```

---

## 🚀 Install and Access ArgoCD

### Step 1: Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Step 2: Expose ArgoCD Server

Port-forward ArgoCD server for browser access:

```bash
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Access it in your browser:  
👉 https://localhost:8080

---

## 🔐 Login to ArgoCD

Fetch initial admin password:

```bash
kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d
```

Login with:
- **Username**: `admin`
- **Password**: *<output from above command>*

---

## 🌿 Connect and Sync Spring Boot App in ArgoCD

1. Click **"New App"**
2. Fill in:
   - **Application Name**: `spring-boot-app`
   - **Repo URL**: your GitHub repo
   - **Path**: path to Kubernetes manifests
   - **Cluster**: `https://kubernetes.default.svc`
   - **Namespace**: `default`
3. Click **Create**, then **Sync**

---

## ✅ Verify Deployment

Check app is running:

```bash
kubectl get pods
kubectl get svc
```

If service is `ClusterIP`, expose it:

```bash
kubectl port-forward svc/spring-boot-app 8081:8080
```

Then open your browser:  
👉 http://localhost:8081

---

## 📎 Additional Notes

- If you encounter `image can't be pulled` errors:
  - Make sure you're using local image (`eval $(minikube docker-env)` before `docker build`)
  - Or push to Docker Hub and use correct `imagePullPolicy`
