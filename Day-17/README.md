# DaemonSet

## What is a DaemonSet?

A DaemonSet is a Kubernetes workload resource that ensures a copy of a Pod runs on every eligible node in a cluster.

Whenever a new node joins the cluster, the DaemonSet automatically creates a Pod on that node. If a node is removed, the corresponding Pod is also removed.

## Why DaemonSet is Needed

Some applications need to run on every node rather than as a few replicas across the cluster.

Examples include:

* Log collection agents
* Monitoring agents
* Network plugins
* Storage daemons
* Security agents

Managing these Pods manually on each node would be difficult and error-prone. DaemonSets automate this process.

## How DaemonSet Works

Node Joins Cluster
↓
DaemonSet Controller Compares Desired State
↓
Detects Missing Pod on New Node
↓
Schedules DaemonSet Pod
↓
Continuously Reconciles State

## Key Characteristics

### 1. One Pod Per Node

A DaemonSet typically runs a single Pod on every eligible node.

### 2. Automatic Deployment

New nodes automatically receive the DaemonSet Pod.

### 3. Self-Healing

If a DaemonSet Pod crashes or is deleted, Kubernetes recreates it automatically.

### 4. Node-Specific Deployment

DaemonSets can target specific nodes using:

* nodeSelector
* nodeAffinity

### 5. Rolling Updates

Supports controlled updates with minimal disruption across cluster nodes.

## DaemonSet Update Strategies

### 1. RollingUpdate (Default)

Updates Pods gradually across nodes.

### 2. OnDelete

Pods are updated only when existing Pods are manually deleted.

## Example DaemonSet Configuration
```yaml 
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluent-bit
  namespace: kube-system

spec:
  selector:
    matchLabels:
      app: fluent-bit

  template:
    metadata:
      labels:
        app: fluent-bit

    spec:
      containers:
      - name: fluent-bit
        image: fluent/fluent-bit:latest

        volumeMounts:
        - name: varlog
          mountPath: /var/log

      volumes:
      - name: varlog
        hostPath:
          path: /var/log
```
This configuration ensures that one fluent-bit Pod runs on every eligible node in the cluster.

## Advantages of DaemonSets

### 1. Guaranteed Node Coverage

Ensures critical services run on all required nodes.

### 2. Automated Management

Automatically handles deployment and recovery of Pods.

### 3. Simplified Operations

Eliminates manual installation of node-level agents.

### 4. Consistent Cluster Monitoring

Ensures every node participates in logging and monitoring.

### 5. Controlled Updates

Supports rolling updates across nodes.

## Limitations

### 1. One Pod Per Node

Not suitable when multiple replicas are required on the same node.

### 2. No Workload-Based Scaling

DaemonSets scale with the number of nodes, not application demand.

### 3. Cluster-Wide Resource Consumption

Poorly configured resource requests can impact all nodes.

### 4. Not Designed for User Applications

Best suited for infrastructure services rather than business workloads.

## DaemonSet vs Deployment

| Feature | DaemonSet | Deployment |
|----------|-----------|------------|
| Purpose | Runs a Pod on every eligible node | Runs a specified number of replicas |
| Scaling | Scales with the number of nodes | Scales with the replica count |
| Pod Placement | One Pod per eligible node | Pods scheduled based on available resources |
| New Node Handling | Automatically creates a Pod on new eligible nodes | Maintains replica count and may schedule Pods on new nodes if needed |
| Common Use Cases | Logging, monitoring, networking, security agents | Web applications, APIs, microservices |

## Key Takeaways

* DaemonSet ensures a Pod runs on every eligible node.
* It automatically handles new nodes and failed Pods.
* DaemonSets are ideal for logging, monitoring, networking, and security agents.
* They support node selection, tolerations, and rolling updates.
* Unlike Deployments, DaemonSets scale based on node count rather than replica count.

## Final Note

I think of DaemonSet as Kubernetes' way of installing a service on every machine in the cluster. Instead of managing node-level agents manually, Kubernetes automatically ensures that the required Pod is always running on every eligible node, making cluster-wide operations such as monitoring, logging, networking, and security much easier to manage.
