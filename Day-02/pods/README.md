# Kubernetes Pods & Cluster Networking

## 1. Kubernetes Pods

A **Pod** is the smallest deployable unit in Kubernetes.

A Pod contains one or more containers that:

* Run together
* Share network namespace
* Share storage volumes
* Share the same lifecycle

### Key Characteristics

* Smallest scheduling unit
* Gets a unique Pod IP
* Containers communicate using `localhost`
* Ephemeral by nature
* Managed as a single unit

## 2. Why Kubernetes Uses Pods Instead of Containers

Kubernetes manages Pods because applications often require multiple tightly coupled containers.

### Benefits

### Shared Networking

Containers inside a Pod:

* Share one IP address
* Share port space
* Communicate through localhost

### Shared Storage

Containers can access common volumes.

### Co-Scheduling

All containers run on the same node.

### Unified Lifecycle

Kubernetes manages:

* Start
* Stop
* Restart
* Health checks

at Pod level.

## 3. Pod Lifecycle

### Step 1: Create Pod
```bash
kubectl apply -f pod.yaml
```
### Step 2: API Server

* Receives request
* Validates object
* Stores desired state in etcd

Pod state:

```text
Pending
```
### Step 3: Scheduler

Scheduler selects the best node based on:

* CPU
* Memory
* Affinity rules
* Taints/Tolerations

Updates:
```yaml
spec.nodeName
```
## Step 4: Kubelet

Kubelet on selected node:

* Watches assigned Pods
* Creates Pod sandbox
* Calls CNI plugin
* Pulls images
* Starts containers

## Step 5: Running State

After successful startup:

```text
Pending → Running
```
Pod becomes:

```text
Ready
```
## Pod Networking Fundamentals

### Core Principle

Every Pod gets a unique IP address.

### Characteristics

* Pod IP is different from Node IP
* No NAT between Pods
* Pods communicate directly
* Same networking model across cluster

Example:

```text
Node IP  : 192.168.1.10
Pod IP   : 10.244.0.5
```
## Kubernetes Networking Components

### 1. CNI Plugin

Examples:

* Calico
* Flannel
* Cilium
* Weave

#### Responsibilities

* Assign Pod IPs
* Create network interfaces
* Configure routes
* Enable Pod-to-Pod communication

### 2. Kube-Proxy

Responsible for Service networking.

#### Functions

* Watches Services
* Watches Endpoints
* Creates iptables/IPVS rules
* Load balances traffic

### 3. CoreDNS

Provides service discovery.

Example:

```text
api-service.default.svc.cluster.local
```
Resolves Service names into ClusterIP addresses.

## Communication Flow

### Container → Container (Same Pod)

Uses:
```text
localhost
```
Reason:Shared network namespace

### Pod → Pod (Same Node)

Uses:

```text
Pod IP
```
Traffic stays on the node.

### Pod → Pod (Different Nodes)

Managed by CNI plugin.

Traffic flows:

```text
Pod A
 ↓
Node A
 ↓
Network
 ↓
Node B
 ↓
Pod B
```
### Pod → Pod (Different Namespaces)

Possible using:

* Pod IP
* Service DNS

Recommended:

```text
service.namespace.svc.cluster.local
```
## Service Communication

Services provide stable access to Pods.

## Ingress

Provides HTTP/HTTPS routing.

Supports:

* Host-based routing
* Path-based routing
* TLS termination

## 🔒 Network Policies

NetworkPolicy controls Pod-level communication.

Acts as a firewall inside Kubernetes.

### Benefits
- Zero Trust Networking
- Traffic Isolation
- Namespace Segmentation
- Enhanced Security

### Policy Types

1.Ingress:Controls incoming traffic.

2.Egress:Controls outgoing traffic.

3.Default Deny Policy:Blocks all traffic unless explicitly allowed.

Example: Allowing traffic only from frontend Pods to backend Pods:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-frontend-to-backend
  namespace: default
spec:
  podSelector: # This policy applies to pods with label app: backend
    matchLabels:
      app: backend
  policyTypes:
    - Ingress # This policy only applies to incoming traffic
  ingress:
    - from:
        - podSelector: # Allow incoming traffic from pods with label app: frontend
            matchLabels:
              app: frontend
      ports: # Allow traffic on specific ports
        - protocol: TCP
          port: 8080
```
## 🚀Conclusion

In this section, I learned how Kubernetes Pods work and how networking enables communication across the cluster.

Key concepts learned:

Pod architecture and lifecycle
Why Kubernetes uses Pods instead of containers
Pod scheduling and creation process
Pod networking fundamentals
Pod-to-Pod communication
Service networking with Kube-Proxy
Service discovery using CoreDNS
Ingress for external access
Network Policies for traffic control and security

Kubernetes networking provides a simple and consistent networking model where every Pod gets its own IP address and can communicate across the cluster. Components like CNI, Kube-Proxy, CoreDNS, Services, Ingress, and Network Policies work together to enable secure, scalable, and reliable application communication.