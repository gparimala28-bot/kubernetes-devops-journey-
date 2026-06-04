# Kubernetes Probes

## 🧠 What Are Kubernetes Probes?

Kubernetes Probes are health checks performed by the Kubelet on containers running inside Pods.

They help Kubernetes monitor application status and take actions when problems occur.

## 🎯 Why Probes Matter

Kubernetes Probes provide several important benefits:

- Enable automatic recovery from failures.
- Support zero-downtime deployments.
- Ensure traffic is sent only to healthy applications.
- Improve application reliability and availability.
- Handle issues such as deadlocks, startup delays, and temporary service failures.

## 🚀 Types of Probes

 1. Liveness Probe-Checks whether the application is still running correctly.

 #### When to Use

   - Application becomes unresponsive while the process is still running.
   - Automatic recovery is required.

 2. Readiness Probe-Checks whether the application is ready to accept traffic.

 #### When to Use

   - Application requires startup time.
   - Application temporarily loses access to dependencies such as databases.

3. Startup Probe-Handles applications with slow startup times.

   Key Benefit-Liveness and Readiness probes remain disabled until startup completes successfully.

 #### When to Use

   - Long initialization processes.
   - Database migrations.
   - Large data loading operations.

## ⚙️ Common Probe Configuration Parameters

All Kubernetes probes (`livenessProbe`, `readinessProbe`, and `startupProbe`) use a set of common configuration settings that control when and how health checks are performed.

1. initialDelaySeconds(int, default:0)-The number of seconds Kubernetes waits after the container starts before running the first probe.

2. periodSeconds(int, default:10)-How often Kubernetes performs the health check.

3. timeoutSeconds(int, default:1)-The maximum time Kubernetes waits for a probe response.

- If the application does not respond within this time, the probe is marked as failed.

4. successThreshold(int, default:1)-The number of consecutive successful checks required for a probe to be considered successful after it has previously failed.

5. failureThreshold(int, default:3)-The number of consecutive failures allowed before Kubernetes takes action.

## 🔍 Probe Handlers

Kubernetes provides different ways to perform health checks, known as Probe Handlers.

1. exec Action-Runs a command directly inside the container to verify the application's health.

#### Common Use Cases

   - Checking internal application status.
   - Verifying the existence of files or directories.
   - Running custom health-check scripts.

#### Example
```yaml
exec:
  command:
    - cat
    - /tmp/healthy
```

In this example, Kubernetes checks whether the file `/tmp/healthy` exists and can be read.

2. httpGet Action-Sends an HTTP GET request to a specific endpoint exposed by the application.

#### Common Use Cases

  - Web applications
  - REST APIs
  - Services exposing health-check endpoints

#### Example

```yaml
httpGet:
  path: /healthz
  port: 8080
  # host: Defaults to Pod IP
  # scheme: HTTP or HTTPS (default: HTTP)
  # httpHeaders: Optional custom headers
```
In this example, Kubernetes sends a request to `/healthz` on port `8080` and expects a successful response.

3. tcpSocket Action-Attempts to open a TCP connection to a specific port.

#### Common Use Cases

   - Databases
   - Message brokers
   - Applications that expose services through a TCP port

#### Example
```yaml
tcpSocket:
  port: 3306
```
In this example, Kubernetes checks whether a service is listening on port `3306` (commonly used by MySQL).

## 🧱 Comprehensive Example

This example demonstrates a single Pod using all three probe types with different handlers for illustrative purposes.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: full-probe-example
  labels:
    app: my-complex-app

spec:
  containers:
    - name: main-container
      image: my-custom-app:latest

      ports:
        - containerPort: 8080
        - containerPort: 50051

      startupProbe:
        httpGet:
          path: /startup-check
          port: 8080
        initialDelaySeconds: 0
        periodSeconds: 10
        failureThreshold: 60
        timeoutSeconds: 5

      livenessProbe:
        tcpSocket:
          port: 8080
        initialDelaySeconds: 20
        periodSeconds: 15
        timeoutSeconds: 2
        failureThreshold: 3

      readinessProbe:
        exec:
          command:
            - /bin/sh
            - -c
            - "pgrep my-app-process && curl -f http://localhost:8080/app-status"
        initialDelaySeconds: 10
        periodSeconds: 5
        timeoutSeconds: 3
        failureThreshold: 2
```

## 🔑 Key Learnings

- Kubernetes Probes monitor application health.
- Liveness Probes detect application failures and trigger restarts.
- Readiness Probes control whether a Pod receives traffic.
- Startup Probes protect slow-starting applications.
- Readiness Probe failures do not restart containers.
- Startup Probes disable Liveness and Readiness checks until startup succeeds.
- Probe configuration parameters control timing and failure behavior.
- Proper probe configuration improves reliability, availability, and deployment stability.

## 💡 Final Insight

Kubernetes Probes act as the health monitoring system for containerized applications.

By combining Liveness, Readiness, and Startup Probes, Kubernetes can automatically recover from failures, prevent traffic from reaching unhealthy applications, and safely handle slow-starting workloads, resulting in highly available and resilient deployments.