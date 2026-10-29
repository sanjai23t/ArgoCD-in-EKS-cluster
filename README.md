# ArgoCD-in-EKS-cluster
GitOps-Based Application Deployment Using GitHub, Argo CD, and Amazon EKS
A hands-on DevOps project where I containerized a custom frontend application, deployed it on Amazon EKS, and used Argo CD to manage Kubernetes deployments through GitOps.

📌 Project Overview
The goal of this project was to understand how containerization, Kubernetes orchestration, and GitOps work together in a practical deployment workflow.

I built a custom frontend using HTML and CSS, packaged it into a Docker image using Nginx, published the image to Docker Hub, and deployed it to an Amazon EKS cluster using Kubernetes manifests managed through Argo CD.

🛠️ Tech Stack
Technology	Purpose
AWS EKS	Managed Kubernetes cluster
Docker	Containerization
Docker Hub	Container image registry
Kubernetes	Application orchestration
Argo CD	GitOps continuous delivery
Git & GitHub	Version control and configuration management
Nginx	Static frontend web server
eksctl	EKS cluster management
kubectl	Kubernetes resource management
🏗️ Architecture
                Developer
                    |
                    v
             Custom Frontend
               HTML + CSS
                    |
                    v
               Docker Build
                    |
                    v
                Docker Hub
                    |
                    v
              GitHub Repository
             /                \
     frontend/                k8s/
   Dockerfile              deployment.yaml
   index.html                   |
                                v
                            Argo CD
                                |
                                v
                         Amazon EKS
                                |
                         Kubernetes Deployment
                                |
                         Application Pods
                                |
                                v
                       Kubernetes Service
                                |
                                v
                        Custom Frontend
The Docker image is built and published separately from the Kubernetes manifests. Argo CD monitors the Git repository and reconciles the Kubernetes resources defined in the configured application path.

📁 Repository Structure
argocd-demo/
├── frontend/
│   ├── Dockerfile
│   └── index.html
├── k8s/
│   └── deployment.yaml
└── README.md
⚙️ Implementation Steps
1. Created the Amazon EKS Cluster
I created an EKS cluster using eksctl and configured kubectl to interact with the cluster.

I also explored the relationship between the EKS control plane, worker nodes, and application workloads.

2. Installed Argo CD
I deployed Argo CD in a dedicated Kubernetes namespace:

kubectl create namespace argocd
I then installed Argo CD and accessed its web interface to manage Kubernetes applications.

3. Built a Custom Frontend Image
I created a static frontend application and used Nginx to serve it.

Dockerfile:

FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
I built the Docker image from the repository root:

docker build -t vicky742/argocd-frontend:1.0 ./frontend
I tested the container locally before publishing the image.

4. Published the Image to Docker Hub
After testing the image, I authenticated with Docker Hub and pushed it to my repository:

docker login
docker push vicky742/argocd-frontend:1.0
This made the custom frontend image available for Kubernetes to pull.

5. Created Kubernetes Manifests
I defined the application using Kubernetes manifests.

The Deployment specifies the application image and desired replica count. The Service provides a stable internal endpoint for accessing the application pods.

The project uses:

Deployment: nginx-app
Service: nginx-service
Application label: app=nginx-app
Container image: vicky742/argocd-frontend:1.0
Desired replicas: 5
The actual number of running replicas depends on the cluster's current state and available resources.

6. Connected GitHub to Argo CD
I configured an Argo CD Application to monitor the GitHub repository's k8s directory and deploy the manifests to the cluster's default namespace.

The application configuration uses:

Source repository: My GitHub argocd-demo repository
Manifest path: k8s
Destination: The in-cluster Kubernetes API
Destination namespace: default
Argo CD uses this configuration to compare the desired state stored in Git with the live Kubernetes resources.

7. Updated the Deployment to Use My Custom Image
Initially, I used the standard Nginx image while setting up the Kubernetes deployment.

I then built and published my custom frontend image and updated the Deployment manifest to use:

image: vicky742/argocd-frontend:1.0
After committing and pushing the manifest changes, I verified the image configured in the live Deployment.

🔄 GitOps Workflow
The project demonstrates the following workflow:

Create or update application code.
Build and publish the Docker image.
Update the Kubernetes manifest in Git.
Commit and push changes to GitHub.
Allow Argo CD to detect and synchronize manifest changes.
Verify the resulting Kubernetes deployment.
With automated synchronization and self-healing enabled, Argo CD can reconcile supported live resource changes with the desired configuration stored in Git.

🔍 Verification Commands
Check the Kubernetes Deployment:

kubectl get deployment nginx-app
Check application pods:

kubectl get pods -l app=nginx-app
Check the Service:

kubectl get svc nginx-service
Check the deployed container image:

kubectl get deployment nginx-app \
  -o jsonpath='{.spec.template.spec.containers[0].image}'
Check rollout progress:

kubectl rollout status deployment/nginx-app
Check Argo CD application status:

kubectl get application nginx-app -n argocd
To access the frontend locally through port-forwarding:

kubectl port-forward svc/nginx-service 8080:80
Then open http://localhost:8080 in your browser.

🧩 Challenges and Troubleshooting
During this project, I encountered and worked through several practical issues:

EKS node readiness: Understanding why an active EKS cluster does not necessarily mean application workloads can be scheduled.
Docker image references: Ensuring the correct Docker Hub repository and tag are used.
Git staging: Correcting file paths and staging the frontend/ and k8s/ directories separately.
Git line endings: Understanding that LF-to-CRLF warnings on Windows are generally informational.
Manifest synchronization: Checking the live Deployment image rather than assuming local changes had already been applied.
Command syntax: Correcting a JSONPath typo that prevented the expected image output from appearing.
These troubleshooting exercises helped me understand the importance of verifying each stage instead of assuming the workflow has succeeded.

📚 Key Learnings
Through this project, I gained hands-on exposure to:

Docker image creation, testing, and publishing
Kubernetes Deployments, Pods, and Services
Amazon EKS fundamentals
Git and GitHub-based configuration management
Argo CD application synchronization
GitOps desired-state reconciliation
Kubernetes rollout verification
Troubleshooting deployment and configuration issues
🚧 Future Improvements
I plan to extend the project with:

Version 2.0 image releases
Rolling updates and rollback testing
Deliberate failure scenarios and recovery
Prometheus and Grafana monitoring
Ingress and HTTPS configuration
CI automation for building and testing images
Container image scanning
Improved IAM, secrets, and Kubernetes security
These are planned improvements, not features claimed as completed.

💡 Final Thoughts
This project helped me connect Docker, Kubernetes, AWS EKS, and Argo CD into a practical deployment workflow.

I learned that DevOps is not just about knowing commands. It is about understanding how components interact, verifying system state, and troubleshooting problems when things do not work as expected.

I'm continuing to build my skills through hands-on projects and progressively more realistic deployment scenarios.

Author: Vicky

Focus: DevOps | AWS | Kubernetes | Docker | GitOps

#DevOps #AWS #Kubernetes #Docker #ArgoCD #GitOps
