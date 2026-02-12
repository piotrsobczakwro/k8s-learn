# What is Kubernetes?

## Overview

**Kubernetes** (often abbreviated as **K8s**) is an open-source container orchestration platform originally developed by Google and now maintained by the Cloud Native Computing Foundation (CNCF).

The name "Kubernetes" comes from Greek, meaning "helmsman" or "pilot" - fitting for a system that steers containerized applications.

## The Problem Kubernetes Solves

Before Kubernetes, organizations faced several challenges when running containerized applications:

### Manual Container Management
- Deploying containers manually across multiple servers was time-consuming
- Tracking which containers were running where became complex
- Scaling applications up or down required manual intervention

### Lack of High Availability
- If a container crashed, manual intervention was needed to restart it
- No automated failover mechanisms
- Difficult to maintain 24/7 availability

### Resource Utilization
- Inefficient use of infrastructure resources
- No intelligent scheduling of workloads
- Difficulty balancing load across servers

## What Kubernetes Does

Kubernetes automates the deployment, scaling, and management of containerized applications. It provides:

### Container Orchestration
- **Automated Deployment**: Deploy containers across a cluster of machines
- **Self-Healing**: Automatically restart failed containers
- **Scaling**: Scale applications up or down based on demand
- **Load Balancing**: Distribute traffic across multiple container instances

### Resource Management
- **Efficient Scheduling**: Intelligently place containers on available nodes
- **Resource Optimization**: Manage CPU and memory allocation
- **Multi-tenancy**: Run multiple applications on the same infrastructure

### Application Lifecycle
- **Rolling Updates**: Update applications without downtime
- **Rollbacks**: Revert to previous versions if issues occur
- **Configuration Management**: Manage application configuration separately from code

## Core Philosophy

Kubernetes follows a **declarative** approach:
- You describe the **desired state** of your application
- Kubernetes continuously works to maintain that state
- If something fails, Kubernetes automatically takes corrective action

For example:
```yaml
# You declare: "I want 3 instances of my application running"
replicas: 3
```
Kubernetes ensures exactly 3 instances are always running, restarting them if they crash.

## Where Kubernetes Runs

Kubernetes is platform-agnostic and can run:
- **On-premises**: In your own data centers
- **Cloud providers**: AWS (EKS), Google Cloud (GKE), Azure (AKS)
- **Hybrid environments**: Spanning on-premises and cloud
- **Local development**: Minikube, Kind, Docker Desktop

## Key Takeaways

✅ Kubernetes is a container orchestration platform  
✅ It automates deployment, scaling, and management of containerized applications  
✅ Uses a declarative approach - you define desired state, K8s maintains it  
✅ Solves problems of scale, availability, and resource efficiency  
✅ Platform-agnostic and widely adopted across the industry

---

[← Back to Lesson Overview](README.md) | [Next: Key Components →](02-key-components.md)
