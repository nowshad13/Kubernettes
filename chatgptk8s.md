Here’s the corrected and simplified information about Kubernetes:

---

### **What is Kubernetes?**
- Kubernetes (K8s) is an open-source container orchestration tool developed by Google. It manages and scales containers automatically.
- Containers (like those from Docker) allow applications to run in isolated environments. However, managing these containers at scale can be challenging.
- Kubernetes solves this problem by automating tasks such as scaling, load balancing, and maintaining the desired state of your applications.

---

### **Why Kubernetes?**
- Imagine streaming services like JioCinema during IPL matches: 
  - When viewers increase, more containers are needed to handle the load.
  - Kubernetes automatically scales the containers up or down based on demand, without manual intervention.

---

### **History of Kubernetes**
- Before Kubernetes, Google used in-house orchestration tools like *Borg* and *Omega* for managing containers.
- Google developed Kubernetes as an open-source project using the Go programming language.
- Today, Kubernetes supports all types of container runtimes, not just Docker.

---

### **Key Features**
- **Pods:** The smallest deployable unit in Kubernetes. A pod can contain one or more containers.
- **Replica Sets:** Ensure a specified number of pod replicas are always running.
- **Services:** Enable communication between parts of an application (internal) or with the outside world (external).
- **Persistent Volumes:** Manage storage for containers.
- **Self-Healing:** Automatically restarts failed containers or replaces them if nodes go down.

---

### **Cluster Architecture**
A Kubernetes cluster consists of:
1. **Master Node (Control Plane):**
   - Manages the cluster and handles scheduling, API communication, and overall health.
2. **Worker Nodes:**
   - Run the application workloads (pods).

---

### **Control Plane Components** (Master Node)
1. **API Server:**
   - Receives and processes requests from `kubectl` or other tools.
2. **etcd:**
   - Stores the cluster’s configuration data in a key-value store.
3. **Scheduler:**
   - Assigns pods to nodes based on resource requirements.
4. **Controller Manager:**
   - Ensures the desired state (e.g., number of pods) is maintained.

---

### **Worker Node Components**
1. **Kubelet:**
   - An agent that ensures containers run as instructed by the control plane.
2. **Container Runtime:**
   - The software to run containers (e.g., Docker, containerd, CRI-O).
3. **Kube-Proxy:**
   - Manages networking for pods, enabling communication between pods and the outside world.

---

### **Kubernetes Objects**
- **Pods:** 
  - Contain one or more containers.
  - Share the same network namespace and storage.
  - Automatically restart containers if they fail (self-healing).
- **Replica Set:** 
  - Ensures a fixed number of pod replicas are running.
- **Deployment:**
  - Manages pods and replica sets, allowing for updates, rollbacks, and zero-downtime deployments.
- **Labels:**
  - Key-value pairs attached to objects for identification and querying.
- **Services:**
  - Expose pods to internal components or external users.

---

### **Interfaces in Kubernetes**
1. **CRI (Container Runtime Interface):**
   - Allows Kubernetes to communicate with container runtimes like Docker, containerd, or CRI-O.
   - Docker is supported via external tools like `cri-dockerd` (from Kubernetes version 1.24 onward).
2. **CNI (Container Network Interface):**
   - Manages networking for Kubernetes clusters.
   - Examples: Calico, Flannel, Weave Net.
3. **CSI (Container Storage Interface):**
   - Handles storage for Kubernetes clusters.

---

### **Key Concepts**
1. **Self-Healing:** Automatically replaces failed pods or containers.
2. **Scaling:** Adds or removes pods based on demand.
3. **Load Balancing:** Distributes traffic across pods to ensure availability.
4. **Resource Management:** Schedules workloads on nodes based on their resource availability.

---

### **Summary**
- Kubernetes simplifies container management, scaling, and networking.
- It doesn’t come with built-in defaults for container runtime (CRI), networking (CNI), or storage (CSI)—you choose these based on your needs.
- It’s not that Kubernetes stopped supporting Docker, but it no longer includes Docker’s runtime by default.

---

If you need further clarification or examples, let me know!


```bash
#!/bin/bash
curl -fsSL https://get.docker.com -o install-docker.sh
sudo sh install-docker.sh
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.32/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.15/cri-dockerd_0.3.15.3-0.ubuntu-jammy_amd64.deb
sudo dpkg -i cri-dockerd_0.3.15.3-0.ubuntu-jammy_amd64.deb
```