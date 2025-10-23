# Kubernetes Archetecture

<img width="4856" height="2656" alt="_C__Users_nowsh_Downloads_Kubernetes%20Archetecture_ svg (1)" src="https://github.com/user-attachments/assets/1edf29c2-0792-4318-9c84-f05bffb88f34" />



Let’s break down **Kubernetes Architecture** in a **clear + practical** way with **real-time examples** so that you can not only understand how it works but also apply it in real DevOps projects.

---

## 🧭 1. What is Kubernetes?

> Kubernetes (K8s) is a **container orchestration platform**.
> It **automates deployment**, **scaling**, and **management** of containerized applications.

✅ Example:
Imagine you created a web app and packaged it as a Docker container.
Running 1 container is easy — but if:

* You want to run **10+ containers** for load balancing
* You want **auto-healing** if a container crashes
* You want **zero-downtime** deployments

👉 Manually doing all this becomes hard.
👉 Kubernetes solves that problem.

---

## 🏗 2. High-level Kubernetes Architecture

A **Kubernetes cluster** has two major parts:

* 🧠 **Control Plane (Master Node)** – The brain 🧠 of the cluster.
* 🧰 **Worker Nodes** – The muscles 💪 that actually run your applications.

### Real-time Analogy:

Think of a **factory**:

* 🧠 **Control Plane** = the **Manager** who plans and schedules work.
* 🧰 **Worker Nodes** = the **Workers** who do the actual job.
* 📦 **Pods** = the **machines** that run inside the worker stations.

---

## 🧠 3. Control Plane Components

These components manage the **cluster state** and **decisions**.

| Component                    | Role                                                                                            | Real Example                                                              |
| ---------------------------- | ----------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **kube-apiserver**           | Acts as the **front door** of the cluster. All communication happens through it (CLI, UI, API). | Like a **Reception Desk** – all orders come here first.                   |
| **etcd**                     | Key-Value **database** that stores the entire cluster state.                                    | Like a **Log Book** that records everything in the factory.               |
| **kube-scheduler**           | Decides **where to place pods** (which node).                                                   | Like the **manager assigning workers to machines**.                       |
| **kube-controller-manager**  | Watches for desired state vs actual state, fixes drifts.                                        | Like a **supervisor** who ensures work is done as planned.                |
| **cloud-controller-manager** | Integrates with cloud providers (optional).                                                     | Like a **vendor liaison** for external services (load balancer, storage). |

✅ Example:
You apply a YAML manifest that says:

> “Run 5 replicas of my frontend pod.”

* `apiserver` receives the request.
* `etcd` stores the desired state.
* `scheduler` picks 5 worker nodes.
* `controller-manager` ensures they are always running.

---

## 🧰 4. Worker Node Components

Worker nodes run your actual application workloads.

| Component                                          | Role                                                                  | Real Example                                                |
| -------------------------------------------------- | --------------------------------------------------------------------- | ----------------------------------------------------------- |
| **kubelet**                                        | Talks to the API server and ensures the pods on its node are healthy. | Like a **worker supervisor** for that machine.              |
| **kube-proxy**                                     | Manages networking and load balancing between pods/services.          | Like the **network cables & switches** connecting machines. |
| **Container runtime** (e.g., containerd or Docker) | Runs the containers.                                                  | Like the **engine** that powers the machines.               |

✅ Example:
If your pod crashes in one node:

* kubelet reports it to control plane
* Controller creates a new pod on another node
* kube-proxy routes traffic to the healthy pods

---

## 🧱 5. Key Kubernetes Objects (with real-time example)

| Object                 | Description                                               | Real Example                                  |
| ---------------------- | --------------------------------------------------------- | --------------------------------------------- |
| **Pod**                | Smallest deployable unit (can have 1 or more containers). | One **web server instance**                   |
| **ReplicaSet**         | Ensures a specified number of pod replicas are running.   | Always keep 5 web servers running             |
| **Deployment**         | Declarative management of pods & ReplicaSets.             | Deploying new app versions with zero downtime |
| **Service**            | Exposes pods internally or externally.                    | Load balancer for your web app                |
| **Ingress**            | Advanced routing (URLs, domains).                         | `https://myapp.com` routes to frontend pods   |
| **ConfigMap / Secret** | Stores config data & sensitive info.                      | DB passwords, environment variables           |
| **Namespace**          | Logical grouping for resources.                           | `dev`, `stage`, `prod` clusters               |

---

## 🌐 6. Real-time Example: Deploying a Web App

Let's say you have a simple Node.js app.

### Step 1: Create a Deployment (frontend.yaml)

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: frontend
        image: nginx:latest
        ports:
        - containerPort: 80
```

### Step 2: Create a Service (service.yaml)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: frontend-service
spec:
  type: LoadBalancer
  selector:
    app: frontend
  ports:
  - port: 80
    targetPort: 80
```

### Step 3: Apply it

```bash
kubectl apply -f frontend.yaml
kubectl apply -f service.yaml
```

✅ What happens:

* Control plane schedules 3 pods on available worker nodes.
* kubelet runs containers on those nodes.
* Service exposes them using kube-proxy.
* You get a **public IP / domain** to access the app.

---

## 🚀 7. Real-world Scenario (Production Example)

Let’s say you’re working on a project for an e-commerce site.

| Component                     | Real Usage                                              |
| ----------------------------- | ------------------------------------------------------- |
| **Namespace**                 | `dev`, `qa`, `prod` environments                        |
| **Deployments**               | Frontend, Backend, Payment, Recommendation              |
| **Horizontal Pod Autoscaler** | Scale backend from 3 to 10 pods when CPU > 70%          |
| **Ingress**                   | `shop.example.com` routes to frontend                   |
| **ConfigMap/Secret**          | Store DB connection strings and API keys                |
| **Persistent Volume**         | Store product images                                    |
| **NetworkPolicy**             | Allow backend to talk to DB only, not frontend directly |

> 💡 If frontend pod crashes, Kubernetes automatically reschedules it on another node.
> 💡 If traffic spikes during a sale, HPA auto-scales pods to handle load.

---

## 🔐 8. Extra Components in Real Setups

| Component                                               | Purpose                                              |
| ------------------------------------------------------- | ---------------------------------------------------- |
| **Helm**                                                | Package manager for Kubernetes (like apt for Ubuntu) |
| **Prometheus & Grafana**                                | Monitoring & alerting                                |
| **Argo CD / Flux**                                      | GitOps continuous delivery                           |
| **Ingress Controller** (e.g., NGINX Ingress Controller) | Manages external traffic                             |
| **Service Mesh** (e.g., Istio)                          | Advanced traffic, security, observability            |

---

## 🧠 9. Summary of Flow

1. You create a **deployment** (desired state).
2. API Server accepts → etcd stores state.
3. Scheduler picks worker nodes.
4. kubelet runs pods.
5. kube-proxy exposes networking.
6. Controller ensures it’s always running as desired.

📊 Real-time:
If 1 node goes down → pods automatically shift to another node → app keeps running.

---

## 🧪 10. Practice Ideas to Learn Fast

| Task                         | What to Learn           |
| ---------------------------- | ----------------------- |
| Deploy NGINX pod             | Basics of pods          |
| Expose it with Service       | Cluster networking      |
| Add Deployment & scale       | Auto-healing            |
| Configure Ingress            | Domain routing          |
| Install Prometheus & Grafana | Monitoring              |
| Install ArgoCD               | GitOps automation       |
| Create a 3-tier app          | Real-world architecture |

---

# Example explanation

Excellent question ✅
This is the **core of understanding Kubernetes architecture** — **“who does what and in which order”** when you say:

👉 *“Hey Kubernetes, create a Deployment with 2 replicas.”*

Let’s break it down like a **movie scene 🎬** — step by step.

---

## 🧠 Scenario:

You run the command:

```bash
kubectl apply -f deployment.yaml
```

Your YAML says:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
spec:
  replicas: 2
  selector:
    matchLabels:
      app: frontend
  template:
    metadata:
      labels:
        app: frontend
    spec:
      containers:
      - name: nginx
        image: nginx:latest
```

✅ You’re asking Kubernetes to create a **Deployment** with 2 replicas (i.e., 2 Pods via ReplicaSet).

---

## 🧭 Step-by-Step Flow: Who Does What 🧭

### 🧑 Step 1: You → `kubectl` → API Server

* You apply the YAML file with `kubectl`.
* `kubectl` is a **client**.
* It **talks to** ➝ `kube-apiserver` using REST API.

🧠 **Component involved:** `kubectl` (client), `kube-apiserver` (control plane)
📌 **Action:** Request is received by the cluster.

---

### 🧠 Step 2: API Server Validates & Stores in etcd

* `kube-apiserver`:

  * Checks YAML for errors (syntax, schema).
  * Checks authentication and RBAC (can you create Deployments?).
  * If valid, it stores the **desired state** of Deployment in **`etcd`** (the cluster database).

🧠 **Component involved:** `kube-apiserver`, `etcd`
📌 **Action:** Desired state saved: *“I want a Deployment named frontend with 2 replicas.”*

---

### 🧭 Step 3: Controller Manager Notices the New Deployment

* `kube-controller-manager` constantly **watches the cluster state**.
* It detects:

  > “There’s a Deployment object but no ReplicaSet for it yet.”
* It **creates a ReplicaSet** object to match the Deployment spec.

🧠 **Component involved:** `kube-controller-manager`
📌 **Action:** Deployment → ReplicaSet created.

---

### 🧭 Step 4: Controller Manager Creates Pods through ReplicaSet

* Once the ReplicaSet is created,
* `ReplicaSet Controller` (part of controller-manager) sees desired replica count = 2.
* It creates 2 **Pod objects**.

🧠 **Component involved:** `kube-controller-manager` (ReplicaSet controller)
📌 **Action:** ReplicaSet → creates Pod definitions in etcd via API server.

---

### 🧭 Step 5: Scheduler Picks Worker Nodes for Pods

* Now there are 2 Pods in “Pending” state — because they’re defined but not placed.
* `kube-scheduler` checks:

  * Available worker nodes
  * Resource requests
  * Node taints/tolerations, affinities
* It assigns Pods to suitable nodes.

🧠 **Component involved:** `kube-scheduler`
📌 **Action:** Pod scheduled to specific nodes.

---

### 🧭 Step 6: Kubelet Runs the Pods on Nodes

* On each selected node, `kubelet` receives:

  > “You need to run this Pod with Nginx container.”
* `kubelet` instructs the **container runtime** (e.g., containerd or Docker) to **pull the image** and start the container.
* It also reports back to API server:
  ✅ “Pod is Running.”

🧠 **Component involved:** `kubelet`, container runtime
📌 **Action:** Pod actually created on node.

---

### 🧭 Step 7: Kube-proxy Configures Networking

* If there’s a Service object, `kube-proxy` updates the routing rules.
* So that traffic to the service goes to the 2 pods.

🧠 **Component involved:** `kube-proxy`
📌 **Action:** Network configured for load balancing.

---

## 🧱 Final State:

| Resource   | Who Created It             | Where Stored        | Action                |
| ---------- | -------------------------- | ------------------- | --------------------- |
| Deployment | You (kubectl) → API Server | etcd                | Desired state         |
| ReplicaSet | Controller Manager         | etcd                | Ensures replica count |
| Pods       | ReplicaSet Controller      | Nodes (via Kubelet) | Actual workload       |

---

## ⚡ Reconciliation Loop (Very Important)

Kubernetes continuously checks:

> “Is the **actual state** of the cluster equal to the **desired state**?”

If one pod dies:

* Kubelet reports failure to API server
* ReplicaSet notices actual = 1, desired = 2
* Controller Manager creates a new Pod
* Scheduler places it on another node
* Kubelet runs it

✅ You don’t need to do anything manually. This is the **self-healing** nature of Kubernetes.

---

## 📊 Real-Life Analogy:

Let’s compare to a factory with 2 machines:

* You (manager) say: “We need 2 packaging machines.”
* Reception (API Server) logs it in system.
* Supervisor (Controller Manager) ensures 2 machines are ordered.
* Logistics (Scheduler) decides which floor they will be installed.
* Technician (Kubelet) installs and starts the machine.
* Electrician (Kube-proxy) wires the network to them.
* If one machine breaks → Supervisor orders a new one immediately.

---

## 🕸️ Bonus: YAML to Running Container — Full Chain

```
kubectl → kube-apiserver → etcd
      ↓
kube-controller-manager (Deployment → ReplicaSet → Pod)
      ↓
kube-scheduler (assign nodes)
      ↓
kubelet (runs containers)
      ↓
kube-proxy (networking)
```

---

✅ **In short:**

* You only tell Kubernetes *what* you want (desired state).
* Kubernetes components work together to make it happen automatically.
* If something breaks, Kubernetes fixes it to match the desired state.

---

