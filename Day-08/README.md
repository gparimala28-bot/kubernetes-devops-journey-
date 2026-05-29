# 💾 Kubernetes Volumes (`emptyDir` & `hostPath`)

## Overview

Containers are ephemeral by nature. Any data stored inside a container's filesystem is lost when the container is removed.

Kubernetes volumes provide storage that exists outside the container filesystem and can be shared between containers within a Pod.

This helps applications:

* Store temporary data
* Share files between containers
* Access node-level resources
* Improve application reliability

## 🧠 Core Concept

Without a Volume:
```text
Pod
 └── Container
      └── Data

Pod Deleted
      ↓
Data Lost
```
With a Volume:
```text
Pod
 ├── Container
 └── Volume
      └── Data

Container Restart
      ↓
Data Survives
```
Volumes solve temporary storage challenges inside Kubernetes Pods.

## 🚀 Kubernetes Storage Architecture

```text
Application
      ↓
Pod
      ↓
Volume
      ↓
Node Storage
```
Kubernetes provides multiple volume types.

In this lab, i explored:

* emptyDir
* hostPath

## 🏗️ Volume Types Covered

| Volume Type | Purpose                  |
| ----------- | ------------------------ |
| emptyDir    | Temporary Pod storage    |
| hostPath    | Access host node storage |

## 📦 emptyDir Volume

An emptyDir volume is temporary storage created automatically when a Pod starts.

### Key Characteristics

* Created when Pod starts
* Shared by containers in the same Pod
* Survives container restarts
* Deleted when Pod is deleted
* Can use disk or memory (tmpfs)

### Common Use Cases

* Temporary files
* Application caching
* Build and compilation tasks
* Sharing files between containers
* Sidecar container communication

### Limitations

* Data lost when Pod is deleted
* Cannot be shared across Pods
* Not suitable for persistent storage

## 🖥️ hostPath Volume

A hostPath volume mounts files or directories directly from the Kubernetes worker node.

### Key Characteristics

* Uses node filesystem
* Data persists beyond Pod lifecycle
* Node-dependent storage
* Provides access to host resources

### Common Use Cases

* Log collection
* Monitoring agents
* CSI Drivers
* Hardware/device access
* Infrastructure workloads

### Risks

* Security concerns
* Host filesystem exposure
* Node dependency
* Not recommended for application persistence

## ⚖️ emptyDir vs hostPath

| Feature          | emptyDir          | hostPath             |
| ---------------- | ----------------- | -------------------- |
| Storage Lifetime | Pod Lifecycle     | Node Lifecycle       |
| Data Lost On     | Pod Deletion      | Node Deletion        |
| Persistence      | No                | Yes                  |
| Security Risk    | Low               | High                 |
| Portability      | High              | Low                  |
| Best Use Case    | Temporary Storage | Host Resource Access |

---

# 🧪 Hands-On Implementation

## Lab 1: Pod Without Storage

### Objective

Understand why Kubernetes storage is required.

### Steps

Create Pod:
```bash
kubectl apply -f pod-no-storage.yaml
```
Create file inside container:
```bash
echo "hello kubernetes" > /tmp/test.txt
```
Delete Pod:
```bash
kubectl delete pod no-storage-pod
```
Recreate Pod and verify file.

### Observation
```text
File not found
```
Learning-Container filesystem is temporary.

## Lab 2: emptyDir Volume

### Objective

Understand Pod-level storage.

### Steps

Create Pod using emptyDir.

Store data:
```bash
echo "persistent inside pod" > /data/test.txt
```
Restart container process.

Verify file:
```bash
cat /data/test.txt
```
Delete Pod and recreate.Verify again.

### Observation
```text
Container Restart
      ↓
Data Available

Pod Deletion
      ↓
Data Lost
```
Learned that Container restart retains emptyDir data

Pod deletion removes emptyDir storage completely

## 🔑 Key Learnings

* Containers are ephemeral.
* Volumes provide storage outside the container filesystem.
* emptyDir is temporary Pod-level storage.
* hostPath provides access to node storage.
* emptyDir survives container restarts.
* hostPath persists on the node.
* hostPath should be used carefully due to security risks.
* Understanding volumes is the foundation for PVs and PVCs.

## 💡 Final Insight

Kubernetes volumes provide storage beyond the container lifecycle and enable applications to store and share data effectively.

While emptyDir is ideal for temporary workloads, hostPath is primarily used for infrastructure-level integrations.

Understanding these volume types is essential before learning Persistent Volumes (PVs), Persistent Volume Claims (PVCs), and StorageClasses, which provide true persistent storage for stateful applications.
