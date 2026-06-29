# CloudForge GitOps

Production-style GitOps repository for deploying and managing the **CloudForge Retail Platform** on **Amazon EKS** using **ArgoCD**, **Kubernetes**, **GitHub Actions**, **Helm**, **Prometheus**, and **Grafana**.

---

# Project Overview

CloudForge GitOps is the deployment repository responsible for managing the Kubernetes infrastructure and application deployment for the CloudForge Retail Platform.

The project follows the **GitOps** approach, where Kubernetes manifests, infrastructure configuration, and platform components are version-controlled in Git. ArgoCD continuously monitors this repository and automatically synchronizes changes with the Amazon EKS cluster.

The application source code and deployment manifests are intentionally maintained in separate repositories to reflect real-world DevOps practices.

---

# Project Repositories

## Application Repository

Contains the application source code, Dockerfile, and CI/CD pipeline.

**Repository**

```
https://github.com/Harshitha-Galla5/Cloudforge-retail-platform
```

Responsibilities:

* Application source code
* React frontend
* Node.js backend
* PostgreSQL integration
* Docker image creation
* GitHub Actions CI pipeline
* Docker Hub image publishing

---

## GitOps Repository

Contains the Kubernetes manifests and platform configuration.

**Repository**

```
https://github.com/Harshitha-Galla5/Cloudforge-gitops
```

Responsibilities:

* Kubernetes manifests
* ArgoCD Applications
* Amazon EKS deployment
* Monitoring platform
* Infrastructure configuration
* GitOps synchronization

---

# Project Architecture

> **Architecture Diagram**

![CloudForge Architecture](docs/images/cloudforge-architecture.png)


The CloudForge platform follows a GitOps-driven deployment workflow where application delivery and infrastructure management are separated into dedicated repositories.

The application repository is responsible for building container images, while the GitOps repository manages the desired Kubernetes state consumed by ArgoCD.

---

# GitOps Workflow

> **GitOps Workflow Diagram**

![GitOps Workflow](docs/images/gitops-workflow.png)


---

# Features

* GitOps-based continuous deployment
* Amazon EKS deployment
* Kubernetes resource management
* ArgoCD automatic synchronization
* AWS Application Load Balancer Ingress
* Horizontal Pod Autoscaler (HPA)
* Kubernetes Secrets
* Helm-based monitoring platform
* Prometheus metrics collection
* Grafana dashboards
* Alertmanager integration

---

# Technology Stack

| Category                 | Technologies                          |
| ------------------------ | ------------------------------------- |
| Cloud                    | AWS, Amazon EKS, Amazon RDS, IAM, VPC |
| Containers               | Docker, Docker Hub                    |
| Container Orchestration  | Kubernetes                            |
| GitOps                   | ArgoCD                                |
| Configuration Management | Kustomize, Helm                       |
| Monitoring               | Prometheus, Grafana, Alertmanager     |
| CI/CD                    | GitHub Actions                        |
| Database                 | PostgreSQL                            |

---

# Repository Structure

```text
cloudforge-gitops/
│
├── apps/
│   └── freshmart/
│       ├── deployment.yaml
│       ├── service.yaml
│       ├── ingress.yaml
│       ├── hpa.yaml
│       ├── namespace.yaml
│       ├── secret-template.yaml
│       └── kustomization.yaml
│
├── bootstrap/
│   ├── cluster.yaml
│   └── iam_policy.json
│
├── platform/
│   └── monitoring/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── application.yaml
│
├── docs/ 
│
└── README.md
```

---

# Deployment Components

The platform consists of the following major components.

## Application Layer

* FreshMart Retail Application
* Kubernetes Deployment
* Kubernetes Service
* Kubernetes Ingress
* Horizontal Pod Autoscaler

---

## Platform Layer

* Amazon EKS
* ArgoCD
* AWS Load Balancer Controller
* Kubernetes Namespaces

---

## Monitoring Layer

* Prometheus
* Grafana
* Alertmanager
* Node Exporter
* kube-state-metrics

---

## Database Layer

* Amazon RDS PostgreSQL

---

# Screenshots

## Application


![Application](docs/screenshots/application-homepage.png)


---

## ArgoCD Dashboard


![ArgoCD](docs/screenshots/argocd-dashboard.png)


---

## Grafana Dashboard


![Grafana](docs/screenshots/grafana-dashboard.png)


---

## GitHub Actions


![GitHub Actions](docs/screenshots/github-workflow-success.png)


---

# Documentation

Detailed documentation is available in the **docs** directory.

| Document                   | Description                                     |
| -------------------------- | ----------------------------------------------- |
| 01-Architecture.md         | Overall cloud architecture and design decisions |
| 02-Installation.md         | Complete deployment guide                       |
| 03-GitOps-Workflow.md      | GitOps implementation                           |
| 04-Kubernetes-Resources.md | Kubernetes resource explanations                |
| 05-Monitoring.md           | Monitoring architecture                         |
| 06-CI-CD.md                | Continuous Integration workflow                 |
| 07-Troubleshooting.md      | Issues encountered and resolutions              |
| 08-Future-Improvements.md  | Planned enhancements                            |

---

# Future Enhancements

* Package FreshMart as a reusable Helm chart
* Multi-environment GitOps (Development, Staging, Production)
* AWS Secrets Manager integration
* HTTPS using AWS Certificate Manager (ACM)
* ExternalDNS integration
* Centralized logging with Loki
* Distributed tracing with OpenTelemetry
* Progressive Delivery (Blue/Green and Canary deployments)

---

# Author

**Harshitha Galla**

Aspiring Cloud & DevOps Engineer

**GitHub Profile**

https://github.com/Harshitha-Galla5

---

# License

This project is intended for educational and portfolio purposes.
