![Step 1](Screenshots/lap17.png) 

# Lab 17: Pod Resource Management with CPU and Memory Requests and Limits

## Objective

The objective of this lab is to demonstrate how to manage CPU and memory resources for Kubernetes Pods by configuring **resource requests and limits** in a Deployment.
This ensures fair resource allocation, prevents Pods from over-consuming node resources, and improves cluster stability.

---

## Lab Environment

* Kubernetes Cluster (kubeadm-based lab environment)
* Node.js application running inside a Pod
* Deployment controller
* Worker node with taint `node=worker:NoSchedule`

---

## Deployment Configuration

The following Deployment was used to run the Node.js application with CPU and memory requests and limits applied:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nodejs-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: nodejs
  template:
    metadata:
      labels:
        app: nodejs
    spec:
      tolerations:
        - key: "node"
          operator: "Equal"
          value: "worker"
          effect: "NoSchedule"
      containers:
        - name: nodejs
          image: abdelrahmannayf/ivolve-app:latest
          ports:
            - containerPort: 3000
          env:
            - name: DB_HOST
              value: mysql
            - name: DB_USER
              value: root
            - name: DB_PASSWORD
              value: password
          resources:
            requests:
              cpu: "1"
              memory: "1Gi"
            limits:
              cpu: "2"
              memory: "2Gi"
```

---

## Resource Configuration Explanation

### Resource Requests

* **CPU Request:** `1`
* **Memory Request:** `1Gi`

These values represent the **minimum guaranteed resources** required by the Pod.
Kubernetes uses these values during scheduling to place the Pod on a suitable node.

---

### Resource Limits

* **CPU Limit:** `2`
* **Memory Limit:** `2Gi`

These values represent the **maximum resources** the Pod is allowed to consume:

* Exceeding CPU limit results in throttling.
* Exceeding memory limit results in Pod termination (OOMKilled).

---

## Verification Steps

### 1. Verify Deployment and Pod Status

```bash
kubectl get deployments
kubectl get pods
```

---

### 2. Verify Resource Requests and Limits

```bash
kubectl describe pod <pod-name>
```

Expected output:

```
Requests:
  cpu:     1
  memory:  1Gi
Limits:
  cpu:     2
  memory:  2Gi
```

---

### 3. Monitor Resource Usage

```bash
kubectl top pod
```

> **Note:** In this lab environment, the Metrics API was not available because `metrics-server` is not supported or not fully configured in the cluster.
> Therefore, real-time resource monitoring using `kubectl top` could not be performed.

---

## QoS Class

The Pod was assigned a **Burstable** Quality of Service (QoS) class because:

* Resource requests are lower than resource limits.

---

## Conclusion

In this lab:

* CPU and memory resource requests and limits were successfully applied to a Pod.
* Kubernetes scheduling and resource management behavior was observed.
* Resource configuration was verified using `kubectl describe pod`.
* The lab demonstrates best practices for managing resources in production-grade Kubernetes environments.

