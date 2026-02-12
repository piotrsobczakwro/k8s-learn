# Kubernetes Learning Exercises

Hands-on exercises to reinforce your learning at each level.

## 🟢 Beginner Exercises

### Exercise 1: Basic Pod Management
**Objective**: Practice creating and managing Pods

Tasks:
1. Create a Pod running nginx:1.21
2. Expose it on port 80
3. Access the Pod using port-forward
4. View the Pod logs
5. Execute a command inside the Pod
6. Delete the Pod

**Expected time**: 20 minutes

### Exercise 2: Your First Deployment
**Objective**: Deploy and scale an application

Tasks:
1. Create a Deployment with 3 replicas of nginx
2. Scale it to 5 replicas
3. Update the image to nginx:1.22
4. Rollback to the previous version
5. Delete the Deployment

**Expected time**: 30 minutes

### Exercise 3: Service Discovery
**Objective**: Expose applications using Services

Tasks:
1. Create a Deployment with nginx (3 replicas)
2. Create a ClusterIP Service
3. Create a NodePort Service
4. Test access to the Service
5. Clean up resources

**Expected time**: 30 minutes

---

## 🟡 Intermediate Exercises

### Exercise 4: Configuration Management
**Objective**: Manage configuration with ConfigMaps and Secrets

Tasks:
1. Create a ConfigMap with application settings
2. Create a Secret with database credentials
3. Create a Deployment that uses both
4. Verify environment variables inside Pods
5. Update the ConfigMap and observe behavior

**Expected time**: 45 minutes

### Exercise 5: Persistent Storage
**Objective**: Work with persistent volumes

Tasks:
1. Create a PersistentVolume (or use dynamic provisioning)
2. Create a PersistentVolumeClaim
3. Create a Pod that uses the PVC
4. Write data to the volume
5. Delete the Pod and create a new one
6. Verify data persisted

**Expected time**: 45 minutes

### Exercise 6: Networking with Ingress
**Objective**: Set up Ingress for HTTP routing

Tasks:
1. Install an Ingress controller (nginx-ingress)
2. Create two different applications (deployments + services)
3. Create an Ingress resource with path-based routing
4. Test accessing both applications via Ingress
5. Add TLS termination (bonus)

**Expected time**: 60 minutes

---

## 🟠 Advanced Exercises

### Exercise 7: StatefulSet Application
**Objective**: Deploy a stateful application

Tasks:
1. Create a headless Service
2. Create a StatefulSet for a database (e.g., MongoDB)
3. Scale the StatefulSet
4. Observe stable network identities
5. Verify persistent storage per Pod

**Expected time**: 60 minutes

### Exercise 8: Jobs and CronJobs
**Objective**: Run batch and scheduled workloads

Tasks:
1. Create a Job that runs to completion
2. Create a Job with parallelism
3. Create a CronJob that runs every 5 minutes
4. View Job and CronJob history
5. Clean up completed Jobs

**Expected time**: 45 minutes

### Exercise 9: Health Checks and Probes
**Objective**: Implement robust health checking

Tasks:
1. Create a Deployment with liveness probe
2. Create readiness probe
3. Simulate a failing health check
4. Observe Kubernetes self-healing
5. Add startup probe for slow-starting app

**Expected time**: 60 minutes

---

## 🔴 Expert Exercises

### Exercise 10: RBAC Configuration
**Objective**: Implement role-based access control

Tasks:
1. Create a ServiceAccount
2. Create a Role with specific permissions
3. Create a RoleBinding
4. Test permissions using kubectl with service account
5. Create ClusterRole and ClusterRoleBinding

**Expected time**: 90 minutes

### Exercise 11: Horizontal Pod Autoscaling
**Objective**: Implement autoscaling based on metrics

Tasks:
1. Install metrics-server
2. Create a Deployment with resource requests
3. Create HorizontalPodAutoscaler
4. Generate load on the application
5. Observe automatic scaling
6. Clean up

**Expected time**: 90 minutes

### Exercise 12: Complete Microservices Application
**Objective**: Deploy a multi-tier application

Tasks:
1. Deploy a database (StatefulSet with PVC)
2. Deploy a backend API (Deployment)
3. Deploy a frontend (Deployment)
4. Configure ConfigMaps and Secrets
5. Set up Services for communication
6. Create Ingress for external access
7. Implement health probes
8. Set up resource limits
9. Add Network Policies

**Expected time**: 3-4 hours

---

## 📝 Project Ideas

### Beginner Projects
1. **Personal Blog**: Deploy WordPress with MySQL
2. **Static Website**: Deploy a static site with nginx
3. **Simple API**: Deploy a REST API with database

### Intermediate Projects
1. **Microservices App**: Deploy a multi-service application
2. **Monitoring Stack**: Set up Prometheus and Grafana
3. **CI/CD Pipeline**: Deploy Jenkins or GitLab CI

### Advanced Projects
1. **Service Mesh**: Implement Istio for microservices
2. **Multi-Tenant Platform**: Set up namespace isolation with RBAC
3. **Disaster Recovery**: Implement backup and restore with Velero

---

## 🎯 Challenge: Complete Application Stack

**Objective**: Deploy a production-like e-commerce application

**Requirements**:
1. **Frontend**: React app (3 replicas)
2. **API**: Node.js/Python backend (3 replicas)
3. **Database**: PostgreSQL (StatefulSet)
4. **Cache**: Redis (StatefulSet)
5. **Message Queue**: RabbitMQ (StatefulSet)

**Configuration**:
- Use ConfigMaps for app config
- Use Secrets for credentials
- Implement proper health checks
- Set resource requests and limits
- Use HPA for frontend and API
- Set up Ingress with TLS
- Implement Network Policies
- Add RBAC for service accounts

**Monitoring**:
- Centralized logging
- Metrics collection
- Alerting setup

**Expected time**: 1-2 days

---

## ✅ Self-Assessment Checklist

### Beginner Level
- [ ] Can create and manage Pods
- [ ] Can create Deployments and Services
- [ ] Can use kubectl effectively
- [ ] Understand basic YAML structure
- [ ] Can troubleshoot common issues

### Intermediate Level
- [ ] Can configure applications with ConfigMaps/Secrets
- [ ] Can manage persistent storage
- [ ] Can set up Ingress
- [ ] Understand Kubernetes networking
- [ ] Can use different workload types

### Advanced Level
- [ ] Can deploy stateful applications
- [ ] Can implement health checks
- [ ] Can configure RBAC
- [ ] Can set up monitoring and logging
- [ ] Can implement autoscaling

### Expert Level
- [ ] Can design production architectures
- [ ] Can implement security best practices
- [ ] Can troubleshoot complex issues
- [ ] Can use operators and CRDs
- [ ] Can implement GitOps workflows

---

## 📚 Additional Resources

- Solutions to exercises available in each lesson's `examples/` directory
- Join Kubernetes community forums for help
- Practice on free cloud platforms (GKE, EKS, AKS free tiers)
- Contribute to open-source Kubernetes projects

**Happy Learning! 🚀**
