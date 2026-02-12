# Kubernetes Quick Start Guide

Get started with Kubernetes in 30 minutes! This guide will help you set up a cluster and deploy your first application.

## Prerequisites

- Computer with at least 4GB RAM
- Internet connection
- Basic command-line skills

## Step 1: Install Tools (5 minutes)

### Install Docker Desktop (Easiest Option)

**macOS/Windows**:
1. Download [Docker Desktop](https://www.docker.com/products/docker-desktop)
2. Install and start Docker Desktop
3. Go to Settings → Kubernetes
4. Enable Kubernetes
5. Click "Apply & Restart"

**Or install Minikube**:

```bash
# macOS
brew install minikube kubectl

# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start minikube
minikube start
```

### Verify Installation

```bash
kubectl version --client
kubectl get nodes
```

You should see one node in "Ready" status.

## Step 2: Deploy Your First App (10 minutes)

### Create a Deployment

```bash
kubectl create deployment hello-k8s --image=nginx:1.21
```

### Check the Deployment

```bash
kubectl get deployments
kubectl get pods
```

Wait until Pod status is "Running".

### Scale the Application

```bash
kubectl scale deployment hello-k8s --replicas=3
kubectl get pods
```

You should now see 3 Pods running!

### Expose the Application

```bash
kubectl expose deployment hello-k8s --port=80 --type=NodePort
kubectl get services
```

### Access the Application

**With Minikube**:
```bash
minikube service hello-k8s
```

**With Docker Desktop**:
```bash
kubectl port-forward service/hello-k8s 8080:80
```
Then visit http://localhost:8080

## Step 3: Update the Application (5 minutes)

### Rolling Update

```bash
kubectl set image deployment/hello-k8s nginx=nginx:1.22
kubectl rollout status deployment/hello-k8s
```

Watch the Pods being updated:
```bash
kubectl get pods --watch
```

Press Ctrl+C to stop watching.

### Rollback (if needed)

```bash
kubectl rollout undo deployment/hello-k8s
```

## Step 4: Use YAML Files (10 minutes)

### Create a Deployment YAML

Create `my-app.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: my-app
spec:
  selector:
    app: my-app
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

### Deploy the Application

```bash
kubectl apply -f my-app.yaml
```

### Check Resources

```bash
kubectl get deployments
kubectl get services
kubectl get pods
```

### View Details

```bash
kubectl describe deployment my-app
kubectl logs <pod-name>
```

## Common kubectl Commands Cheat Sheet

```bash
# Get resources
kubectl get pods
kubectl get deployments
kubectl get services
kubectl get all

# Describe resources
kubectl describe pod <pod-name>
kubectl describe service <service-name>

# View logs
kubectl logs <pod-name>
kubectl logs -f <pod-name>  # Follow logs

# Execute commands
kubectl exec -it <pod-name> -- /bin/bash

# Delete resources
kubectl delete pod <pod-name>
kubectl delete deployment <deployment-name>
kubectl delete -f my-app.yaml

# Port forwarding
kubectl port-forward <pod-name> 8080:80

# Scale deployments
kubectl scale deployment <name> --replicas=5

# Update deployments
kubectl set image deployment/<name> container=image:tag

# View rollout status
kubectl rollout status deployment/<name>
kubectl rollout history deployment/<name>
kubectl rollout undo deployment/<name>
```

## Clean Up

Remove all resources created:

```bash
kubectl delete deployment hello-k8s
kubectl delete deployment my-app
kubectl delete service hello-k8s
kubectl delete service my-app
```

Or delete by file:
```bash
kubectl delete -f my-app.yaml
```

## What's Next?

Now that you've:
✅ Set up a Kubernetes cluster  
✅ Deployed an application  
✅ Scaled and updated it  
✅ Used YAML manifests

Continue your journey:

1. **Start with the basics**: [Lesson 1: Basic Concepts](lessons/01-basic-concepts/README.md)
2. **Get hands-on**: [Lesson 2: Getting Started](lessons/02-getting-started/README.md)
3. **Try exercises**: [Exercises](EXERCISES.md)
4. **Explore resources**: [Resources](RESOURCES.md)

## Troubleshooting

### Issue: "connection refused" error

**Solution**: Make sure your cluster is running:
```bash
# For Minikube
minikube status

# For Docker Desktop
kubectl cluster-info
```

### Issue: Pod stuck in "Pending"

**Solution**: Check events:
```bash
kubectl describe pod <pod-name>
```

Common causes:
- Insufficient resources
- Image pull errors

### Issue: Can't access Service

**Solution**: Check Service and Pod:
```bash
kubectl get service
kubectl get pods
kubectl describe service <service-name>
```

### Issue: Image pull errors

**Solution**: Use correct image names:
```bash
kubectl set image deployment/<name> container=nginx:1.21
```

## Tips for Success

💡 **Use kubectl aliases**:
```bash
alias k=kubectl
alias kgp='kubectl get pods'
alias kgs='kubectl get services'
```

💡 **Use tab completion**:
```bash
source <(kubectl completion bash)
```

💡 **Use kubectl explain**:
```bash
kubectl explain pod
kubectl explain deployment.spec
```

💡 **Dry run for learning**:
```bash
kubectl create deployment test --image=nginx --dry-run=client -o yaml
```

## Next Steps

Follow the complete learning path:

🟢 **Beginner**: Lessons 1-2 (Foundation)  
🟡 **Intermediate**: Lessons 3-5 (Core Skills)  
🟠 **Advanced**: Lessons 6-8 (Production Ready)  
🔴 **Expert**: Lessons 9-10 (Mastery)

**Happy Kubernetes Journey! 🚀**
