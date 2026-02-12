# Key Kubernetes Components

Understanding the key components of Kubernetes is essential for working with the platform. This guide covers the fundamental building blocks.

## Core Components

### 1. Pod

**What it is**: The smallest deployable unit in Kubernetes.

**Key characteristics**:
- Contains one or more containers that share resources
- Containers in a Pod share the same network namespace (IP address)
- Containers in a Pod share storage volumes
- Pods are ephemeral - they can be created and destroyed at any time

**Analogy**: Think of a Pod as a "logical host" - like a single server that runs one or more closely related processes.

**Example use case**:
```
Pod containing:
- Main application container (web server)
- Helper container (log collector)
```

**Key points**:
- ✅ Pods are created and managed by higher-level controllers
- ✅ Each Pod gets its own unique IP address
- ✅ Containers within a Pod can communicate via localhost
- ⚠️ Pods are not persistent - don't rely on individual Pod identity

---

### 2. Node

**What it is**: A worker machine (physical or virtual) in the Kubernetes cluster.

**Key characteristics**:
- Runs containerized applications
- Managed by the control plane
- Can be a physical server, VM, or cloud instance
- Each Node contains the runtime (like Docker), kubelet, and kube-proxy

**Components on each Node**:
- **kubelet**: Agent that ensures containers are running in Pods
- **Container runtime**: Software that runs containers (Docker, containerd, CRI-O)
- **kube-proxy**: Maintains network rules for Pod communication

**Key points**:
- ✅ A cluster typically has multiple Nodes for redundancy
- ✅ Nodes report their status and available resources to the control plane
- ✅ Nodes can be added or removed from the cluster dynamically

---

### 3. Cluster

**What it is**: A set of Nodes managed as a single system.

**Key characteristics**:
- Consists of at least one control plane and one or more worker Nodes
- Provides a unified platform for running applications
- Resources are shared across the cluster

**Components**:
- **Control Plane**: Manages the cluster (scheduling, scaling, updates)
- **Worker Nodes**: Run application workloads

**Key points**:
- ✅ A cluster is the complete Kubernetes deployment
- ✅ Provides high availability and fault tolerance
- ✅ Can scale horizontally by adding more Nodes

---

### 4. Service

**What it is**: An abstraction that defines a logical set of Pods and a policy for accessing them.

**Why it's needed**:
- Pods are ephemeral and can be recreated with different IP addresses
- Services provide a stable endpoint to access Pods
- Handles load balancing across multiple Pod instances

**Types of Services**:
- **ClusterIP** (default): Exposes Service on a cluster-internal IP
- **NodePort**: Exposes Service on each Node's IP at a static port
- **LoadBalancer**: Exposes Service externally using a cloud provider's load balancer
- **ExternalName**: Maps Service to an external DNS name

**Example**:
```
Service "web-app" -> Routes traffic to Pods labeled "app=web"
- Even if Pods are replaced, Service endpoint remains stable
- Traffic is load-balanced across available Pods
```

**Key points**:
- ✅ Services provide stable networking for Pods
- ✅ Enable service discovery within the cluster
- ✅ Support load balancing automatically
- ✅ Decouple consumers from Pod implementation details

---

### 5. Deployment

**What it is**: A controller that manages a set of identical Pods.

**Key characteristics**:
- Ensures specified number of Pod replicas are running
- Handles rolling updates and rollbacks
- Provides declarative updates to applications

**What it manages**:
- Creates and manages ReplicaSets
- ReplicaSets ensure the desired number of Pods are running
- Handles Pod lifecycle and scaling

**Example workflow**:
```
1. You create Deployment: "Run 3 replicas of my app"
2. Deployment creates ReplicaSet
3. ReplicaSet creates 3 Pods
4. If a Pod fails, ReplicaSet creates a replacement
5. When you update the Deployment, it performs a rolling update
```

**Common operations**:
- **Scale**: Change number of replicas (scale up/down)
- **Update**: Roll out new application version
- **Rollback**: Revert to previous version if needed
- **Pause/Resume**: Control rollout process

**Key points**:
- ✅ Deployments are the recommended way to run stateless applications
- ✅ Provide declarative updates with rollback capability
- ✅ Handle zero-downtime deployments
- ✅ Automatically replace failed Pods

---

## How Components Work Together

Here's how these components interact in a typical scenario:

```
1. You create a Deployment specification
   ↓
2. Deployment creates Pods on Nodes in the Cluster
   ↓
3. Service provides stable access to these Pods
   ↓
4. If a Pod fails on a Node, Deployment creates a new one
   ↓
5. Service automatically routes traffic to healthy Pods
```

## Component Relationship Diagram

```
Cluster
├── Control Plane (manages everything)
└── Worker Nodes (run workloads)
    ├── Node 1
    │   ├── Pod 1 (containers)
    │   └── Pod 2 (containers)
    └── Node 2
        ├── Pod 3 (containers)
        └── Pod 4 (containers)

Deployment → ReplicaSet → Pods
Service → Routes traffic to Pods
```

## Key Takeaways

✅ **Pod**: Smallest unit, runs containers  
✅ **Node**: Worker machine that runs Pods  
✅ **Cluster**: Collection of Nodes managed together  
✅ **Service**: Stable network endpoint for accessing Pods  
✅ **Deployment**: Manages Pod lifecycle, updates, and scaling  

These five components form the foundation of Kubernetes. Understanding how they work together is crucial for deploying and managing applications effectively.

---

[← Previous: What is Kubernetes?](01-what-is-kubernetes.md) | [Next: Architecture Overview →](03-architecture-overview.md)
