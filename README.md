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
        DBSvc -->|Persist| DBPod[MySQL StatefulSet]```
    end


Component,Technology,Version
Backend,Go (Golang),1.18
Database,MySQL,5.7
Proxy,Nginx,Latest
Orchestration,Kubernetes (Minikube),Latest
Containerization,Docker,20.10+
