# Autoscaling in Kubernetes

Learn to automatically scale your applications based on demand.

## Types of Autoscaling

### 1. Horizontal Pod Autoscaler (HPA)
Scales the number of Pod replicas based on metrics.

### 2. Vertical Pod Autoscaler (VPA)
Adjusts CPU and memory requests/limits for containers.

### 3. Cluster Autoscaler
Scales the number of nodes in your cluster.

## Horizontal Pod Autoscaler (HPA)

### Prerequisites

Install metrics-server:
```bash
# For Minikube
minikube addons enable metrics-server

# For other clusters
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Verify:
```bash
kubectl top nodes
kubectl top pods
```

### Basic HPA Example

Create a Deployment with resource requests:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  replicas: 1
  selector:
    matchLabels:
      app: php-apache
  template:
    metadata:
      labels:
        app: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 200m
          limits:
            cpu: 500m
```

Create HPA:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: php-apache
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: php-apache
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
```

Or create imperatively:
```bash
kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=10
```

### Check HPA Status

```bash
kubectl get hpa
kubectl describe hpa php-apache
```

### Test Autoscaling

Generate load:
```bash
kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
```

Watch scaling:
```bash
kubectl get hpa php-apache --watch
```

### Multiple Metrics

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: multi-metric-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
  - type: Pods
    pods:
      metric:
        name: custom_metric
      target:
        type: AverageValue
        averageValue: "1000"
```

## Vertical Pod Autoscaler (VPA)

### Install VPA

```bash
git clone https://github.com/kubernetes/autoscaler.git
cd autoscaler/vertical-pod-autoscaler
./hack/vpa-up.sh
```

### VPA Example

```yaml
apiVersion: autoscaling.k8s.io/v1
kind: VerticalPodAutoscaler
metadata:
  name: my-app-vpa
spec:
  targetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  updatePolicy:
    updateMode: "Auto"  # Auto, Recreate, Initial, or Off
  resourcePolicy:
    containerPolicies:
    - containerName: app
      minAllowed:
        cpu: 100m
        memory: 128Mi
      maxAllowed:
        cpu: 1
        memory: 1Gi
```

## Scaling Behaviors

Control scale-up and scale-down behavior:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: controlled-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 2
  maxReplicas: 10
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300  # Wait 5 min before scaling down
      policies:
      - type: Percent
        value: 50  # Scale down max 50% of current replicas
        periodSeconds: 60
      - type: Pods
        value: 2  # Or max 2 pods
        periodSeconds: 60
      selectPolicy: Min  # Choose the minimum change
    scaleUp:
      stabilizationWindowSeconds: 0  # Scale up immediately
      policies:
      - type: Percent
        value: 100  # Double replicas
        periodSeconds: 15
      - type: Pods
        value: 4  # Or add 4 pods
        periodSeconds: 15
      selectPolicy: Max  # Choose the maximum change
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 60
```

## Custom Metrics

Using custom metrics from Prometheus:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: custom-metric-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-app
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: External
    external:
      metric:
        name: queue_messages_ready
        selector:
          matchLabels:
            queue_name: my_queue
      target:
        type: AverageValue
        averageValue: "30"
```

## Best Practices

✅ **Always set resource requests** - HPA needs them  
✅ **Start conservative** - Use higher target utilization initially  
✅ **Monitor scaling behavior** - Watch for thrashing  
✅ **Set appropriate min/max** - Prevent over/under scaling  
✅ **Use stabilization windows** - Prevent rapid scaling  
✅ **Test with realistic load** - Validate scaling behavior  
✅ **Combine with PodDisruptionBudgets** - Maintain availability

## Common Pitfalls

❌ **No resource requests** - HPA won't work  
❌ **Too aggressive scaling** - Causes instability  
❌ **Conflicting autoscalers** - Don't use HPA and VPA on same resource  
❌ **Ignoring scale-down delay** - May cause cost issues  
❌ **Not monitoring** - Miss scaling problems

## Troubleshooting

**HPA not scaling**:
```bash
kubectl describe hpa <hpa-name>
kubectl get hpa <hpa-name> -o yaml
kubectl top pods
```

Common issues:
- Metrics-server not running
- No resource requests set
- Target already at min/max
- Insufficient permissions

**Check HPA events**:
```bash
kubectl get events --field-selector involvedObject.name=<hpa-name>
```

## Complete Examples

See `examples/` for:
- `hpa-basic.yaml` - Basic CPU-based HPA
- `hpa-multi-metric.yaml` - Multiple metrics
- `hpa-behavior.yaml` - Custom scaling behavior
- `vpa-example.yaml` - VPA configuration

## Monitoring Autoscaling

Create alerts for:
- Hitting min/max replicas
- Frequent scaling events
- Metric collection failures
- Resource exhaustion

---

[← Previous: Resource Management](01-resource-management.md) | [Next: High Availability →](03-high-availability.md)
