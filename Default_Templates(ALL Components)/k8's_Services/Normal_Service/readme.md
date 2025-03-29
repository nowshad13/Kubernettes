# Kubernetes Service

## Overview
A **Service** in Kubernetes provides a stable IP and DNS name for accessing a set of pods. It enables communication between different components within a cluster.

## Key Features
- **Provides a stable IP** for pod communication.
- **Load balances traffic** across multiple pod replicas.
- **Allows communication via ClusterIP, NodePort, or LoadBalancer.**

## Example Setup
### 1️⃣ **Deploy the Service and Deployment**
```sh
kubectl apply -f service.yaml
