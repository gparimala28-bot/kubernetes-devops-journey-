# 🚀 TLS-Based Kubernetes Ingress using NGINX

# 📌 Project Overview

This project demonstrates secure HTTPS communication in Kubernetes using NGINX Ingress, self-signed TLS certificates, and Kubernetes TLS Secrets.

The project focuses on understanding:
- TLS/HTTPS configuration
- Kubernetes Ingress routing
- TLS Secret management
- Secure application exposure inside Kubernetes

# 🛠️ Tools & Technologies Used

- Kubernetes
- Minikube
- NGINX Ingress Controller
- OpenSSL
- TLS/HTTPS
- Kubernetes Secrets

# 🏗️ TLS Ingress Architecture

```text
Browser
   ↓ HTTPS Request
NGINX Ingress Controller
   ↓
TLS Secret (tls.crt + tls.key)
   ↓
Kubernetes Service
   ↓
Flask Application Pod
```

# 🔧 What I Implemented

- Created Kubernetes Deployment and Service
- Installed and configured NGINX Ingress Controller
- Generated self-signed TLS certificates using OpenSSL
- Created Kubernetes TLS Secret
- Configured HTTPS Ingress routing
- Added SAN (Subject Alternative Name) support using openssl.cnf
- Configured local domain mapping using hosts file
- Tested secure HTTPS communication
- Troubleshooted TLS certificate validation issues

# ▶️ Steps to Run Project

## 1. Start Minikube
```bash
minikube start
```
Enable ingress:

```bash
minikube addons enable ingress
```
## 2. Apply Kubernetes Resources
```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```
## 3. Generate TLS Certificate
```bash
openssl req -x509 -nodes -days 365 \
-newkey rsa:2048 \
-keyout tls.key \
-out tls.crt \
-config openssl.cnf \
-extensions req_ext
```
## 4. Create TLS Secret
```bash
kubectl create secret tls app-secret \
  --cert=tls.crt \
  --key=tls.key
```
## 5. Apply Ingress Configuration
```bash
kubectl apply -f ingress.yaml
```
## 6. Update Hosts File
Add:
```text
<MINIKUBE-IP> myflaskapp.in
```
## 7. Access Application
```text
https://myflaskapp.in
```
# ⚠️ Errors Faced & Fixes
## 1. TLS Certificate Validation Error
Fix:
- Added SAN (Subject Alternative Name) support using openssl.cnf
- Regenerated TLS certificate
- Recreated Kubernetes TLS Secret

## 2. HTTPS Not Working Initially
Fix:
- Restarted NGINX Ingress Controller
- Verified Ingress and Secret configuration

# 🔑 Key Learnings

- Understood TLS and HTTPS workflow in Kubernetes
- Learned SSL/TLS certificate generation using OpenSSL
- Learned Kubernetes TLS Secret management
- Understood NGINX Ingress HTTPS configuration
- Learned SAN-based certificate validation
- Gained hands-on experience with secure Kubernetes networking
- Improved troubleshooting of TLS-related issues

# 🚀 Final Outcome

This project helped me understand how HTTPS security is implemented in Kubernetes environments using TLS, Kubernetes Secrets, and NGINX Ingress Controller.

It also improved my understanding of secure traffic routing, certificate management, and Kubernetes networking fundamentals.

