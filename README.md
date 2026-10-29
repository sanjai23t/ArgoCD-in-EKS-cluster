# 🚀 GitOps Deployment on AWS EKS Using Docker, Kubernetes & Argo CD

A hands-on DevOps project demonstrating GitOps-based application deployment using GitHub, Docker, Kubernetes, Argo CD, and Amazon EKS.

## 📌 Project Overview

The goal of this project was to understand how containerization, Kubernetes orchestration, and GitOps work together in a real-world deployment workflow.

I created an Amazon EKS cluster, developed a custom frontend application using HTML and CSS, containerized it using Docker and Nginx, and published the Docker image to Docker Hub. I then stored Kubernetes manifests in GitHub and configured Argo CD to synchronize the manifests with my EKS cluster.

Argo CD manages the desired application state defined in Git, enabling consistent deployments and easier configuration management.

## 🛠️ Tech Stack

| Technology | Purpose |
|---|---|
| AWS EKS | Managed Kubernetes cluster |
| Docker | Containerization |
| Docker Hub | Container image registry |
| Kubernetes | Application orchestration |
| Argo CD | GitOps continuous delivery |
| Git & GitHub | Version control and configuration management |
| Nginx | Frontend web server |
| eksctl | EKS cluster management |
| kubectl | Kubernetes resource management |

## 🏗️ Architecture & Working Flow

```text
Developer
    |
    v
Custom Frontend (HTML + CSS)
    |
    v
Docker Build
    |
    v
Docker Hub
    |
    v
GitHub Repository
    |
    v
Argo CD
    |
    v
Amazon EKS
    |
    v
Kubernetes Deployment
    |
    v
Frontend Pods
    |
    v
Kubernetes LoadBalancer Service
    |
    v
Web Application
```

**How it works:**

1. The frontend application is packaged into a Docker image and pushed to Docker Hub.
2. Kubernetes manifests are committed and pushed to GitHub.
3. Argo CD monitors the configured GitHub repository and manifest directory.
4. Argo CD synchronizes the desired configuration with Amazon EKS.
5. Kubernetes creates and maintains the application Pods.
6. The LoadBalancer Service provides external access to the frontend.

## 📁 Repository Structure

```text
ArgoCD-in-EKS-cluster/
├── frontend/
│   ├── Dockerfile
│   └── index.html
├── k8s/
│   ├── deployment.yaml
│   └── service.yaml
└── README.md
```

## ⚙️ Implementation Steps

### 1. Created the Amazon EKS Cluster

Created an EKS cluster in the Hyderabad region using `eksctl`.

```bash
eksctl create cluster \
  --name sanjai-js-k8s \
  --region ap-south-2 \
  --nodes 2 \
  --node-type t3.small
```

Configured local Kubernetes access:

```bash
aws eks update-kubeconfig \
  --region ap-south-2 \
  --name sanjai-js-k8s
```

Verified the cluster using `kubectl get nodes`.

### 2. Created the GitHub Repository

Created the repository `ArgoCD-in-EKS-cluster`, cloned it locally, and organized the project into `frontend/` and `k8s/` directories.

### 3. Containerized the Frontend

Created a custom HTML frontend and a Dockerfile using Nginx to serve the application.

Built the image from the repository root:

```bash
docker build -t sanjai2023/frontend:v1 ./frontend
```

### 4. Published the Image to Docker Hub

Authenticated with Docker Hub and pushed the image:

```bash
docker login
docker push sanjai2023/frontend:v1
```

This made the image available for Kubernetes to pull.

### 5. Created Kubernetes Manifests

Created `deployment.yaml` and `service.yaml` inside the `k8s/` directory.

- **Deployment:** `frontend-deployment`
- **Desired replicas:** 2
- **Container image:** `sanjai2023/frontend:v1`
- **Service:** `frontend-service`
- **Service type:** `LoadBalancer`

The Deployment manages the frontend Pods, while the Service provides a stable endpoint and external access through an AWS load balancer.

### 6. Configured Argo CD

Installed Argo CD in the Kubernetes cluster and configured the Argo CD Application named `frontend-app`.

Application configuration:

- **GitHub repository:** `https://github.com/sanjai23t/ArgoCD-in-EKS-cluster.git`
- **Branch:** `main`
- **Manifest path:** `k8s`
- **Destination:** Amazon EKS cluster
- **Namespace:** `default`

Argo CD monitors the configured repository path and synchronizes the Kubernetes manifests with the live cluster.

### 7. Verified the Deployment

Verified that the Argo CD application reached `Synced` and `Healthy` status and that both frontend replicas were running.

## 🔄 GitOps Workflow

When Kubernetes configuration changes are committed and pushed to GitHub, Argo CD detects the changes and reconciles the cluster with the desired state defined in Git.

For a new frontend version, the workflow is:

1. Update the application code.
2. Build and push a new Docker image tag.
3. Update the image tag in `k8s/deployment.yaml`.
4. Commit and push the changes to GitHub.
5. Allow Argo CD to synchronize the updated manifest.
6. Verify the rollout and application health.

**Note:** Publishing a new Docker image alone does not automatically update the Deployment when the manifest still references the previous tag. The manifest must be updated, or a separate image automation tool must be configured.

## 🔍 Verification Commands

```bash
kubectl get nodes
kubectl get deployments
kubectl get pods -l app=frontend
kubectl get svc frontend-service
kubectl rollout status deployment/frontend-deployment
kubectl get applications -n argocd
```

To inspect the deployed image:

```bash
kubectl get deployment frontend-deployment \
  -o jsonpath='{.spec.template.spec.containers[0].image}'
```

## 📚 Key Learnings

- Creating and managing an Amazon EKS cluster.
- Building and publishing Docker images.
- Deploying applications using Kubernetes Deployments and Services.
- Managing Kubernetes manifests with Git and GitHub.
- Configuring Argo CD for GitOps-based synchronization.
- Understanding desired-state reconciliation and deployment verification.
- Troubleshooting application and cluster configuration issues.

## 🚀 Future Improvements

- Automate Docker image builds using a CI pipeline.
- Configure automated image version updates.
- Add Prometheus and Grafana monitoring.
- Configure Ingress and HTTPS.
- Improve IAM permissions and container security.
- Practice rolling updates and rollback scenarios.

## 💡 Conclusion

This project helped me understand how Docker, Kubernetes, Amazon EKS, GitHub, and Argo CD work together to deliver applications using GitOps principles. It strengthened my practical understanding of containerization, Kubernetes deployment, version control, and automated configuration synchronization.

**Author:** Sanjai M.

**Focus:** DevOps | AWS | Kubernetes | Docker | Argo CD | GitOps

#DevOps #AWS #EKS #Kubernetes #Docker #ArgoCD #GitOps
