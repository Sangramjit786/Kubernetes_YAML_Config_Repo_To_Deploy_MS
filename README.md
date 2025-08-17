# Kubernetes YAML Configurations for SpringBoot Banking Services

This repository contains **Kubernetes YAML configuration files** for deploying the [springboot-banking-services](https://github.com/Sangramjit786/springboot-banking-services.git) project into a Kubernetes Cluster.  
It demonstrates how to deploy Spring Boot microservices (ConfigServer, Accounts, Cards, Loans, Message, EurekaServer, GatewayServer, etc.) using native Kubernetes manifests.

---

## 📂 Repository Purpose
- Deploy individual microservices into a Kubernetes cluster using YAML manifest files.  
- Manage environment variables with **ConfigMaps**.  
- Demonstrate **self-healing, rollout/rollback, and service types** in Kubernetes.  
- Highlight the **limitations of manual YAML** and the transition towards **Helm charts** for automation.  

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Kubernetes YAML Configurations](#kubernetes-yaml-configurations)
   - [1. ConfigServer Deployment](#1-configserver-deployment)
   - [2. ConfigMaps for Environment Variables](#2-configmaps-for-environment-variables)
   - [3. Microservices Deployment](#3-microservices-deployment)
4. [Deployment Instructions](#deployment-instructions)
5. [Troubleshooting](#troubleshooting)
6. [Next Steps](#next-steps)

## Project Overview

This project demonstrates the deployment of microservices using Kubernetes YAML manifests. It includes configurations for:

- Config Server
- Eureka Server
- API Gateway
- Account Service
- Cards Service
- Loans Service
- Message Service

## Prerequisites

- Kubernetes cluster (Minikube, EKS, AKS, or GKE)
- `kubectl` configured to communicate with your cluster
- Docker (for building container images)
- Java 17+ (for building microservices)

## 🚀 Deployment Details

### Deploying **ConfigServer Microservice**
- A dedicated Kubernetes YAML config file is created for the **ConfigServer** (`configserver-ms`).  
- This includes:
  - **Deployment.yaml** → Defines Pod replicas and container image.  
  - **Service.yaml** → Exposes ConfigServer to other microservices.  
  - **ConfigMap/Secrets (optional)** → Supplies environment variables and credentials.  

**Deep Dive:**  
- Deploy with `kubectl apply -f configserver-deployment.yaml`.  
- Kubernetes ensures **automatic pod scheduling**, **self-healing** if pods crash, and **scaling** when replicas are increased.  

---

## Kubernetes YAML Configurations

### 1. ConfigServer Deployment

The ConfigServer is a critical component that provides centralized configuration management for all microservices.

**Key Files:**
- `configserver-deployment.yaml`: Defines the ConfigServer deployment
- `configserver-service.yaml`: Configures the service for ConfigServer

**Key Features:**
- Liveness and readiness probes
- Resource limits and requests
- Environment-specific configurations
- Service discovery integration

### 2. ConfigMaps for Environment Variables

ConfigMaps are used to manage environment-specific configurations separately from container images.

**Example ConfigMap (`configmaps.yaml`):**
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: configserver-config
  namespace: banking
data:
  application.properties: |
    server.port=8071
    spring.cloud.config.server.git.uri=file:///config-repo
    spring.profiles.active=default
    eureka.client.serviceUrl.defaultZone=http://eurekaserver:8761/eureka
```

**Usage in Deployment:**

yaml
envFrom:
  - configMapRef:
      name: configserver-config

### 3. Microservices Deployment
Each microservice has its own set of Kubernetes manifests:

Deployment File Structure:
```
kubernetes/
├── accounts/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
├── cards/
│   ├── deployment.yaml
│   ├── service.yaml
│   └── configmap.yaml
└── ...
```
---
## Key Features:

- Zero-downtime deployments
- Horizontal pod autoscaling
- Resource management
- Health checks
- Service discovery

---

## Deployment Instructions
### 1. Create Namespace
```bash
kubectl create namespace banking
```
### 2. Deploy ConfigMaps
```bash
kubectl apply -f kubernetes/configmaps/ -n banking
```
### 3. Deploy ConfigServer
```bash
kubectl apply -f kubernetes/configserver/ -n banking
```
### 4. Deploy Other Microservices
```bash
kubectl apply -f kubernetes/accounts/ -n banking
kubectl apply -f kubernetes/cards/ -n banking
kubectl apply -f kubernetes/loans/ -n banking
```
### 5. Verify Deployments
```bash
kubectl get all -n banking
```

## Troubleshooting
### Common Issues
### 1. Pods not starting
```bash
kubectl describe pod <pod-name> -n banking
kubectl logs <pod-name> -n banking
```
### 2. Service Discovery Issues
- Verify Eureka server is running
- Check service registration in Eureka dashboard
- Verify network policies

### 3. Configuration Problems
- Check ConfigMap values
- Verify environment variables in pods
- Check ConfigServer logs

## Viewing Logs
```bash
# Get pod logs
kubectl logs <pod-name> -n banking

# Stream logs in real-time
kubectl logs -f <pod-name> -n banking

# View logs from previous container instance
kubectl logs -p <pod-name> -n banking
```

