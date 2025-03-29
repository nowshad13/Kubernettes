# Headless Service in Kubernetes

## Overview
A **Headless Service** is a Kubernetes Service that **does not assign a cluster IP**. Instead, it allows direct access to individual pod IPs, making it useful for Stateful applications like databases, distributed systems, and DNS-based discovery.

## Key Features
- **No Cluster IP** (`clusterIP: None`)
- **Used with StatefulSets**
- **DNS-based Pod Discovery**

## Example Setup
### 1️⃣ **Create the Headless Service and StatefulSet**
```sh
kubectl apply -f headless-service.yaml
