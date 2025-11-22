# 🚀 3-Tier Blog Application on Kubernetes (Go + MySQL + Nginx)

A production-ready three-tier web application demonstrating containerized microservices architecture. This project deploys a Go backend, a MySQL database, and an Nginx reverse proxy using **Kubernetes (Minikube)**.

## 📋 Table of Contents
- [Overview](#-overview)
- [Architecture](#-architecture)
- [Technologies Used](#%EF%B8%8F-technologies-used)
- [Project Structure](#-project-structure)
- [Prerequisites](#-prerequisites)
- [Kubernetes Deployment](#%EF%B8%8F-kubernetes-deployment)
- [Accessing the Application](#-accessing-the-application)
- [Challenges & Troubleshooting](#-challenges--troubleshooting)

---

## 📖 Overview
This project implements a **3-tier blog API system** consisting of:
* **Backend API (Go):** Serves REST responses with blog post titles.
* **Database (MySQL 5.7):** Persistent storage using StatefulSet.
* **Reverse Proxy (Nginx):** Exposes the API over HTTPS with SSL/TLS termination.

---

## 🏗 Architecture

```mermaid
graph TD
    Client[Client Browser] -->|HTTPS :30443| NodePort
    subgraph Kubernetes Cluster
        NodePort[Nginx Service NodePort] -->|Route| NginxPod
        NginxPod[Nginx Proxy Pod] -->|HTTP :8000| BackendSvc[Backend Service]
        BackendSvc -->|Internal| BackendPod[Go Backend Pod]
        BackendPod -->|TCP :3306| DBSvc[DB Headless Service]
        DBSvc -->|Persist| DBPod[MySQL StatefulSet]
    end
```
---
## 🛠️ Technologies Used
| Component | Technology | Version |
|-----------|------------|---------|
| **Backend** | Go (Golang) | 1.18 |
| **Database** | MySQL | 5.7 |
| **Proxy** | Nginx | Latest |
| **Orchestration** | Kubernetes (Minikube) | Latest |
| **Containerization** | Docker | 20.10+ |

---

## 📂 Project Structure
```project/
├── backend/
│   ├── Dockerfile              # Multi-stage build for Go app
│   ├── main.go                 # Backend API logic
│   └── go.mod                  # Dependencies
│
├── nginx/
│   ├── Dockerfile              # Nginx container with SSL certs
│   ├── nginx.conf              # Proxy configuration
│   └── generate-ssl.sh         # Script for self-signed certs
│
├── K8S/
│   ├── backend_deployment.yaml     # Backend Deployment
│   ├── backend_service.yaml        # ClusterIP Service
│   ├── db_statefulset.yaml         # MySQL StatefulSet
│   ├── db_headless_service.yaml    # Headless Service
│   ├── db-data-pv.yaml             # Persistent Volume
│   ├── db-data-pvc.yaml            # Persistent Volume Claim
│   ├── db-secret.yaml              # Secrets (Encoded)
│   ├── proxy_deployment.yaml       # Nginx Deployment
│   ├── proxy_nodeport.yml          # NodePort Service (30443)
│   └── nginx-certs.yaml            # TLS Secrets
│
└── README.md
```
---
## ✅ Prerequisites
- Docker Engine installed.
- Minikube installed and running.
- kubectl CLI tool.
---
## ☸️ Kubernetes Deployment
Step 1: Clone and Prepare
```bash
git clone <your-repo-url>
cd project
```
Step 2: Build Docker Images in Minikube
We use the Minikube Docker daemon to build images locally so Kubernetes can find them.
```bash
eval $(minikube docker-env)
docker build -t my-go-backend:v1 ./backend
# (Nginx image is built similarly or pulled)
docker build -t my-custom-nginx:v1
```
Step 3: Apply Kubernetes Manifests
We deploy the infrastructure in specific order (Storage -> DB -> Backend -> Proxy).
```bash
# 1. Storage & Secrets
kubectl apply -f K8S/db-secret.yaml
kubectl apply -f K8S/db-data-pv.yaml
kubectl apply -f K8S/db-data-pvc.yaml

# 2. Database
kubectl apply -f K8S/db_headless_service.yaml
kubectl apply -f K8S/db_statefulset.yaml

# 3. Backend App
kubectl apply -f K8S/backend_service.yaml
kubectl apply -f K8S/backend_deployment.yaml

# 4. Nginx Proxy
kubectl apply -f K8S/proxy_deployment.yaml
kubectl apply -f K8S/proxy_nodeport.yml

Step 4: Verify Deployment
Check if all pods are running:
```bash
kubectl get all
```
Running Status: 
<img width="913" height="642" alt="image" src="https://github.com/user-attachments/assets/3c03b222-7ceb-4b36-a74b-4f7ee9705bcb" />

---
## 🌐 Accessing the Application
1. Via Nginx Proxy (HTTPS)
To access the application from the browser via the secure proxy:
```bash
# Get Minikube IP
minikube ip
# Browse to: https://<MINIKUBE-IP>:30443
```
Browser Result:
![WhatsApp Image 2025-11-20 at 23 55 52_e8bccdf1](https://github.com/user-attachments/assets/73468fff-f1ef-4821-8e2d-f6c32ee6fe44)

2. Direct Backend Access (Port Forwarding)
For debugging purposes, we can bypass the proxy and access the backend directly:
```bash
kubectl port-forward --address 0.0.0.0 svc/backend 9080:8000
# Then curl http://localhost:9080
```
Direct Access Result:
![WhatsApp Image 2025-11-21 at 01 04 46_7bf96e45](https://github.com/user-attachments/assets/4243fab9-2ac0-4420-91be-53fcbffe11f3)

---
## 🔧 Challenges & Troubleshooting
During the development, I encountered and solved several critical issues:

- ErrImageNeverPull:

⚠️ Issue: Kubernetes couldn't find the local Docker images.

✅ Fix: Used eval $(minikube docker-env) to build images inside Minikube's context and set imagePullPolicy: Never.

- Read-only file system (CrashLoopBackOff):

⚠️ Issue: The backend container failed to start because Kubernetes tried to mount the ServiceAccount token into a read-only secret volume.

✅ Fix: Added automountServiceAccountToken: false to the Backend Deployment manifest.

- Port Forwarding Connection Refused:

⚠️ Issue: Port forwarding was bound to 127.0.0.1 inside the VM, making it inaccessible from Windows.

✅ Fix: Used --address 0.0.0.0 to allow external access to the tunnel.

---
---------
