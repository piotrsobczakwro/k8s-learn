# Your First Deployment

Deployments are the recommended way to run applications in Kubernetes. Unlike Pods, Deployments provide self-healing, scaling, and update capabilities.

## Why Use Deployments?

While Pods are the basic unit, Deployments provide:
- ✅ **Self-healing**: Automatically replaces failed Pods
- ✅ **Scaling**: Easily scale up/down number of replicas
- ✅ **Rolling updates**: Update with zero downtime
- ✅ **Rollback**: Revert to previous versions
- ✅ **Declarative updates**: Describe desired state, K8s maintains it

## Creating Your First Deployment

### Method 1: Imperative

```bash
kubectl create deployment nginx-deployment --image=nginx:1.21 --replicas=3
```

### Method 2: Declarative (Recommended)

Create `nginx-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3                    # Number of Pod replicas
  selector:
    matchLabels:
      app: nginx                 # Match Pods with this label
  template:                      # Pod template
    metadata:
      labels:
        app: nginx               # Label for Pods
    spec:
      containers:
      - name: nginx
        image: nginx:1.21
        ports:
        - containerPort: 80
```

Apply it:
```bash
kubectl apply -f nginx-deployment.yaml
```

## Understanding the Deployment

Check deployment status:
```bash
kubectl get deployments
```

Output:
```
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
nginx-deployment   3/3     3            3           30s
```

- **READY**: Ready pods / Desired pods
- **UP-TO-DATE**: Pods with latest spec
- **AVAILABLE**: Pods available to users

View created Pods:
```bash
kubectl get pods -l app=nginx
```

You'll see 3 Pods:
```
NAME                                READY   STATUS    RESTARTS   AGE
nginx-deployment-7d64f7b5b9-abc12   1/1     Running   0          1m
nginx-deployment-7d64f7b5b9-def34   1/1     Running   0          1m
nginx-deployment-7d64f7b5b9-ghi56   1/1     Running   0          1m
```

## How Deployments Work

```
Deployment
    ↓
ReplicaSet (manages Pod replicas)
    ↓
Pods (actual containers running)
```

View the ReplicaSet:
```bash
kubectl get replicasets
```

The Deployment manages ReplicaSets, which manage Pods.

## Scaling Your Deployment

### Scale up
```bash
kubectl scale deployment nginx-deployment --replicas=5
```

Watch Pods being created:
```bash
kubectl get pods -l app=nginx --watch
```

### Scale down
```bash
kubectl scale deployment nginx-deployment --replicas=2
```

### Autoscaling

For automatic scaling based on CPU:
```bash
kubectl autoscale deployment nginx-deployment --min=2 --max=10 --cpu-percent=80
```

## Self-Healing in Action

Let's test self-healing. Delete a Pod:

```bash
# Get one Pod name
POD_NAME=$(kubectl get pods -l app=nginx -o jsonpath='{.items[0].metadata.name}')

# Delete it
kubectl delete pod $POD_NAME

# Immediately watch
kubectl get pods -l app=nginx --watch
```

You'll see:
1. Pod enters Terminating state
2. Deployment immediately creates a new Pod
3. New Pod starts and becomes Running
4. Replica count is maintained!

## Exposing Your Deployment

To access your deployment, create a Service:

```bash
kubectl expose deployment nginx-deployment --port=80 --type=NodePort
```

Check the Service:
```bash
kubectl get service nginx-deployment
```

Output:
```
NAME               TYPE       CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
nginx-deployment   NodePort   10.96.100.200   <none>        80:30123/TCP   10s
```

### Access the Service

For Minikube:
```bash
minikube service nginx-deployment
```

For Kind or Docker Desktop:
```bash
kubectl port-forward service/nginx-deployment 8080:80
```

Then visit `http://localhost:8080`

## Updating Your Deployment

### Rolling Update

Update the image version:
```bash
kubectl set image deployment/nginx-deployment nginx=nginx:1.22
```

Watch the rollout:
```bash
kubectl rollout status deployment/nginx-deployment
```

You'll see:
```
Waiting for deployment "nginx-deployment" rollout to finish: 1 out of 3 new replicas have been updated...
Waiting for deployment "nginx-deployment" rollout to finish: 2 out of 3 new replicas have been updated...
deployment "nginx-deployment" successfully rolled out
```

### Rollout Strategy

Kubernetes updates Pods gradually:
1. Creates new Pod with new image
2. Waits for it to be Ready
3. Terminates old Pod
4. Repeats until all Pods updated

This ensures **zero downtime**!

### View Rollout History

```bash
kubectl rollout history deployment/nginx-deployment
```

## Rolling Back

If an update has issues, rollback:

```bash
# Rollback to previous version
kubectl rollout undo deployment/nginx-deployment

# Rollback to specific revision
kubectl rollout undo deployment/nginx-deployment --to-revision=1
```

Check rollback status:
```bash
kubectl rollout status deployment/nginx-deployment
```

## Advanced Deployment Patterns

### Update Deployment YAML

Edit `nginx-deployment.yaml`:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 4                    # Changed from 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.22        # Updated version
        ports:
        - containerPort: 80
        resources:               # Added resource limits
          limits:
            cpu: "500m"
            memory: "256Mi"
          requests:
            cpu: "250m"
            memory: "128Mi"
```

Apply changes:
```bash
kubectl apply -f nginx-deployment.yaml
```

### Pause and Resume Rollout

Useful for making multiple changes:

```bash
# Pause rollout
kubectl rollout pause deployment/nginx-deployment

# Make changes
kubectl set image deployment/nginx-deployment nginx=nginx:1.23
kubectl set resources deployment/nginx-deployment -c nginx --limits=cpu=1

# Resume rollout
kubectl rollout resume deployment/nginx-deployment
```

## Deployment Strategies

### 1. RollingUpdate (Default)

Gradually replaces old Pods with new ones.

```yaml
spec:
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1        # Max Pods above desired count
      maxUnavailable: 1  # Max Pods unavailable during update
```

### 2. Recreate

Terminates all old Pods before creating new ones. Causes downtime.

```yaml
spec:
  strategy:
    type: Recreate
```

## Complete Example with Service

Create `webapp-deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
  labels:
    app: webapp
spec:
  replicas: 3
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: webapp
        image: nginx:1.21
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
---
apiVersion: v1
kind: Service
metadata:
  name: webapp
spec:
  selector:
    app: webapp
  ports:
  - port: 80
    targetPort: 80
  type: LoadBalancer
```

Deploy everything:
```bash
kubectl apply -f webapp-deployment.yaml
```

This creates both the Deployment and Service in one command!

## Monitoring Your Deployment

### Get detailed information
```bash
kubectl describe deployment nginx-deployment
```

### View events
```bash
kubectl get events --field-selector involvedObject.name=nginx-deployment
```

### Check resource usage (requires metrics-server)
```bash
kubectl top pods -l app=nginx
```

## Clean Up

```bash
# Delete deployment and service
kubectl delete deployment nginx-deployment
kubectl delete service nginx-deployment

# Or delete using file
kubectl delete -f nginx-deployment.yaml
```

## Hands-On Exercise

Create a Deployment that:
1. Runs a Redis container (`redis:7-alpine`)
2. Has 2 replicas
3. Uses labels `app=cache` and `tier=backend`
4. Requests 100m CPU and 128Mi memory
5. Limits to 200m CPU and 256Mi memory

Bonus:
- Scale it to 4 replicas
- Update to `redis:7.0.11-alpine`
- Rollback the update

Check solution in `examples/redis-deployment.yaml`!

## Key Takeaways

✅ Deployments manage Pod lifecycle automatically  
✅ Use Deployments, not bare Pods, in production  
✅ Deployments provide self-healing and scaling  
✅ Rolling updates enable zero-downtime deployments  
✅ Easy rollback if updates fail  
✅ Services provide stable access to Pods  
✅ Use `kubectl apply` for declarative management

## What's Next?

You've completed the Getting Started lesson! You can now:
- Set up a Kubernetes cluster
- Use kubectl effectively
- Create and manage Pods
- Deploy applications with Deployments
- Scale and update applications

Next, explore:
- [Lesson 3: Core Workloads](../03-core-workloads/README.md) - Deep dive into workload resources
- [Lesson 4: Configuration and Storage](../04-configuration-storage/README.md) - Manage configuration and data

---

[← Previous: Your First Pod](03-first-pod.md) | [Back to Lesson Overview](README.md)
