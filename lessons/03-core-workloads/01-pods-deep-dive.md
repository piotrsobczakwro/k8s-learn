# Pods Deep Dive

Understanding Pods thoroughly is essential for mastering Kubernetes.

## Pod Lifecycle States

Pods go through several phases during their lifecycle:

### Phase Details

| Phase | Description | What's Happening |
|-------|-------------|------------------|
| **Pending** | Pod accepted by cluster | Waiting for scheduling, pulling images |
| **Running** | Pod bound to node, containers running | At least one container is running |
| **Succeeded** | All containers completed successfully | Typical for Jobs |
| **Failed** | At least one container terminated with failure | Container exited with non-zero status |
| **Unknown** | Cannot determine Pod state | Usually communication error with node |

## Container States

Each container in a Pod has its own state:

- **Waiting**: Container is pulling image or waiting for something
- **Running**: Container is executing
- **Terminated**: Container finished execution or was terminated

Check container state:
```bash
kubectl get pod <pod-name> -o jsonpath='{.status.containerStatuses[0].state}'
```

## Pod Conditions

Pods have conditions that describe their readiness:

| Condition | Meaning |
|-----------|---------|
| **PodScheduled** | Pod has been scheduled to a node |
| **ContainersReady** | All containers in the Pod are ready |
| **Initialized** | All init containers have completed |
| **Ready** | Pod can serve requests |

View Pod conditions:
```bash
kubectl describe pod <pod-name> | grep -A 10 "Conditions:"
```

## Init Containers

Init containers run before app containers and must complete successfully.

**Use cases**:
- Wait for external services to be ready
- Set up configuration or files
- Clone repositories
- Database migrations

**Example**:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  initContainers:
  - name: install
    image: busybox
    command: ['sh', '-c', 'echo Initializing... && sleep 5']
  containers:
  - name: app
    image: nginx
    ports:
    - containerPort: 80
```

Create and watch:
```bash
kubectl apply -f init-demo.yaml
kubectl get pod init-demo --watch
```

## Resource Requests and Limits

Control how much CPU and memory containers can use:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resource-demo
spec:
  containers:
  - name: app
    image: nginx
    resources:
      requests:        # Minimum guaranteed resources
        cpu: 100m      # 0.1 CPU core
        memory: 128Mi  # 128 megabytes
      limits:          # Maximum allowed resources
        cpu: 500m      # 0.5 CPU core
        memory: 256Mi  # 256 megabytes
```

**Best practices**:
- Always set requests for production Pods
- Set limits to prevent resource exhaustion
- Requests affect scheduling, limits affect runtime behavior

## Restart Policies

Control what happens when containers exit:

```yaml
spec:
  restartPolicy: Always  # Always, OnFailure, or Never
```

- **Always**: Restart containers on any exit (default for Deployments)
- **OnFailure**: Restart only on failure (exit code ≠ 0)
- **Never**: Don't restart (useful for Jobs)

## Liveness and Readiness Probes

### Liveness Probe
Determines if a container is alive. If it fails, kubelet kills the container.

```yaml
livenessProbe:
  httpGet:
    path: /health
    port: 8080
  initialDelaySeconds: 15
  periodSeconds: 10
```

### Readiness Probe
Determines if a container is ready to serve traffic. If it fails, Pod is removed from Service endpoints.

```yaml
readinessProbe:
  httpGet:
    path: /ready
    port: 8080
  initialDelaySeconds: 5
  periodSeconds: 5
```

### Startup Probe
For slow-starting containers. Disables liveness/readiness checks until it succeeds.

```yaml
startupProbe:
  httpGet:
    path: /started
    port: 8080
  failureThreshold: 30
  periodSeconds: 10
```

### Probe Types

**HTTP GET**:
```yaml
httpGet:
  path: /health
  port: 8080
  httpHeaders:
  - name: Custom-Header
    value: Awesome
```

**TCP Socket**:
```yaml
tcpSocket:
  port: 3306
```

**Command Exec**:
```yaml
exec:
  command:
  - cat
  - /tmp/healthy
```

## Complete Pod Example

See `examples/advanced-pod.yaml` for a comprehensive example with:
- Init containers
- Resource limits
- Multiple containers
- Health probes
- Volume mounts

## Pod Quality of Service (QoS)

Kubernetes assigns QoS classes based on requests/limits:

| QoS Class | Condition | Priority |
|-----------|-----------|----------|
| **Guaranteed** | requests = limits for all containers | Highest |
| **Burstable** | Has requests, but requests < limits | Medium |
| **BestEffort** | No requests or limits | Lowest |

Check Pod QoS:
```bash
kubectl get pod <pod-name> -o jsonpath='{.status.qosClass}'
```

## Pod Security Context

Control security settings:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: app
    image: nginx
    securityContext:
      allowPrivilegeEscalation: false
      readOnlyRootFilesystem: true
```

## Best Practices

✅ **Use resource requests and limits**  
✅ **Implement health probes**  
✅ **Use init containers for setup tasks**  
✅ **Run as non-root user**  
✅ **Use read-only root filesystem when possible**  
✅ **Set appropriate restart policies**  
✅ **Label Pods consistently**

## Debugging Pods

**Get Pod events**:
```bash
kubectl describe pod <pod-name>
```

**View logs**:
```bash
kubectl logs <pod-name>
kubectl logs <pod-name> --previous  # Previous instance
kubectl logs <pod-name> -c <container-name>  # Specific container
```

**Execute commands**:
```bash
kubectl exec -it <pod-name> -- /bin/sh
```

**Debug with ephemeral containers** (Kubernetes 1.23+):
```bash
kubectl debug <pod-name> -it --image=busybox
```

## Hands-On Exercise

Create a Pod that:
1. Uses nginx:1.21 image
2. Runs as user ID 1000
3. Has CPU request of 100m and limit of 200m
4. Has memory request of 128Mi and limit of 256Mi
5. Has a liveness probe on port 80, path /
6. Has a readiness probe on port 80, path /

Try it yourself! Solution in `examples/advanced-pod.yaml`.

---

[← Back to Lesson Overview](README.md) | [Next: ReplicaSets →](02-replicasets.md)
