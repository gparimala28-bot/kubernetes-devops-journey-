# 🗄️ Kubernetes StatefulSet

## 🧠 Why StatefulSets Exist

Kubernetes Pods are ephemeral.

If a Pod is deleted, Kubernetes creates a new Pod to replace it. For stateless applications, this is not a problem. However, stateful applications such as databases store critical data that must survive Pod failures.

Without persistent storage and stable identity, application data can be lost.

StatefulSets solve this problem by providing stable identities, stable networking, and persistent storage for stateful workloads.


## 🧠 Core Concept

A StatefulSet is a Kubernetes workload resource designed for stateful applications.

Each Pod receives:

- Stable Pod identity
- Stable network identity
- Dedicated persistent storage
- Ordered startup and shutdown
- Controlled rolling updates

This allows applications such as databases and distributed systems to maintain consistency even when Pods are recreated.

## 🚀 StatefulSet Architecture

The following diagram illustrates how StatefulSets use Headless Services, PVCs, and Persistent Volumes to provide stable identities and persistent storage.

![StatefulSet Architecture](diagrams/statefulset-architecture.jpg)

```text
                Headless Service
                       │
                       ▼
                  StatefulSet
                       │
       ┌───────────────┼───────────────┐
       ▼               ▼               ▼

    mysql-0         mysql-1         mysql-2
       │               │               │
       ▼               ▼               ▼

     PVC-0           PVC-1           PVC-2
       │               │               │
       ▼               ▼               ▼

      PV-0            PV-1            PV-2
```

## 🏗️ Key Components

#### 1. StatefulSet- Manages stateful Pods and provides:

* Stable identities
* Persistent storage association
* Ordered lifecycle management

#### 2. Headless Service-Created using:
```yaml
clusterIP: None
```
Purpose:

* No load balancing
* Creates DNS records for individual Pods
* Enables direct Pod communication

#### 3. volumeClaimTemplates-Automatically creates a dedicated PVC for every Pod.
Example:
```text
mysql-0 → PVC-0
mysql-1 → PVC-1
mysql-2 → PVC-2
```
#### 4. Persistent Volumes-Each PVC is bound to a dedicated Persistent Volume.
```text
mysql-0 → PVC-0 → PV-0
mysql-1 → PVC-1 → PV-1
mysql-2 → PVC-2 → PV-2
```

## 🔑 Important Concepts

#### Stable Pod Identity-Pods receive predictable names.
```text
mysql-0
mysql-1
mysql-2
```
If a Pod fails, Kubernetes recreates it with the same identity.

#### Stable Network Identity-Each Pod receives a permanent DNS record through the Headless Service.

#### Persistent Storage-Each Pod receives dedicated storage through its own PVC and PV.

When a Pod is recreated, it reconnects to the same PVC and retains its data.

#### Ordered Deployment-Pods are created sequentially.
```text
mysql-0
   ↓
mysql-1
   ↓
mysql-2
```
#### Rolling Updates-Pods are updated one at a time in reverse order to minimize application disruption.

## 🧱 What I Implemented

* Created a Headless Service
* Deployed a MySQL StatefulSet with 2 replicas
* Verified automatic PV provisioning through the StorageClass
* Created a database and inserted sample data
* Deleted a StatefulSet Pod
* Verified Pod recreation with the same identity
* Confirmed PVC reuse after Pod recreation
* Verified data persistence after Pod deletion

## 🔑 Key Learnings

* StatefulSets are designed for stateful workloads
* Deployments are designed for stateless workloads
* Headless Services provide stable Pod networking
* StatefulSets provide stable Pod identities
* Each Pod receives dedicated persistent storage
* `volumeClaimTemplates` automate PVC creation
* Data persists across Pod recreation
* Pods are created and terminated in a predictable order
* StatefulSets are the preferred Kubernetes resource for databases

## 💡 Final Insight

StatefulSets ensure that application identity, network identity, and storage remain consistent throughout the Pod lifecycle.

By combining StatefulSets, Headless Services, Persistent Volumes, and Persistent Volume Claims, Kubernetes enables reliable and production-ready stateful applications.
