# kubectl Basics

kubectl (pronounced "kube-control" or "kube-cuttle") is your primary tool for interacting with Kubernetes clusters.

## Basic Syntax

```bash
kubectl [command] [TYPE] [NAME] [flags]
```

- **command**: What you want to do (get, create, delete, describe, etc.)
- **TYPE**: Resource type (pod, deployment, service, etc.)
- **NAME**: Resource name (optional)
- **flags**: Additional options

## Essential kubectl Commands

### 1. Getting Information

**View cluster info**:
```bash
kubectl cluster-info
```

**List nodes**:
```bash
kubectl get nodes
```

**List all resources in a namespace**:
```bash
kubectl get all
kubectl get all -n kube-system  # in specific namespace
```

**Get detailed information**:
```bash
kubectl describe node <node-name>
kubectl describe pod <pod-name>
```

**Get YAML/JSON output**:
```bash
kubectl get pod <pod-name> -o yaml
kubectl get pod <pod-name> -o json
```

### 2. Creating Resources

**Create from a file**:
```bash
kubectl create -f my-pod.yaml
kubectl apply -f my-pod.yaml  # preferred - can update existing resources
```

**Create from a directory**:
```bash
kubectl apply -f ./my-configs/
```

**Create from URL**:
```bash
kubectl apply -f https://example.com/config.yaml
```

**Imperative creation** (quick testing):
```bash
# Create a pod
kubectl run nginx --image=nginx

# Create a deployment
kubectl create deployment nginx --image=nginx

# Create a service
kubectl expose deployment nginx --port=80 --type=NodePort
```

### 3. Updating Resources

**Apply changes**:
```bash
kubectl apply -f my-deployment.yaml
```

**Edit resource directly**:
```bash
kubectl edit deployment my-deployment
```

**Scale deployments**:
```bash
kubectl scale deployment my-deployment --replicas=5
```

**Set image (rolling update)**:
```bash
kubectl set image deployment/my-deployment nginx=nginx:1.21
```

### 4. Deleting Resources

**Delete specific resource**:
```bash
kubectl delete pod my-pod
kubectl delete deployment my-deployment
```

**Delete from file**:
```bash
kubectl delete -f my-pod.yaml
```

**Delete all resources of a type**:
```bash
kubectl delete pods --all
```

### 5. Viewing Logs and Debugging

**View logs**:
```bash
kubectl logs my-pod
kubectl logs my-pod -f  # follow logs (like tail -f)
kubectl logs my-pod --previous  # logs from previous instance
```

**For multi-container pods**:
```bash
kubectl logs my-pod -c container-name
```

**Execute commands in a pod**:
```bash
kubectl exec my-pod -- ls /
kubectl exec -it my-pod -- /bin/bash  # interactive shell
```

**Port forwarding** (access pod locally):
```bash
kubectl port-forward pod/my-pod 8080:80
# Now access http://localhost:8080
```

### 6. Working with Namespaces

**List namespaces**:
```bash
kubectl get namespaces
```

**Create namespace**:
```bash
kubectl create namespace dev
```

**Use namespace**:
```bash
kubectl get pods -n dev
kubectl get pods --all-namespaces  # or -A
```

**Set default namespace**:
```bash
kubectl config set-context --current --namespace=dev
```

## Common Resource Types (Short Names)

| Full Name | Short Name | Example |
|-----------|-----------|---------|
| pods | po | `kubectl get po` |
| services | svc | `kubectl get svc` |
| deployments | deploy | `kubectl get deploy` |
| replicasets | rs | `kubectl get rs` |
| namespaces | ns | `kubectl get ns` |
| nodes | no | `kubectl get no` |
| configmaps | cm | `kubectl get cm` |
| secrets | - | `kubectl get secrets` |
| persistentvolumes | pv | `kubectl get pv` |
| persistentvolumeclaims | pvc | `kubectl get pvc` |

## Useful Flags

**Output formats**:
```bash
-o wide          # More details
-o yaml          # YAML format
-o json          # JSON format
-o name          # Only names
```

**Watch for changes**:
```bash
kubectl get pods --watch  # or -w
```

**Show labels**:
```bash
kubectl get pods --show-labels
```

**Filter by label**:
```bash
kubectl get pods -l app=nginx
kubectl get pods -l env=prod,tier=frontend
```

**Sort by field**:
```bash
kubectl get pods --sort-by=.metadata.name
kubectl get pods --sort-by=.status.startTime
```

## Helpful Tips

### 1. Use `kubectl explain`

Get documentation for any resource:
```bash
kubectl explain pod
kubectl explain pod.spec
kubectl explain pod.spec.containers
```

### 2. Use Auto-completion

**Bash**:
```bash
source <(kubectl completion bash)
echo "source <(kubectl completion bash)" >> ~/.bashrc
```

**Zsh**:
```bash
source <(kubectl completion zsh)
echo "source <(kubectl completion zsh)" >> ~/.zshrc
```

### 3. Create Aliases

Add to your `.bashrc` or `.zshrc`:
```bash
alias k=kubectl
alias kgp='kubectl get pods'
alias kgs='kubectl get services'
alias kgd='kubectl get deployments'
alias kdp='kubectl describe pod'
alias kl='kubectl logs'
alias kex='kubectl exec -it'
```

### 4. Dry Run

Test commands without creating resources:
```bash
kubectl create deployment nginx --image=nginx --dry-run=client -o yaml
```

This is useful for:
- Generating YAML templates
- Testing configurations
- Learning proper syntax

## Practice Exercises

Try these commands to familiarize yourself:

1. **List all pods** in all namespaces
2. **Describe** a pod from kube-system namespace
3. **Get pod details** in YAML format
4. **Create a pod** named "test-pod" using nginx image
5. **View logs** from the pod you created
6. **Execute** a command inside the pod
7. **Delete** the pod

## Key Takeaways

✅ kubectl is the main CLI tool for Kubernetes  
✅ Use `kubectl get` to list resources  
✅ Use `kubectl describe` for detailed information  
✅ Use `kubectl apply` for creating/updating resources  
✅ Use `kubectl logs` for debugging  
✅ Use `kubectl exec` to run commands in containers  
✅ Learn short names and aliases for efficiency

---

[← Previous: Installation](01-installation.md) | [Next: Your First Pod →](03-first-pod.md)
