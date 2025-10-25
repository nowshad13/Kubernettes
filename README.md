# Kubernetes Archetecture

<img width="4856" height="2656" alt="_C__Users_nowsh_Downloads_Kubernetes%20Archetecture_ svg (1)" src="https://github.com/user-attachments/assets/1edf29c2-0792-4318-9c84-f05bffb88f34" />


### How the Architecture Works

When a user writes a YAML file to create Kubernetes resources like Pods, Deployments, ReplicaSets, etc., and runs `kubectl apply -f <filename>.yaml`, the `kubectl` command communicates with the **Kube API Server**, which is the first entry point to the control plane. The API Server reads the YAML, understands the user’s desired state, and stores it in **etcd**, a key-value store that maintains the complete cluster state. The **Controller Manager** continuously communicates with the API Server to monitor changes in the desired state. When it detects a new Deployment, the Deployment Controller inside it creates a ReplicaSet object, and the ReplicaSet Controller ensures the required number of Pods are created. At this point, the Pods are in a **Pending** state because they have not yet been assigned to a node. The **Scheduler** then observes these unscheduled Pods through the API Server, evaluates available nodes based on CPU, memory, and other constraints, and assigns each Pod to a suitable node, updating the Pod specification in the API Server. Once the Pod is assigned, the **kubelet** on that node pulls the required container image using the container runtime, creates the container, and updates the Pod’s status to **Running**. Meanwhile, **kube-proxy** configures network routing so that Services and other Pods can communicate with the newly running Pod, completing the process from YAML definition to a fully operational Pod in the cluster.


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


# PORTS


<img width="2233" height="3239" alt="_C__Users_nowsh_Downloads_Kubernetes%20Ports svg" src="https://github.com/user-attachments/assets/07b6d81a-6e9d-4b59-9aaf-6ccc66b43ac8" />


## ☸️ Step 4: Enter Kubernetes — Port Concepts

In Kubernetes, there are **3 levels of ports** you must understand clearly:

| Type              | Purpose                                              | Scope     | Example |
| ----------------- | ---------------------------------------------------- | --------- | ------- |
| **ContainerPort** | Inside Pod                                           | Pod only  | 8080    |
| **TargetPort**    | What the Service forwards traffic to inside Pod      | Pod level | 8080    |
| **NodePort**      | Port opened on Node to access the service externally | Node      | 30080   |

---

## 🧪 Step 5: K8s Practical Example

### 1. Pod Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 2
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 8080
```

✅ Pod listens on port `8080`.

---

### 2. Service (NodePort)

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
    - port: 80         # Service Port (ClusterIP)
      targetPort: 8080 # Container Port
      nodePort: 30080  # NodePort (External)
```

* `targetPort` → 8080 (inside pod)
* `port` → 80 (inside cluster)
* `nodePort` → 30080 (external node)

🧭 **ASCII Network Flow Diagram**

```
           Client (Browser)
           http://<NodeIP>:30080
                     │
                     ▼
        ┌───────────────────────────┐
        │   Kubernetes Node         │
        │ (NodePort: 30080 open)    │
        └─────────┬─────────────────┘
                  │
           Service (ClusterIP:80)
                  │
           Load balances traffic
        ┌─────────┴──────────┐
        ▼                    ▼
   Pod 1 (8080)         Pod 2 (8080)
```

✅ Traffic Flow:

1. Client hits `http://<NodeIP>:30080`
2. NodePort forwards to Service Port 80
3. Service forwards to targetPort 8080
4. Load balances between multiple Pods

👉 Now you can scale the Pods without changing the NodePort.

---

## ☁️ Step 6: When Using LoadBalancer

If you deploy:

```yaml
type: LoadBalancer
```

Kubernetes (on cloud like Amazon Web Services) provisions an external IP and directly maps to the service.

```
Public IP (LoadBalancer)
        │
        ▼
NodePort (30080)
        │
Service (80)
        │
Pods (8080)
```

---

## 📌 Bonus: Port Summary Table

| Layer       | Docker   | Kubernetes               | Description                  |
| ----------- | -------- | ------------------------ | ---------------------------- |
| App         | 8080     | ContainerPort (8080)     | App listens inside container |
| Host        | 80       | NodePort (30080)         | Exposes to outside world     |
| Internal LB | N/A      | ClusterIP (80)           | Internal routing             |
| External LB | Optional | LoadBalancer (Public IP) | Optional external IP         |

---

## ⚔️ Real World Example — Deploying a Website

Imagine you want to host your portfolio site on Kubernetes:

1. App runs on port 8080 inside container
2. Service exposes NodePort 30080
3. Ingress or LoadBalancer assigns domain `aravindh.xyz`
4. When user types `https://aravindh.xyz`, the traffic flow:

```
Client → DNS → LoadBalancer (80/443) 
       → NodePort 30080 
       → ClusterIP 80 
       → Pod 8080
```

---

## 🧪 Practical Task for You

👉 **Try this yourself in your Kubernetes playground:**

```bash
kubectl create deployment myapp --image=nginx --port=8080
kubectl expose deployment myapp --port=80 --target-port=8080 --type=NodePort
kubectl get svc
```

Then:

* Copy the NodePort from `kubectl get svc`
* Visit `http://<NODE_IP>:<NODE_PORT>` in browser

✅ You’ll see the NGINX welcome page.

---

## 📚 Key Takeaways

* **Docker:** Host Port ↔ Container Port (NAT via bridge)
* **Kubernetes:** NodePort ↔ Service Port ↔ TargetPort (Pod)
* NodePort gives external access; ClusterIP is internal; LoadBalancer gives cloud IP.
* Host port in Docker must be unique, but multiple containers can share container port.
* Kubernetes abstracts this with Services and doesn’t tie Pod IPs to host ports directly.

---

Would you like me to now **draw this with Ingress + custom domain** (`aravindh.xyz`) as the next level? (this will show how real apps are exposed in production 🚀)





Of course 🔥 — Step 6 is **where real-world apps** usually live.
You already understood `NodePort`… now we’ll **level up to `LoadBalancer` and `Ingress`**, which is how production traffic enters your Kubernetes cluster.

Let’s go slowly and clearly 👇

---

## ☁️ STEP 6: Kubernetes Service — LoadBalancer Type

In cloud environments (like Amazon Web Services, Microsoft Azure, or Google Cloud), when you create a **LoadBalancer Service**, the cloud provider automatically gives you a **public IP** or DNS name.

🧾 Example Service Manifest:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: LoadBalancer       # 👈 Key difference
  selector:
    app: webapp
  ports:
  - port: 80               # ClusterIP port
    targetPort: 8080       # Container port
    nodePort: 30080        # Auto-assigned or manually set (NodePort layer)
```

✅ After applying this:

```bash
kubectl apply -f service.yaml
kubectl get svc
```

You’ll see something like:

```
NAME               TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
webapp-service     LoadBalancer   10.96.48.120   52.23.45.11      80:30080/TCP   1m
```

Here:

* **Cluster IP:** 10.96.48.120 (internal)
* **External IP:** 52.23.45.11 (public)
* **NodePort:** 30080 (still exists under the hood)
* **TargetPort:** 8080 (inside pod)

---

## 📡 Traffic Flow (LoadBalancer)

📊 **ASCII Diagram:**

```
    [ Client Browser ]
         │  http://52.23.45.11
         ▼
┌───────────────────────────────┐
│ LoadBalancer (Cloud Provider) │
│   Public IP: 52.23.45.11      │
└───────────┬───────────────────┘
            │ forwards to NodePort
            ▼
┌───────────────────────────┐
│ Kubernetes Node (Worker)  │
│ NodePort 30080 open       │
└───────────┬───────────────┘
            │ forwards to Service
            ▼
┌───────────────────────────┐
│  ClusterIP Service (80)   │
│ Load balances traffic     │
└───────────┬───────────────┘
            │
   ┌────────┴────────┐
   ▼                 ▼
[ Pod 1:8080 ]   [ Pod 2:8080 ]
```

**Step-by-step flow of traffic:**

1. 🌐 User types `http://52.23.45.11` in the browser.
2. 🚀 LoadBalancer (from cloud) receives the request.
3. ⛓️ It forwards the traffic to the Kubernetes NodePort (30080).
4. 🌀 NodePort forwards it to the Service on ClusterIP (port 80).
5. ⚖️ Service load balances to one of the pods on port 8080.
6. 🧠 Pod responds → Service → NodePort → LoadBalancer → Client.

✅ This allows you to **avoid remembering Node IPs or ports**.
You just use the **LoadBalancer public IP**.

---

## 🌍 STEP 7: Add Domain + Ingress (Production Way)

In many real setups, we don’t want users to hit the IP directly.
We want:

```
http://aravindh.xyz
```

instead of:

```
http://52.23.45.11:80
```

This is where **Ingress** comes in.

---

### ✨ What is Ingress?

* Ingress = Smart traffic router at cluster edge.
* It lets you use **hostnames / paths** like:

  * `aravindh.xyz` → web frontend
  * `api.aravindh.xyz` → backend API
  * `grafana.aravindh.xyz` → monitoring dashboard
* It works with an **Ingress Controller** (e.g., NGINX Ingress Controller).

---

### 🧾 Ingress Manifest Example

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: webapp-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: aravindh.xyz
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: webapp-service
            port:
              number: 80
```

✅ Then:

1. Create a **DNS A record**:

   ```
   aravindh.xyz → 52.23.45.11
   ```

   (the public IP of the LoadBalancer that fronts the Ingress Controller)

2. When someone types `http://aravindh.xyz`, the flow is:

---

## 🧭 Ingress Traffic Flow

📊 **ASCII Diagram:**

```
 Client: http://aravindh.xyz
             │
             ▼
 ┌──────────────────────────────┐
 │   DNS (A Record)             │
 │ aravindh.xyz → 52.23.45.11   │
 └────────────┬─────────────────┘
              ▼
 ┌──────────────────────────────┐
 │ LoadBalancer (Public IP)     │
 │ Routes to Ingress Controller │
 └────────────┬─────────────────┘
              ▼
 ┌──────────────────────────────┐
 │ Ingress Resource             │
 │ Matches host/path rules      │
 └────────────┬─────────────────┘
              ▼
 ┌──────────────────────────────┐
 │ ClusterIP Service (80)       │
 │ Selects Pods                 │
 └────────────┬─────────────────┘
              ▼
      [ Pod 1:8080 ] [ Pod 2:8080 ]
```

**Step-by-step:**

1. 🌍 User enters `aravindh.xyz`
2. 🧭 DNS resolves `aravindh.xyz` → LoadBalancer IP
3. 🚀 LoadBalancer sends traffic to Ingress Controller
4. 📜 Ingress resource checks rules and forwards to the right service
5. 🧭 Service sends traffic to Pods
6. 🧠 Response goes back the same route

---

## ⚡ Why LoadBalancer + Ingress Is Best in Real World:

| Feature        | NodePort        | LoadBalancer            | Ingress                            |
| -------------- | --------------- | ----------------------- | ---------------------------------- |
| External IP    | Node IP         | Cloud LB Public IP      | Domain (DNS)                       |
| Port Exposure  | High ports only | 80/443 supported easily | 80/443, multiple routes            |
| Scaling        | Manual routing  | Auto scaling            | Advanced routing + hostnames       |
| Domain Support | ❌               | ⚠️ Limited              | ✅ Full                             |
| SSL/TLS        | Manual setup    | Easier                  | Automatic (with cert-manager etc.) |

👉 In most **production clusters**, NodePort is rarely exposed directly.
We use **LoadBalancer + Ingress** with a DNS record and SSL.

---

## 🧪 Practical Command for You (AWS or local with minikube)

```bash
# Step 1: Deploy Ingress Controller (e.g., nginx)
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml

# Step 2: Create deployment and service (already done)
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml

# Step 3: Create Ingress
kubectl apply -f ingress.yaml

# Step 4: Get LB IP
kubectl get svc -n ingress-nginx

# Step 5: Point your domain DNS to LB IP
# Step 6: Access http://aravindh.xyz
```

---

## 🏁 Summary of Port Layers in Kubernetes (LoadBalancer + Ingress)

| Layer            | Example Value   | Description                             |
| ---------------- | --------------- | --------------------------------------- |
| ContainerPort    | 8080            | Inside Pod — app listens here           |
| TargetPort       | 8080            | Service forwards traffic to this        |
| ClusterIP Port   | 80              | Internal service routing                |
| NodePort         | 30080           | Node layer port (auto for LoadBalancer) |
| LoadBalancer     | 52.23.45.11:80  | Public IP from cloud                    |
| Ingress + Domain | aravindh.xyz:80 | DNS + HTTP routing at Layer 7           |

✅ Now you understand not just Docker ports…
but the **entire Kubernetes port routing stack** like a pro 🔥

---

Would you like me to now **set this up practically in your Kubernetes playground (e.g. KillerKoda)** so you can test LoadBalancer and Ingress step by step? (we can use `nip.io` domain for free if you don’t have a domain) 🌐💪


