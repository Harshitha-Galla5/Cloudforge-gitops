# System Architecture

This document explains the overall architecture, design decisions, and deployment workflow of the CloudForge Retail Platform. It describes how the application, infrastructure, monitoring stack, and GitOps pipeline work together to provide a production-inspired Kubernetes deployment on Amazon EKS.

---

# Architecture Overview

CloudForge Retail Platform follows a modern cloud-native architecture based on Kubernetes and GitOps principles.

Instead of deploying applications manually, every infrastructure and application change is stored inside Git repositories. ArgoCD continuously monitors the GitOps repository and synchronizes the desired state with the Kubernetes cluster.

The project separates application development from infrastructure management by maintaining two dedicated repositories.

* **Application Repository**

  * Application source code
  * Dockerfile
  * GitHub Actions workflow
  * Docker image creation

* **GitOps Repository**

  * Kubernetes manifests
  * ArgoCD Applications
  * Monitoring stack
  * Infrastructure configuration

This separation closely resembles enterprise DevOps workflows where developers and platform engineers work independently while maintaining a single source of truth through Git.

---

# Overall Architecture

![CloudForge Architecture](images/cloudforge-architecture.png)


The architecture consists of the following major layers:

1. Source Code Management
2. Continuous Integration
3. Container Registry
4. GitOps
5. Kubernetes Platform
6. Monitoring
7. Database

Each layer has a dedicated responsibility, making the system modular, maintainable, and scalable.

---

# Project Objectives

The primary objectives of this project were:

* Deploy a production-style application on Kubernetes.
* Implement GitOps using ArgoCD.
* Automate image creation using GitHub Actions.
* Separate application and infrastructure repositories.
* Deploy workloads on Amazon EKS.
* Expose the application using AWS Application Load Balancer.
* Enable automatic horizontal scaling.
* Monitor cluster health using Prometheus and Grafana.
* Document the complete deployment architecture.

---

# Architecture Components

## 1. Application Repository

The Application Repository contains the business logic of the FreshMart Retail Platform.

Responsibilities include:

* Frontend source code
* Backend source code
* Dockerfile
* GitHub Actions workflow
* Docker image build
* Docker image publishing

Whenever a developer pushes code to the repository, GitHub Actions automatically builds a Docker image and publishes it to Docker Hub.

---

## 2. GitHub Actions

GitHub Actions provides the Continuous Integration (CI) pipeline.

Its responsibilities include:

* Building the Docker image
* Tagging the image
* Pushing the image to Docker Hub
* Updating the GitOps repository with the latest image tag

This removes the need for manual deployment steps and ensures repeatable builds.

---

## 3. Docker Hub

Docker Hub acts as the container image registry.

Instead of building images directly inside Kubernetes, the cluster always pulls immutable container images from Docker Hub.

Benefits include:

* Version-controlled images
* Faster deployments
* Consistent runtime environment
* Easy rollback

---

## 4. GitOps Repository

The GitOps repository stores the desired state of the Kubernetes cluster.

It contains:

* Kubernetes manifests
* ArgoCD Applications
* Monitoring platform
* Infrastructure configuration

Rather than deploying resources manually using kubectl, changes are committed to Git and automatically synchronized by ArgoCD.

This ensures that Git remains the single source of truth.

---

## 5. ArgoCD

ArgoCD continuously watches the GitOps repository.

Whenever changes are committed:

1. ArgoCD detects the change.
2. Compares Git with the cluster.
3. Identifies configuration drift.
4. Synchronizes the cluster automatically.

This provides:

* Automatic deployments
* Rollback capability
* Deployment history
* Cluster state visualization

---

## 6. Amazon EKS

Amazon Elastic Kubernetes Service (EKS) hosts the Kubernetes cluster.

The cluster runs:

* FreshMart Application
* Monitoring Platform
* AWS Load Balancer Controller
* ArgoCD

Using a managed Kubernetes service eliminates the operational complexity of managing Kubernetes control planes.

---

## 7. Kubernetes Resources

The application is deployed using native Kubernetes resources.

The project currently uses:

* Namespace
* Deployment
* Service
* Ingress
* Secret
* Horizontal Pod Autoscaler

These resources work together to provide application deployment, networking, configuration management, and autoscaling.

Detailed explanations are available in **04-Kubernetes-Resources.md**.

---

## 8. AWS Application Load Balancer

The AWS Load Balancer Controller automatically provisions an Application Load Balancer (ALB) whenever an Ingress resource is created.

Responsibilities include:

* Internet-facing endpoint
* HTTP request routing
* Traffic distribution
* Health checks

The ALB forwards requests to the Kubernetes Service, which then distributes traffic across application Pods.

---

## 9. Amazon RDS PostgreSQL

The application stores persistent data inside Amazon RDS PostgreSQL.

Advantages include:

* Managed backups
* High availability options
* Automated maintenance
* Reliable persistent storage

The application connects securely using Kubernetes Secrets.

---

## 10. Monitoring Platform

Monitoring is deployed separately using Helm.

Components include:

* Prometheus
* Grafana
* Alertmanager
* Node Exporter
* kube-state-metrics

These services continuously collect infrastructure and application metrics for operational visibility.

---

# Architecture Summary

CloudForge Retail Platform demonstrates a production-inspired cloud-native deployment by combining Kubernetes, GitOps, CI/CD, monitoring, and AWS managed services into a single automated deployment workflow.

Each component has a clearly defined responsibility, making the overall architecture modular, scalable, and easy to maintain.

The following documents explain each part of the system in greater detail.
