# Troubleshooting Guide

This document summarizes the major issues encountered during the implementation of the CloudForge Retail Platform and the solutions used to resolve them.

Rather than hiding failures, this guide documents the debugging process that led to a stable and production-inspired deployment. These scenarios reflect common issues encountered when working with Kubernetes, Amazon EKS, GitOps, and cloud-native applications.

---

# Overview

During the implementation of CloudForge, several deployment and infrastructure issues were encountered across different layers of the platform.

These included:

* Amazon RDS connectivity
* Kubernetes Pod failures
* Container configuration issues
* AWS Load Balancer configuration
* ArgoCD synchronization
* Helm deployment
* Monitoring stack configuration
* Grafana access
* Horizontal Pod Autoscaler
* Kubernetes scheduling

Understanding these issues significantly improved the overall reliability of the deployment.

---

# Issue 1 - Database Connectivity Failure

## Problem

The application failed to connect to the Amazon RDS PostgreSQL instance.

The Pods either failed during startup or continuously restarted because the database connection could not be established.

---

## Root Cause

The RDS instance was not accepting connections due to networking and connection configuration issues.

Possible causes included:

* Incorrect security group rules
* Invalid database connection string
* SSL configuration mismatch

---

## Solution

The following changes were made:

* Verified RDS endpoint.
* Updated Kubernetes Secret.
* Allowed PostgreSQL traffic on port **5432**.
* Verified database credentials.
* Tested connectivity using `psql`.

---

## Verification

```bash id="we0vng"
kubectl exec -it <pod-name> -- psql
```

Application successfully connected to Amazon RDS.

---

# Issue 2 - Pods Stuck in Pending State

## Problem

Application Pods remained in the **Pending** state.

---

## Error

```text id="m8vkzq"
0/2 nodes are available:
Too many pods.
```

---

## Root Cause

The Kubernetes nodes had reached their maximum supported Pod capacity.

Although CPU and memory were available, no additional Pods could be scheduled.

---

## Solution

Scaled the Amazon EKS node group by adding another worker node.

After the new node joined the cluster, Kubernetes scheduled the pending Pods successfully.

---

## Verification

```bash id="7d4l5g"
kubectl get nodes
```

---

# Issue 3 - CreateContainerConfigError

## Problem

Pods entered the following state:

```text id="0hm23t"
CreateContainerConfigError
```

---

## Root Cause

The Deployment referenced a Kubernetes Secret that did not exist in the cluster.

---

## Solution

Created the required Secret before deploying the application.

```bash id="y6zcl0"
kubectl apply -f secret.yaml
```

Pods started successfully after the Secret became available.

---

# Issue 4 - Application Not Accessible

## Problem

The Pods were running successfully, but the application was not accessible through the Load Balancer.

---

## Investigation

Verified:

* Pods
* Services
* Endpoints
* Load Balancer
* Deployment

---

## Root Cause

The Kubernetes Service and application ports were not correctly aligned.

The application exposed:

```text id="mtuk5v"
5000
```

while traffic was expected through the Kubernetes Service.

---

## Solution

Updated the Service configuration so that the Service targetPort matched the application container port.

Verified the Service endpoints.

```bash id="5s4mx0"
kubectl get endpoints
```

---

# Issue 5 - AWS Load Balancer Not Created

## Problem

Ingress resources were created successfully, but no AWS Application Load Balancer appeared.

---

## Root Cause

The AWS Load Balancer Controller was not installed.

Without the controller, Kubernetes could create Ingress resources, but AWS could not provision an ALB.

---

## Solution

Installed:

* IAM OIDC Provider
* IAM Policy
* IAM Service Account
* AWS Load Balancer Controller

After installation, the ALB was automatically provisioned.

---

## Verification

```bash id="5fjlwm"
kubectl get ingress
```

An AWS ALB hostname was successfully assigned.

---

# Issue 6 - Grafana Access

## Problem

Grafana returned:

```text id="n3vymn"
Page Not Found
```

---

## Root Cause

Ingress routing configuration did not match Grafana's expected base URL.

---

## Temporary Solution

Used Kubernetes Port Forwarding.

```bash id="4t1jfw"
kubectl port-forward svc/monitoring-grafana \
-n monitoring \
3000:80
```

Grafana became accessible through:

```text id="ihs62l"
http://localhost:3000
```

Future enhancement:

Configure Grafana root URL for ALB-based routing.

---

# Issue 7 - ArgoCD Sync Error

## Problem

The monitoring application failed to synchronize.

---

## Error

CRDs exceeded the Kubernetes annotation size limit.

---

## Root Cause

Large Custom Resource Definitions managed through GitOps caused annotation size limitations.

---

## Solution

Managed the monitoring stack using Helm and ArgoCD rather than attempting to patch generated CRDs.

---

# Issue 8 - Horizontal Pod Autoscaler

## Problem

The HPA initially displayed:

```text id="qkg0g9"
cpu: unknown
```

---

## Root Cause

Metrics Server was unavailable.

---

## Solution

Verified that Metrics Server was running successfully.

After deployment, CPU metrics became available and the HPA reported utilization correctly.

---

## Verification

```bash id="l2lvck"
kubectl top nodes

kubectl top pods
```

---

# Issue 9 - Helm Deployment

## Problem

Running:

```bash id="4mimjj"
helm upgrade
```

resulted in:

```text id="b1ynx4"
no deployed releases
```

---

## Root Cause

The monitoring stack was deployed through ArgoCD rather than directly using Helm.

---

## Solution

Allowed ArgoCD to manage the Helm release instead of manually performing upgrades.

---

# Issue 10 - Kubernetes Scheduling

## Problem

The scheduler failed to place Pods.

---

## Root Cause

Node capacity limitations.

---

## Solution

Added an additional worker node.

Verified successful scheduling.

---

# Lessons Learned

This project reinforced several important cloud-native engineering concepts.

* Git should be the single source of truth.
* Kubernetes resources depend on one another.
* Monitoring should be deployed from the beginning.
* Infrastructure debugging is often more important than writing YAML.
* Reading Kubernetes Events is essential during troubleshooting.
* Cloud networking is just as important as Kubernetes configuration.

---

# Useful Debugging Commands

Cluster

```bash id="nukdpk"
kubectl get nodes
```

Pods

```bash id="v6bz8n"
kubectl get pods -A
```

Resources

```bash id="wumg1d"
kubectl get all -A
```

Describe Pod

```bash id="nms8te"
kubectl describe pod <pod-name>
```

Logs

```bash id="iwcqwy"
kubectl logs <pod-name>
```

Ingress

```bash id="9kq56m"
kubectl get ingress
```

Metrics

```bash id="ntr1qb"
kubectl top nodes

kubectl top pods
```

---

# Summary

The successful deployment of CloudForge was achieved through systematic troubleshooting and iterative debugging rather than a single deployment attempt.

Each issue improved understanding of Kubernetes internals, AWS networking, GitOps workflows, and cloud-native application deployment.

These experiences strengthened practical DevOps skills beyond simply deploying a working application.

---
