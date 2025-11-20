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

