# Kubernetes Architecture Overview

Understanding the architecture of Kubernetes helps you grasp how all the components work together to manage containerized applications.

## High-Level Architecture

A Kubernetes cluster consists of two main parts:

1. **Control Plane** (Master): The brain of the cluster
2. **Worker Nodes**: Execute the actual workloads

```
┌─────────────────────────────────────────────────────────┐
│                    Control Plane                        │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌────────┐ │
│  │   API    │  │ Scheduler│  │Controller│  │  etcd  │ │
│  │  Server  │  │          │  │ Manager  │  │        │ │
│  └──────────┘  └──────────┘  └──────────┘  └────────┘ │
└─────────────────────────────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          │                │                │
┌─────────────────┐ ┌─────────────────┐ ┌─────────────────┐
│   Worker Node 1 │ │   Worker Node 2 │ │   Worker Node N │
│  ┌───────────┐  │ │  ┌───────────┐  │ │  ┌───────────┐  │
│  │  kubelet  │  │ │  │  kubelet  │  │ │  │  kubelet  │  │
│  │kube-proxy │  │ │  │kube-proxy │  │ │  │kube-proxy │  │
│  │ Container │  │ │  │ Container │  │ │  │ Container │  │
│  │  Runtime  │  │ │  │  Runtime  │  │ │  │  Runtime  │  │
│  └───────────┘  │ │  └───────────┘  │ │  └───────────┘  │
│  ┌───────────┐  │ │  ┌───────────┐  │ │  ┌───────────┐  │
│  │   Pods    │  │ │  │   Pods    │  │ │  │   Pods    │  │
│  └───────────┘  │ │  └───────────┘  │ │  └───────────┘  │
└─────────────────┘ └─────────────────┘ └─────────────────┘
```

## Control Plane Components

The control plane makes global decisions about the cluster and detects/responds to cluster events.

### 1. API Server (kube-apiserver)

**Role**: Front-end for the Kubernetes control plane.

**Responsibilities**:
- Exposes the Kubernetes API
- Serves as the gateway for all administrative tasks
- Validates and processes API requests
- Only component that directly interacts with etcd

**Why it matters**:
- All components communicate through the API server
- `kubectl` commands interact with the API server
- Authentication and authorization happen here

### 2. etcd

**Role**: Consistent and highly-available key-value store.

**Responsibilities**:
- Stores all cluster data (desired state, actual state, metadata)
- Source of truth for the cluster
- Provides distributed consensus

**Why it matters**:
- Contains all cluster configuration and state
- Critical for cluster operation - if etcd is lost, cluster state is lost
- Should be backed up regularly

### 3. Scheduler (kube-scheduler)

**Role**: Assigns Pods to Nodes.

**Responsibilities**:
- Watches for newly created Pods with no assigned Node
- Selects optimal Node for each Pod based on:
  - Resource requirements (CPU, memory)
  - Hardware/software/policy constraints
  - Affinity and anti-affinity specifications
  - Data locality
  - Resource availability

**Decision factors**:
- Node capacity (available resources)
- Pod requirements
- Quality of service requirements
- Custom constraints and policies

### 4. Controller Manager (kube-controller-manager)

**Role**: Runs controller processes that regulate cluster state.

**Responsibilities**:
Contains multiple controllers, each responsible for a specific aspect:

- **Node Controller**: Monitors Node health
- **Replication Controller**: Maintains correct number of Pods
- **Endpoints Controller**: Populates Endpoints objects (joins Services & Pods)
- **Service Account & Token Controllers**: Create default accounts and API access tokens

**How it works**:
- Controllers watch the desired state (from API server)
- Compare it to actual state
- Take action to reconcile differences

## Worker Node Components

Each worker Node runs components necessary to maintain running Pods and provide the Kubernetes runtime environment.

### 1. kubelet

**Role**: Primary node agent that runs on each Node.

**Responsibilities**:
- Registers the Node with the API server
- Watches for Pod assignments to its Node
- Ensures containers described in Pods are running and healthy
- Reports Node and Pod status to the API server
- Executes liveness and readiness probes

**Key functions**:
- Takes Pod specifications (PodSpecs) from API server
- Ensures containers are running in those Pods
- Monitors container health
- Manages container lifecycle

### 2. kube-proxy

**Role**: Network proxy that maintains network rules on Nodes.

**Responsibilities**:
- Implements Service abstraction
- Maintains network rules for Pod communication
- Forwards traffic to appropriate Pods
- Performs load balancing for Services

**How it works**:
- Watches the API server for Service and Endpoint changes
- Updates network rules (iptables, IPVS, or other mechanisms)
- Enables Pod-to-Pod and external-to-Pod communication

### 3. Container Runtime

**Role**: Software responsible for running containers.

**Supported runtimes**:
- **containerd**: Industry-standard container runtime
- **CRI-O**: Lightweight runtime for Kubernetes
- **Docker Engine**: (via dockershim, deprecated in newer versions)

**Responsibilities**:
- Pulling container images
- Running containers
- Managing container lifecycle
- Providing container isolation

## Communication Flow

Here's how components communicate in a typical workflow:

### Creating a Deployment

```
1. User runs: kubectl create deployment
   ↓
2. kubectl → API Server: Create Deployment request
   ↓
3. API Server → etcd: Store Deployment object
   ↓
4. Deployment Controller (in Controller Manager) detects new Deployment
   ↓
5. Deployment Controller → API Server: Create ReplicaSet
   ↓
6. ReplicaSet Controller detects new ReplicaSet
   ↓
7. ReplicaSet Controller → API Server: Create Pods
   ↓
8. Scheduler detects unscheduled Pods
   ↓
9. Scheduler → API Server: Assign Pods to Nodes
   ↓
10. kubelet on assigned Node detects Pod assignment
    ↓
11. kubelet → Container Runtime: Create containers
    ↓
12. kubelet → API Server: Report Pod status
```

### Service Traffic Flow

```
1. External request arrives at Service IP
   ↓
2. kube-proxy routes to appropriate Pod
   ↓
3. Pod handles request
   ↓
4. Response returns through same path
```

## Data Flow

```
┌──────────┐
│   User   │
└─────┬────┘
      │ kubectl commands
      ▼
┌──────────┐     ┌──────┐
│   API    │────▶│ etcd │
│  Server  │◀────└──────┘
└─────┬────┘
      │
      ├──────────▶ Scheduler (assigns Pods)
      │
      ├──────────▶ Controller Manager (maintains state)
      │
      └──────────▶ kubelet (on each Node)
                   │
                   └──────▶ Container Runtime
```

## Key Architectural Principles

### 1. Declarative Configuration
- You declare desired state
- Kubernetes reconciles actual state to match

### 2. Controller Pattern
- Controllers continuously monitor and adjust
- Self-healing and automatic reconciliation

### 3. Loosely Coupled Components
- Components communicate via API server
- Can be updated/replaced independently

### 4. API-Driven
- Everything interacts through the API
- Extensible and programmable

### 5. Distributed System
- Control plane can be highly available
- Workloads distributed across multiple Nodes

## Key Takeaways

✅ **Control Plane**: Manages cluster (API Server, etcd, Scheduler, Controllers)  
✅ **Worker Nodes**: Run workloads (kubelet, kube-proxy, container runtime)  
✅ **API Server**: Central communication hub for all components  
✅ **etcd**: Stores all cluster state  
✅ **Scheduler**: Assigns Pods to Nodes intelligently  
✅ **Controllers**: Maintain desired state through reconciliation loops  
✅ **kubelet**: Ensures containers run on each Node  
✅ **kube-proxy**: Handles networking and load balancing  

Understanding this architecture helps you troubleshoot issues, optimize performance, and make informed decisions about cluster configuration.

---

[← Previous: Key Components](02-key-components.md) | [Next: Use Cases and Benefits →](04-use-cases-and-benefits.md)
