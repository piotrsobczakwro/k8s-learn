# RBAC - Role-Based Access Control

Control who can do what in your Kubernetes cluster.

## What is RBAC?

RBAC (Role-Based Access Control) regulates access to Kubernetes resources based on roles assigned to users, groups, or service accounts.

## Core Concepts

### 1. Subjects (Who)
- **Users**: Human users
- **Groups**: Collections of users
- **ServiceAccounts**: Application identities

### 2. Resources (What)
- Pods, Services, Deployments, etc.
- API groups and endpoints

### 3. Verbs (Actions)
- `get`, `list`, `watch`
- `create`, `update`, `patch`, `delete`

### 4. Roles (Permissions)
- **Role**: Namespace-scoped permissions
- **ClusterRole**: Cluster-wide permissions

### 5. Bindings (Assignment)
- **RoleBinding**: Binds Role to subjects in a namespace
- **ClusterRoleBinding**: Binds ClusterRole to subjects cluster-wide

## Creating a Role

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""]
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
```

This Role allows reading Pods in the `default` namespace.

## Creating a RoleBinding

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
- kind: User
  name: jane
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: pod-reader
  apiGroup: rbac.authorization.k8s.io
```

This binds the `pod-reader` Role to user `jane`.

## ServiceAccounts

ServiceAccounts provide identities for Pods.

### Create a ServiceAccount

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: my-service-account
  namespace: default
```

### Use in a Pod

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  serviceAccountName: my-service-account
  containers:
  - name: app
    image: nginx
```

## ClusterRole Example

For cluster-wide permissions:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: secret-reader
rules:
- apiGroups: [""]
  resources: ["secrets"]
  verbs: ["get", "watch", "list"]
```

## ClusterRoleBinding Example

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: read-secrets-global
subjects:
- kind: Group
  name: manager
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: ClusterRole
  name: secret-reader
  apiGroup: rbac.authorization.k8s.io
```

## Common Patterns

### Read-Only Access

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: reader
rules:
- apiGroups: ["", "apps", "batch"]
  resources: ["*"]
  verbs: ["get", "list", "watch"]
```

### Deployment Manager

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: deployment-manager
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "create", "update", "patch", "delete"]
```

## Checking Permissions

```bash
# Check if you can do an action
kubectl auth can-i create deployments

# Check for another user
kubectl auth can-i create deployments --as jane

# Check for a service account
kubectl auth can-i list secrets --as system:serviceaccount:default:my-service-account
```

## Best Practices

✅ **Principle of least privilege**: Grant minimum permissions needed  
✅ **Use ServiceAccounts for applications**  
✅ **Avoid using default ServiceAccount**  
✅ **Review permissions regularly**  
✅ **Use namespaces for isolation**  
✅ **Aggregate ClusterRoles when possible**

## Complete Example

See `examples/rbac-demo.yaml` for a complete working example.

---

[← Back to Lesson Overview](README.md) | [Next: Security Contexts →](02-security-contexts.md)
