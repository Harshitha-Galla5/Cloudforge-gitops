# INSTALLATION.md

# CloudForge Retail Platform - Installation Guide

This guide explains how to deploy the CloudForge Retail Platform on Amazon EKS using GitOps with ArgoCD.

---

# Prerequisites

Ensure the following tools are installed:

* AWS CLI
* kubectl
* eksctl
* Docker
* Helm
* Git
* GitHub Account
* Docker Hub Account

---

# Clone the Repository

```bash
git clone https://github.com/<YOUR_GITHUB_USERNAME>/cloudforge-gitops.git

cd cloudforge-gitops
```

---

# Create Amazon EKS Cluster

```bash
eksctl create cluster -f bootstrap/cluster.yaml
```

Verify the cluster:

```bash
kubectl get nodes
```

---

# Install AWS Load Balancer Controller

Create the IAM policy:

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://bootstrap/iam_policy.json
```

Associate the IAM OIDC provider:

```bash
eksctl utils associate-iam-oidc-provider \
  --cluster cloudforge-cluster \
  --region ap-south-1 \
  --approve
```

Create the IAM Service Account:

```bash
eksctl create iamserviceaccount \
  --cluster cloudforge-cluster \
  --namespace kube-system \
  --name aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn arn:aws:iam::<ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

Install the AWS Load Balancer Controller using Helm.

Verify:

```bash
kubectl get pods -n kube-system
```

---

# Install ArgoCD

Create the namespace:

```bash
kubectl create namespace argocd
```

Install ArgoCD:

```bash
kubectl apply -n argocd \
-f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Wait until all pods are running:

```bash
kubectl get pods -n argocd
```

Access the UI:

```bash
kubectl port-forward svc/argocd-server \
-n argocd \
8080:443
```

Open:

```
https://localhost:8080
```

Retrieve the initial admin password:

```bash
kubectl -n argocd get secret argocd-initial-admin-secret \
-o jsonpath="{.data.password}" | base64 -d
```

---

# Create Kubernetes Secret

Before deploying the application, create the database secret.

```bash
kubectl create secret generic cloudforge-db-secret \
--from-literal=DATABASE_URL="<YOUR_DATABASE_URL>" \
--from-literal=OPENAI_API_KEY="<YOUR_OPENAI_API_KEY>" \
-n cloudforge
```

---

# Deploy FreshMart

Apply the application manifests:

```bash
kubectl apply -k apps/freshmart
```

Verify:

```bash
kubectl get all -n cloudforge
```

---

# Deploy Monitoring Stack

Create the ArgoCD Application:

```bash
kubectl apply -f platform/monitoring/application.yaml
```

Verify:

```bash
kubectl get applications -n argocd
```

Wait until the Monitoring application becomes:

```
Synced
Healthy
```

---

# Access the Application

Retrieve the Ingress:

```bash
kubectl get ingress -n cloudforge
```

Open the ALB hostname shown in the output.

---

# Access Grafana

Grafana is intended as an internal administration tool.

Use port forwarding:

```bash
kubectl port-forward svc/monitoring-grafana \
-n monitoring \
3000:80
```

Open:

```
http://localhost:3000
```

Default credentials:

```
Username: admin
Password: admin123
```

---

# Verify Horizontal Pod Autoscaler

```bash
kubectl get hpa -n cloudforge
```

View resource usage:

```bash
kubectl top pods -n cloudforge
```

---

# Verify GitOps

Make a code change in the application repository.

GitHub Actions will:

* Build the Docker image
* Push the image to Docker Hub
* Update the GitOps repository

ArgoCD will automatically detect the Git change and synchronize the cluster.

Verify:

```bash
kubectl get applications -n argocd
```

---

# Useful Commands

View Pods

```bash
kubectl get pods -A
```

View Services

```bash
kubectl get svc -A
```

View Ingress

```bash
kubectl get ingress -A
```

Describe Pod

```bash
kubectl describe pod <pod-name> -n cloudforge
```

View Logs

```bash
kubectl logs <pod-name> -n cloudforge
```

---

# Cleanup

Delete the application:

```bash
kubectl delete -k apps/freshmart
```

Delete the monitoring stack:

```bash
kubectl delete -f platform/monitoring/application.yaml
```

Delete the EKS cluster:

```bash
eksctl delete cluster \
--name cloudforge-cluster \
--region ap-south-1
```
