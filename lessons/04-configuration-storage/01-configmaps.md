# ConfigMaps

ConfigMaps allow you to decouple configuration from container images, making applications more portable.

## What are ConfigMaps?

ConfigMaps store configuration data as key-value pairs that can be consumed by Pods.

**Use cases**:
- Application configuration files
- Command-line arguments
- Environment variables
- Configuration data

## Creating ConfigMaps

### Method 1: From Literal Values

```bash
kubectl create configmap my-config \
  --from-literal=database.host=mysql \
  --from-literal=database.port=3306
```

### Method 2: From Files

Create a file `app.properties`:
```
database.host=mysql
database.port=3306
database.name=myapp
```

Create ConfigMap:
```bash
kubectl create configmap app-config --from-file=app.properties
```

### Method 3: From YAML

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
data:
  database.host: "mysql"
  database.port: "3306"
  database.name: "myapp"
  app.properties: |
    database.host=mysql
    database.port=3306
    database.name=myapp
```

Apply it:
```bash
kubectl apply -f configmap.yaml
```

## Using ConfigMaps in Pods

### As Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-demo
spec:
  containers:
  - name: app
    image: nginx
    env:
    - name: DATABASE_HOST
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database.host
    - name: DATABASE_PORT
      valueFrom:
        configMapKeyRef:
          name: app-config
          key: database.port
```

### Load All Keys as Environment Variables

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-demo-all
spec:
  containers:
  - name: app
    image: nginx
    envFrom:
    - configMapRef:
        name: app-config
```

### As Volume Mount

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: config-volume-demo
spec:
  containers:
  - name: app
    image: nginx
    volumeMounts:
    - name: config-volume
      mountPath: /etc/config
  volumes:
  - name: config-volume
    configMap:
      name: app-config
```

This mounts all ConfigMap keys as files in `/etc/config/`.

### Mount Specific Keys

```yaml
volumes:
- name: config-volume
  configMap:
    name: app-config
    items:
    - key: app.properties
      path: application.properties
```

## Managing ConfigMaps

**View ConfigMaps**:
```bash
kubectl get configmaps
kubectl describe configmap app-config
kubectl get configmap app-config -o yaml
```

**Update ConfigMap**:
```bash
kubectl edit configmap app-config
# Or replace
kubectl create configmap app-config --from-literal=key=newvalue --dry-run=client -o yaml | kubectl apply -f -
```

**Delete ConfigMap**:
```bash
kubectl delete configmap app-config
```

## Best Practices

✅ **Use ConfigMaps for non-sensitive data only**  
✅ **Version your ConfigMaps** (e.g., app-config-v1, app-config-v2)  
✅ **Use immutable ConfigMaps** in production  
✅ **Document what each key represents**  
✅ **Keep ConfigMaps small** (max 1MB)

## Immutable ConfigMaps

Kubernetes 1.21+ supports immutable ConfigMaps:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
immutable: true
data:
  database.host: "mysql"
```

Benefits:
- Protects from accidental updates
- Improves performance (kubelet doesn't watch for changes)
- Reduces load on API server

## Complete Example

See `examples/configmap-demo.yaml` for a full example with Deployment using ConfigMap.

## Hands-On Exercise

1. Create a ConfigMap with application settings
2. Create a Deployment that uses the ConfigMap as environment variables
3. Update the ConfigMap and observe Pod behavior
4. Create another Deployment that mounts the ConfigMap as a file

Solutions in `examples/` directory.

---

[← Back to Lesson Overview](README.md) | [Next: Secrets →](02-secrets.md)
