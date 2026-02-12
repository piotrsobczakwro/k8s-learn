# Installation and Setup

Setting up your Kubernetes development environment is the first step in your hands-on journey.

## Local Kubernetes Options

There are several ways to run Kubernetes locally for learning:

### 1. Minikube (Recommended for Beginners)

**What it is**: A tool that runs a single-node Kubernetes cluster in a VM on your laptop.

**Pros**:
- ✅ Easy to install and use
- ✅ Supports multiple container runtimes
- ✅ Includes add-ons for common services
- ✅ Works on Windows, macOS, and Linux

**Installation**:

```bash
# macOS
brew install minikube

# Windows (with Chocolatey)
choco install minikube

# Linux
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

**Start Minikube**:
```bash
minikube start
```

### 2. Kind (Kubernetes in Docker)

**What it is**: Runs Kubernetes clusters using Docker containers as nodes.

**Pros**:
- ✅ Fast startup time
- ✅ Lightweight
- ✅ Great for CI/CD pipelines
- ✅ Supports multi-node clusters

**Installation**:

```bash
# macOS/Linux
curl -Lo ./kind https://kind.sigs.k8s.io/dl/latest/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind

# Or with package managers
brew install kind  # macOS
```

**Create a cluster**:
```bash
kind create cluster --name my-cluster
```

### 3. Docker Desktop

**What it is**: Built-in Kubernetes support in Docker Desktop.

**Pros**:
- ✅ Easy to enable if you already use Docker Desktop
- ✅ Integrated with Docker
- ✅ Simple UI toggle

**Setup**:
1. Install Docker Desktop
2. Go to Settings → Kubernetes
3. Check "Enable Kubernetes"
4. Click "Apply & Restart"

### 4. k3d (Lightweight Kubernetes)

**What it is**: Runs k3s (lightweight Kubernetes) in Docker.

**Pros**:
- ✅ Very fast
- ✅ Low resource usage
- ✅ Multi-cluster support

**Installation**:
```bash
curl -s https://raw.githubusercontent.com/k3d-io/k3d/main/install.sh | bash
```

## Installing kubectl

**kubectl** is the command-line tool for interacting with Kubernetes clusters.

### Installation Methods

**macOS**:
```bash
brew install kubectl
```

**Windows** (with Chocolatey):
```bash
choco install kubernetes-cli
```

**Linux**:
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

### Verify Installation

```bash
kubectl version --client
```

You should see output showing the kubectl client version.

## Verify Your Cluster

Once you've installed Kubernetes and kubectl, verify everything is working:

```bash
# Check cluster information
kubectl cluster-info

# Check nodes
kubectl get nodes

# Check all resources
kubectl get all --all-namespaces
```

Expected output:
```
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   5m    v1.28.0
```

## Understanding Contexts

Kubernetes uses **contexts** to manage multiple clusters. A context contains:
- Cluster information
- User credentials
- Namespace preference

```bash
# View current context
kubectl config current-context

# List all contexts
kubectl config get-contexts

# Switch context
kubectl config use-context <context-name>
```

## Setting Up Your Workspace

Create a directory for your Kubernetes YAML files:

```bash
mkdir -p ~/k8s-practice
cd ~/k8s-practice
```

## Troubleshooting

### Common Issues

**Issue**: `minikube start` fails with VM driver error
- **Solution**: Specify a driver: `minikube start --driver=docker`

**Issue**: kubectl can't connect to cluster
- **Solution**: Check cluster is running: `minikube status` or `kind get clusters`

**Issue**: Insufficient resources
- **Solution**: Increase resources: `minikube start --memory=4096 --cpus=2`

## Next Steps

Now that you have:
- ✅ Installed Kubernetes locally
- ✅ Installed kubectl
- ✅ Verified your cluster is running

You're ready to learn kubectl basics!

---

[← Back to Lesson Overview](README.md) | [Next: kubectl Basics →](02-kubectl-basics.md)
