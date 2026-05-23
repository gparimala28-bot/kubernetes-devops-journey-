# ☸️ Kubernetes Services

# 🧠 Why Kubernetes Services Exist

Pods in Kubernetes are **ephemeral**.

When a Pod restarts, its IP address changes.

```text id="x5q5ks"
Old Pod IP → 10.244.0.5
New Pod IP → 10.244.0.9
```
If users access Pods directly using Pod IPs, the application breaks whenever Pods change.

Kubernetes Services solve this problem by providing a **stable networking layer** in front of Pods.

# 🧩 What is a Kubernetes Service?

A Service is a Kubernetes object used to provide stable communication and access to Pods.

Instead of accessing Pods directly:
```text id="6tzm5g"
User → Pod IP ❌
```
Users access:

```text id="sd1v3u"
User → Service → Pods ✅
```
The Service automatically routes traffic to healthy Pods.

# 🔑 Key Features of Services

* Stable IP address
* Stable DNS name
* Load balancing
* Service discovery
* Internal Pod communication
* External application exposure
* Automatically detects new Pods
* Works using Labels and Selectors

# ⚙️ How Services Work

Services use:
* Labels
* Selectors
## Example Label
```yaml id="8f9v0s"
labels:
  app: payment
```
## Example Selector
```yaml id="cqwb4z"
selector:
  app: payment
```
The Service automatically finds all Pods matching the selector.

# 🚀 Kubernetes Service Architecture
```text id="8q4g7l"
User
  ↓
Service
  ↓
Pods
```
The Service acts as a stable entry point while Pods can change internally.

# 🔄 Service Discovery
Services provide automatic Pod discovery.

Even if:

* Pods restart
* Pod IPs change
* New replicas are created

The Service continues routing traffic correctly.This is achieved using Labels and Selectors instead of tracking Pod IPs directly.

# 🌐 Kubernetes DNS
Every Service gets a stable internal DNS name.
## DNS Format
```text id="4q5vdf"
service-name.namespace.svc.cluster.local
```
Example:
```text id="rz93sn"
payment.default.svc.cluster.local
```
Pods communicate internally using DNS instead of IP addresses.

# 📦 Types of Kubernetes Services

## 1. ClusterIP (Default)
Used for internal communication inside the cluster.
### 🔑 Features
* Internal access only
* Default Service type
* Used for Pod-to-Pod communication
* Not accessible from outside cluster

## 2. NodePort
Exposes application using:
```text id="7p6txf"
NodeIP + NodePort
```
### 🔑 Features
* External access through worker node
* Accessible inside organization/network
* Opens a static port on every node

## 3. LoadBalancer
Exposes application to the internet using a cloud load balancer.
### 🔑 Features
* Public internet access
* Creates external load balancer automatically
* Used in cloud environments
### Common Platforms
* AWS EKS
* Azure AKS
* Google GKE

# 🛠️ Hands-On Practice — Lab

# Creating a deployment 
```bash id="pdqxyj"
vim deployment.yaml
```
# 🚀 Creating a Service
## STEP 1 — Create Service YAML
```bash id="pdqxyj"
vim service.yaml
```
## STEP 2 — Apply Service
```bash id="3vxbvv"
kubectl apply -f service.yaml
```
## STEP 3 — Verify Service
```bash id="08d4ql"
kubectl get svc
```
## STEP 4 — Verify Endpoints

```bash id="4l3qvf"
kubectl get endpoints
```
This confirms the Service successfully discovered Pods.

# 🔑 Key Understanding

I learned how Kubernetes Services provide stable networking and communication between applications inside and outside the cluster.
Key concepts learned:
* Stable networking
* Service discovery
* Load balancing
* Internal DNS
* External exposure
* Labels and Selectors
* Endpoint discovery
Kubernetes Services make application communication reliable even when Pods continuously change internally.


