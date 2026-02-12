# Your First Pod

Now it's time to create your first Pod in Kubernetes! This is where theory meets practice.

## What We'll Create

We'll create a simple Pod running an nginx web server, then interact with it using various kubectl commands.

## Method 1: Imperative (Quick)

The fastest way to create a Pod:

```bash
kubectl run my-nginx --image=nginx:1.21
```

Check if it's running:
```bash
kubectl get pods
```

You should see output like:
```
NAME       READY   STATUS    RESTARTS   AGE
my-nginx   1/1     Running   0          10s
```

## Method 2: Declarative (Recommended)

In production, you'll use YAML files. Create a file called `first-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-first-pod
  labels:
    app: nginx
    environment: learning
spec:
  containers:
  - name: nginx-container
    image: nginx:1.21
    ports:
    - containerPort: 80
```

Create the Pod:
```bash
kubectl apply -f first-pod.yaml
```

## Understanding the YAML

Let's break down each section:

```yaml
apiVersion: v1              # API version for this resource type
kind: Pod                   # Type of Kubernetes object
metadata:                   # Information about the Pod
  name: my-first-pod       # Pod name (must be unique)
  labels:                  # Key-value pairs for organization
    app: nginx
    environment: learning
spec:                      # Specification of the Pod
  containers:              # List of containers in this Pod
  - name: nginx-container  # Container name
    image: nginx:1.21      # Docker image to use
    ports:                 # Ports to expose
    - containerPort: 80    # Container listens on port 80
```

## Inspecting Your Pod

### Get basic information
```bash
kubectl get pod my-first-pod
```

### Get detailed information
```bash
kubectl describe pod my-first-pod
```

This shows:
- Pod status and IP address
- Container information
- Events (creation, scheduling, pulling image, etc.)

### Get Pod in YAML format
```bash
kubectl get pod my-first-pod -o yaml
```

### View Pod logs
```bash
kubectl logs my-first-pod
```

### Watch Pod in real-time
```bash
kubectl get pod my-first-pod --watch
```

## Interacting with Your Pod

### Execute a command
```bash
kubectl exec my-first-pod -- ls /usr/share/nginx/html
```

### Interactive shell
```bash
kubectl exec -it my-first-pod -- /bin/bash
```

Once inside, try:
```bash
# Check nginx is running
ps aux | grep nginx

# View nginx config
cat /etc/nginx/nginx.conf

# Exit
exit
```

### Access the web server

Forward Pod port to your local machine:
```bash
kubectl port-forward my-first-pod 8080:80
```

Now open a browser and go to `http://localhost:8080` - you should see the nginx welcome page!

Press Ctrl+C to stop port forwarding.

## Pod Lifecycle

Understanding Pod states:

| Phase | Description |
|-------|-------------|
| Pending | Pod accepted but container(s) not created yet |
| Running | Pod bound to a node and at least one container running |
| Succeeded | All containers completed successfully |
| Failed | At least one container failed |
| Unknown | Pod state cannot be determined |

Check your Pod's status:
```bash
kubectl get pod my-first-pod -o jsonpath='{.status.phase}'
```

## Common Pod Operations

### View all Pods with labels
```bash
kubectl get pods --show-labels
```

### Filter by label
```bash
kubectl get pods -l app=nginx
kubectl get pods -l environment=learning
```

### Get Pod IP
```bash
kubectl get pod my-first-pod -o wide
```

### Get more detailed status
```bash
kubectl get pod my-first-pod -o custom-columns=NAME:.metadata.name,STATUS:.status.phase,IP:.status.podIP
```

## Practice: Create a Multi-Container Pod

Create `multi-container-pod.yaml`:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx
    image: nginx:1.21
    ports:
    - containerPort: 80
  - name: sidecar
    image: busybox
    command: ['sh', '-c', 'while true; do echo "Hello from sidecar"; sleep 10; done']
```

Create and inspect:
```bash
kubectl apply -f multi-container-pod.yaml

# View logs from specific container
kubectl logs multi-container-pod -c nginx
kubectl logs multi-container-pod -c sidecar

# Exec into specific container
kubectl exec -it multi-container-pod -c nginx -- /bin/bash
```

## Troubleshooting Pods

### Pod stuck in Pending
```bash
kubectl describe pod <pod-name>
```
Look for events - common causes:
- Insufficient resources
- Image pull errors
- Node selector issues

### Pod in CrashLoopBackOff
```bash
kubectl logs <pod-name> --previous
```
View logs from crashed container.

### Pod in ImagePullBackOff
```bash
kubectl describe pod <pod-name>
```
Check Events section - usually wrong image name or private registry issues.

## Cleaning Up

Delete Pods:
```bash
kubectl delete pod my-first-pod
kubectl delete pod multi-container-pod
# Or delete using file
kubectl delete -f first-pod.yaml
```

Verify deletion:
```bash
kubectl get pods
```

## Hands-On Exercise

Create a Pod that:
1. Runs a Redis container (image: `redis:7-alpine`)
2. Has the label `app=cache`
3. Exposes port 6379

Try it yourself, then check the solution in `examples/redis-pod.yaml`!

## Key Takeaways

✅ Pods are the smallest deployable units in Kubernetes  
✅ Use `kubectl run` for quick tests, YAML files for production  
✅ A Pod can contain multiple containers  
✅ Containers in a Pod share network and storage  
✅ Use `kubectl describe` to debug Pod issues  
✅ Pods are ephemeral - they can be destroyed and recreated

## What's Next?

While you can create individual Pods, in practice you'll use **Deployments** which manage Pods for you, providing features like:
- Automatic scaling
- Self-healing
- Rolling updates
- Rollbacks

Let's explore Deployments next!

---

[← Previous: kubectl Basics](02-kubectl-basics.md) | [Next: First Deployment →](04-first-deployment.md)
