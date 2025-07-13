# Assignment 08 - DevOps: Kubernetes + HELM + Jenkins CI/CD

This project demonstrates the end-to-end deployment of a **MERN (MongoDB, Express.js, React.js, Node.js)** stack application using **Kubernetes**, **HELM**, and **Jenkins** automation on an **Amazon EC2** instance.

## 📁 Repository Structure

``` text
Assignment_08_DevOps/
├── backend/ # Node.js + Express backend code
├── frontend/ # React frontend code
├── helm/learner-report # HELM chart for deployment
├── output-logs # Output logs and post-script
├── wip # Jenkins Files Work In Progress via Local Docker and without driver=docker during minikube start
├── Jenkinsfile # Jenkins Groovy script for full CI/CD automation
└── README.md # Project documentation
```


---

## 🚀 Features

- Dockerized **frontend** and **backend**
- Kubernetes deployment manifests for both services
- HELM chart for flexible and reusable deployments
- Jenkins pipeline to:
  - SSH into EC2
  - Install Docker, Minikube, kubectl, Helm
  - Build Docker images
  - Push images to Docker Hub
  - Generate and deploy HELM charts
- MongoDB Atlas integration using environment variables

---

## 🔧 Technologies Used

- **Kubernetes** (Minikube on EC2)
- **HELM 3**
- **Jenkins (Groovy scripted pipeline)**
- **Docker + Docker Hub**
- **Node.js**, **React.js**
- **MongoDB Atlas**

---

## ⚙️ Setup Overview

### 1. Backend & Frontend Dockerization
- Each service has its own `Dockerfile` for independent container builds.

### 2. Kubernetes Deployment
- Separate YAML manifests for `Deployment`, `Service`, `ConfigMap`, and `Secrets` are templated using HELM.

### 3. HELM Chart
- Located under `/helm-chart`
- Parameterized values using `values.yaml`
- Handles both frontend and backend deployments in a single chart

### 4. Jenkins Pipeline (Jenkinsfile)
- Automates entire deployment flow:

```text
Pipeline
   ↓
Stage: Git Pull
   ↓
Task: Clones the GitHub repository (frontend, backend)
   ↓
Stage: SSH into EC2 & Setup Tools
   ↓
Task: SSH into EC2 instance using Jenkins credentials
   ↓
Task: Install dependencies on EC2:
     - Docker
     - Minikube
     - kubectl
     - Helm
     - curl
     - git
     - apt-transport-https
     - software-properties-common
     - conntrack
     - daemonize
   ↓
Stage: Build & Push Docker Images to Docker Hub
   ↓
Task: Build Docker images:
     - Backend image from backend/Dockerfile
     - Frontend image from frontend/Dockerfile
   ↓
Task: Push Docker images to Docker Hub:
     - Uses credentials stored in Jenkins
   ↓
Stage: Create imagePullSecret in Kubernetes
   ↓
Task: Creating docker-hub-secret for use in Kubernetes
   ↓
Stage: Generate Helm Charts & Deploy
   ↓
Task: Generate HELM chart values dynamically:
     - Injects MongoDB URI, HASH_KEY, JWS_SECRET_KEY, API base URL
   ↓
Task: Deploy application to Minikube using HELM:
     - `helm upgrade --install mern-app ./helm-chart`
   ↓
Stage: Post-Deploy Validation
   ↓
Verify the Deployments and Print in Jenkins Console
   ↓
Wait for the Pods to Run Successfuly and Print the Accessible Endpoint URL
(Note: Jenkins Masks this Value)
   ↓
Task Expose services via NodePort and print access URL with the Public IP Address of EC2 Instance

```

---

## 🔐 Environment Variables (Managed in Jenkins Credentials)

``` text
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
| Variable Name                                | Purpose                                      | Sample Values                                                                                         |
|----------------------------------------------|----------------------------------------------|--------------------------------------------------------------------------------------------------------
| `JL_ASSIGNMENT_EIGHT_MONGO_ATLAS_URI`        | MongoDB connection string                    | mongodb+srv://userid:password@cluster0.aorqndq.mongodb.net/learner_report?retryWrites=true&w=majority |
| `JL_ASSIGNMENT_EIGHT_HASH_KEY`               | JWT hash key for backend auth                | thisIsMyHashKey                                                                                       |
| `JL_ASSIGNMENT_EIGHT_JWS_SECRET_KEY`         | JWS secret key                               | thisIsMyJwtSecretKey                                                                                  |
| `JL_ASSIGNMENT_EIGHT_EC2_IP_ADDRESS`         | EC2 instance IP                              | 65.0.139.200                                                                                          |
| `JL_ASSIGNMENT_EIGHT_DOCKER_REPOSITORY_NAME` | Docker Hub repo                              | https://hub.docker.com/repositories/devopspikachu                                                     |
| `JL_EC2_USER`                                | EC2 User Name                                | ubuntu                                                                                                |
| `JL_WORK_RELATED_EMAIL_ADDRESS`              | Valid Working Email Address for Docker       | test1234@gmail.com                                                                                    |
| `JL_EC2_SSH_PRIVATE_KEY`                     | Username Password Key For SSH Connect        | BEGIN RSA PRIVATE KEY ASD3432....                                                                     |
| `JL_DOCKERHUB_CREDS`                         | Username Password Key For Docker Connect     | Username: devopspikachu and Password: <DOCKER-PAT-TOKEN_PLACEHOLDER>                                  |
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

---
