# QuakeWatch DevOps Platform

End-to-end DevOps project for the **QuakeWatch Flask application**, covering Docker containerization, Kubernetes deployment, Helm packaging, GitHub Actions CI/CD, Docker Hub publishing, and ArgoCD GitOps deployment.

This project is part of a multi-phase DevOps course project and will later be extended with AWS and Terraform.

---

## Project Overview

QuakeWatch is a Flask-based web application that displays earthquake-related data.

This repository contains:

* Application source code
* Dockerfile and Docker Compose configuration
* Kubernetes manifests
* Helm chart
* Published Helm chart artifact on Docker Hub
* GitHub Actions CI/CD workflows
* ArgoCD GitOps Application manifest

---

## Repository Structure

```text
quakewatch-devops-platform/
├── QuakeWatch/
│   ├── app.py
│   ├── dashboard.py
│   ├── utils.py
│   ├── requirements.txt
│   ├── Dockerfile
│   ├── docker-compose.yml
│   ├── templates/
│   └── static/
│
├── k8s/
│   ├── quakewatch-deployment.yaml
│   ├── quakewatch-service.yaml
│   ├── quakewatch-configmap.yaml
│   ├── quakewatch-secret.yaml
│   ├── quakewatch-cronjob.yaml
│   └── quakewatch-hpa.yaml
│
├── helm/
│   └── myquakewatch-chart/
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── .helmignore
│       └── templates/
│           ├── NOTES.txt
│           ├── _helpers.tpl
│           ├── quakewatch-deployment.yaml
│           ├── quakewatch-service.yaml
│           ├── quakewatch-configmap.yaml
│           ├── quakewatch-secret.yaml
│           ├── quakewatch-cronjob.yaml
│           └── quakewatch-hpa.yaml
│
├── argocd/
│   └── quakewatch-application.yaml
│
├── .github/
│   └── workflows/
│       ├── ci.yaml
│       └── cd.yaml
│
├── .gitignore
└── README.md
```

---

## Application

The application source code is located in:

```text
QuakeWatch/
```

Main files:

```text
app.py
dashboard.py
utils.py
requirements.txt
Dockerfile
docker-compose.yml
```

---

## Docker

The application is containerized using Docker.

### Docker Image

Docker Hub image:

```text
rawiassy97/quakewatch
```

Available tags include:

```text
rawiassy97/quakewatch:latest
rawiassy97/quakewatch:v1
rawiassy97/quakewatch:v0.<github_run_number>
```

### Build Locally

From the repository root:

```bash
docker build -t quakewatch:local ./QuakeWatch
```

### Run Locally

```bash
docker run -p 5000:5000 quakewatch:local
```

Then open:

```text
http://localhost:5000
```

### Docker Compose

```bash
cd QuakeWatch
docker compose up
```

---

## Kubernetes

Raw Kubernetes manifests are located in:

```text
k8s/
```

The Kubernetes deployment includes:

* Deployment
* Service
* ConfigMap
* Secret
* CronJob
* Horizontal Pod Autoscaler
* Liveness and readiness probes

### Apply Kubernetes Manifests

```bash
kubectl apply -f k8s/
```

### Check Resources

```bash
kubectl get pods
kubectl get svc
kubectl get deployment
kubectl get hpa
kubectl get cronjob
```

### Access the Application

```bash
kubectl port-forward service/quakewatch-service 8080:80
```

Then open:

```text
http://localhost:8080
```

---

## Helm

The Helm chart is located in:

```text
helm/myquakewatch-chart/
```

The chart templates the Kubernetes resources and uses `values.yaml` to configure:

* Replica count
* Docker image repository and tag
* Service type and ports
* ConfigMap values
* Secret values
* Resource requests and limits
* Liveness and readiness probes
* HPA settings
* CronJob settings

### Render Helm Chart

```bash
helm template myquakewatch ./helm/myquakewatch-chart
```

### Install Helm Chart Locally

```bash
helm install myquakewatch ./helm/myquakewatch-chart
```

### Upgrade Helm Release

```bash
helm upgrade myquakewatch ./helm/myquakewatch-chart
```

### Uninstall Helm Release

```bash
helm uninstall myquakewatch
```

---

## Published Helm Chart Artifact

The Helm chart was packaged and published to Docker Hub as an OCI artifact.

Artifact repository:

```text
oci://registry-1.docker.io/rawiassy97/myquakewatch
```

Chart version:

```text
0.1.0
```

Docker Hub URL:

```text
https://hub.docker.com/repository/docker/rawiassy97/myquakewatch/general
```

### Pull Helm Chart

```bash
helm pull oci://registry-1.docker.io/rawiassy97/myquakewatch --version 0.1.0
```

### Install Published Helm Chart

```bash
helm install myquakewatch oci://registry-1.docker.io/rawiassy97/myquakewatch --version 0.1.0
```

---

## GitHub Actions CI/CD

GitHub Actions workflows are located in:

```text
.github/workflows/
```

### CI Workflow

Workflow file:

```text
.github/workflows/ci.yaml
```

The CI workflow runs on every push to non-main branches.

Trigger:

```text
push to any branch except main/master
```

CI steps:

1. Checkout repository
2. Set up Python
3. Install Python dependencies
4. Install pylint
5. Run pylint on Python files
6. Build Docker image locally

The CI workflow verifies that the Python code can be linted and that the Docker image can be built successfully.

The Docker image created during CI is temporary and exists only inside the GitHub Actions runner. It is not pushed to Docker Hub.

---

### CD Workflow

Workflow file:

```text
.github/workflows/cd.yaml
```

The CD workflow runs when a Pull Request targeting `main` is merged.

CD steps:

1. Checkout repository
2. Log in to Docker Hub
3. Build Docker image
4. Push Docker image to Docker Hub

Docker image tags pushed by CD:

```text
rawiassy97/quakewatch:latest
rawiassy97/quakewatch:v0.<github_run_number>
```

Required GitHub repository secrets:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

`DOCKERHUB_TOKEN` is a Docker Hub access token with Read/Write permissions.

---

## Branching Strategy

The repository uses `main` as the stable branch.

Development work is done on feature branches.

Example branch names:

```text
feature/add-ci
feature/add-cd
feature/update-helm-chart
feature/gitops-test
```

Changes are merged into `main` using Pull Requests.

### Branching Flow

```text
feature branch
      ↓
push changes
      ↓
CI workflow runs
      ↓
open Pull Request to main
      ↓
review and merge
      ↓
CD workflow runs
      ↓
Docker image is pushed to Docker Hub
```

### Rules

* `main` is the stable branch.
* Work should be done on feature branches.
* CI runs on pushes to non-main branches.
* CD runs only after a Pull Request is merged into `main`.
* Docker images are pushed to Docker Hub only by the CD workflow.

---

## GitOps with ArgoCD

ArgoCD is used to deploy the Helm chart from GitHub to Kubernetes.

The ArgoCD Application manifest is located in:

```text
argocd/quakewatch-application.yaml
```

The ArgoCD Application points to:

```text
Repository: quakewatch-devops-platform
Branch: main
Path: helm/myquakewatch-chart
Destination namespace: default
```

ArgoCD watches the Helm chart in Git. When the chart changes in the repository, ArgoCD detects the change and syncs the Kubernetes resources.

### Apply ArgoCD Application

```bash
kubectl apply -f argocd/quakewatch-application.yaml
```

### Check ArgoCD Application

```bash
kubectl get applications -n argocd
```

### Access ArgoCD UI

```bash
kubectl port-forward svc/argocd-server -n argocd 8081:443
```

Then open:

```text
https://localhost:8081
```

### GitOps Test

A simple GitOps test can be done by changing the replica count in:

```text
helm/myquakewatch-chart/values.yaml
```

Example:

```yaml
replicaCount: 2
```

After committing and pushing the change to `main`, ArgoCD detects the change and updates the Kubernetes Deployment.

Check the result:

```bash
kubectl get deployment quakewatch-deployment
kubectl get pods
```

---

## Verification Commands

### Check Docker Image

```bash
docker pull rawiassy97/quakewatch:latest
```

### Check Helm Chart Artifact

```bash
helm pull oci://registry-1.docker.io/rawiassy97/myquakewatch --version 0.1.0
```

### Check Kubernetes Resources

```bash
kubectl get all
kubectl get hpa
kubectl get cronjob
kubectl get configmap
kubectl get secret
```

### Check ArgoCD Application

```bash
kubectl get applications -n argocd
```

---

## Deliverables

### Helm Chart Published to Artifact Repository

```text
Artifact:
oci://registry-1.docker.io/rawiassy97/myquakewatch

Version:
0.1.0

Pull command:
helm pull oci://registry-1.docker.io/rawiassy97/myquakewatch --version 0.1.0
```

### Git Repository

This repository contains:

* QuakeWatch application code
* Dockerfile and Docker Compose file
* Kubernetes manifests
* Helm chart
* ArgoCD Application manifest
* GitHub Actions CI/CD workflows
* Documented branching strategy and workflow behavior

### Working CI/CD Pipeline

CI:

```text
Runs on pushes to non-main branches.
Runs pylint and builds the Docker image.
```

CD:

```text
Runs after a Pull Request is merged into main.
Builds and pushes the Docker image to Docker Hub.
```

---

## Future Work

Planned future additions:

* Terraform infrastructure provisioning
* AWS deployment
* Cloud-based Kubernetes deployment
* Improved environment separation for development, staging, and production
