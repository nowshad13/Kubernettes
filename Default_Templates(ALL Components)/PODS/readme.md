# Kubernetes Pod and Service

## Overview
A **Pod** is the smallest deployable unit in Kubernetes. It encapsulates a container (or multiple containers) that share storage and networking. This example deploys a **single Nginx Pod** and exposes it using a **NodePort Service**.

## Files

### `pod.yaml` (Pod and Service Configuration)
```yaml
--- # Pod
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  labels:
    app: nginx
    env: dev
spec:
  containers:
    - name: mycon1
      image: aravindh146/nginx-gym:1.0
      ports:
        - containerPort: 80
          protocol: TCP
--- # Service
apiVersion: v1
kind: Service
metadata:
  name: mypod-service
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      protocol: TCP
  selector:
    app: nginx
```

## Applying the Configuration
To deploy the Pod and Service, run:
```sh
kubectl apply -f pod.yaml
```

## Verifying the Deployment
Check the status of the Pod:
```sh
kubectl get pods
```

Check the status of the Service:
```sh
kubectl get svc
```

Describe the Pod for more details:
```sh
kubectl describe pod mypod
```

## Accessing the Pod
Since the service type is **NodePort**, retrieve the assigned port using:
```sh
kubectl get svc mypod-service
```
Then, access the Pod using:
```sh
http://<Node-IP>:<NodePort>
```

## Deleting the Pod and Service
To remove the resources, use:
```sh
kubectl delete -f pod.yaml
```


---

# 🧾 README – Kubernetes Pod Concepts Explained

This document explains the most important fields used inside a Kubernetes Pod YAML file —
`resources`, `livenessProbe`, `readinessProbe`, and `restartPolicy`.
These are essential for keeping your applications **stable**, **healthy**, and **well-managed** in production.

---

## ⚙️ 1. RESOURCES

### 💡 What it means

`resources` define how much **CPU** and **Memory (RAM)** your container needs.

There are two parts:

```yaml
resources:
  requests:
    cpu: "100m"
    memory: "128Mi"
  limits:
    cpu: "500m"
    memory: "256Mi"
```

| Field      | Meaning                                                           |
| ---------- | ----------------------------------------------------------------- |
| `requests` | The **minimum** resources your container needs to start properly. |
| `limits`   | The **maximum** resources your container can use.                 |

---

### 🧩 Real-time Example

Imagine your laptop has 4 GB RAM.
You open **Chrome** and **VS Code**.

You can say:

* Chrome should get **1 GB minimum** (request)
* But it should **not use more than 2 GB** (limit)

This helps Kubernetes **fairly share resources** among all pods in the cluster.

---

### 🏢 Industry Practice

Always define both **requests** and **limits** in production YAMLs.
It prevents one container from consuming too many resources and crashing others.

---

## 💓 2. LIVENESS PROBE

### 💡 What it means

A **liveness probe** checks if your container is **alive** or **stuck**.

If the app stops responding, Kubernetes will **kill the container** and **start a new one** automatically.

---

### ✅ Example:

```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 10
  periodSeconds: 5
```

| Field                 | Meaning                                                   |
| --------------------- | --------------------------------------------------------- |
| `httpGet`             | Checks health by making an HTTP request to the container. |
| `initialDelaySeconds` | Wait 10 seconds before starting the first check.          |
| `periodSeconds`       | Run this check every 5 seconds.                           |

---

### 🧩 Real-time Example

Think of Kubernetes as a **nurse** checking if a patient is alive.
If the patient doesn’t respond, the nurse restarts life support 💉

Similarly, if your app freezes or crashes, Kubernetes restarts it automatically —
this is called **self-healing**.

---

## 🚦 3. READINESS PROBE

### 💡 What it means

A **readiness probe** checks if the container is **ready to serve traffic**.

Even if a container is running, it might still be **loading configurations** or **connecting to a database**.
This probe tells Kubernetes **not to send traffic** until the app is ready.

---

### ✅ Example:

```yaml
readinessProbe:
  httpGet:
    path: /
    port: 80
  initialDelaySeconds: 5
  periodSeconds: 3
```

---

### 🧩 Real-time Example

Imagine a restaurant:

* The kitchen (container) is open but still **preparing food**.
* The waiter (Kubernetes Service) should **not send customers** yet.
* Once the cook says “I’m ready,” the waiter starts serving orders.

That’s exactly what **readiness probe** does 🍛

---

### 🏢 Industry Practice

Always define **readiness probes** for production web apps.
They help avoid downtime during app startup or updates.

---

## 🔁 4. RESTART POLICY

### 💡 What it means

Defines how Kubernetes should behave if a container **crashes**.

---

### ✅ Example:

```yaml
restartPolicy: Always
```

| Value       | Meaning                                                              |
| ----------- | -------------------------------------------------------------------- |
| `Always`    | Restart the container every time it crashes (default for most pods). |
| `OnFailure` | Restart only if it fails (useful for batch jobs).                    |
| `Never`     | Do not restart even if it fails.                                     |

---

### 🧩 Real-time Example

Imagine your **Chrome browser** crashes:

* If it **automatically reopens** → `Always`
* If it **reopens only when you click reopen** → `OnFailure`
* If it **never reopens** → `Never`

---

### 🏢 Industry Practice

For web apps, `Always` is used so that your service is always available.

---

## 🧩 Summary Table

| Concept            | Meaning                                  | Real-world Analogy                     |
| ------------------ | ---------------------------------------- | -------------------------------------- |
| **resources**      | Control CPU and memory usage             | Allocating RAM for apps on your laptop |
| **livenessProbe**  | Checks if container is alive             | Nurse checking if a patient is alive   |
| **readinessProbe** | Checks if container is ready for traffic | Waiter checking if kitchen is ready    |
| **restartPolicy**  | What to do if app crashes                | Auto-reopen apps after crash           |

---

## ✅ Example Pod YAML with All Concepts

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
    - name: nginx-container
      image: nginx:1.27
      ports:
        - containerPort: 80
      resources:
        requests:
          cpu: "100m"
          memory: "128Mi"
        limits:
          cpu: "500m"
          memory: "256Mi"
      livenessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 10
        periodSeconds: 5
      readinessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 5
        periodSeconds: 3
  restartPolicy: Always
```

---

## 🧠 Quick Recap

* `resources`: Define how much power the container can use
* `livenessProbe`: Checks if it’s still working
* `readinessProbe`: Checks if it’s ready for traffic
* `restartPolicy`: Tells what to do when it fails

---



