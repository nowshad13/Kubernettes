# Kubernetes StatefulSet

## Overview
A **StatefulSet** in Kubernetes is a controller used to manage stateful applications. Unlike Deployments, which create interchangeable pods, StatefulSets ensure that:
- Each pod has a **stable network identity** (hostnames remain consistent across restarts).
- Each pod gets its **own persistent storage**.
- Pods are created and deleted in an **ordered sequence**.

### **Use Cases**
✅ Databases (MySQL, PostgreSQL, MongoDB)  
✅ Message Queues (Kafka, RabbitMQ)  
✅ Clustered applications (Elasticsearch, Zookeeper)  

---

## StatefulSet Example
The following example demonstrates how to create a **MySQL StatefulSet**.

### `statefulset.yaml`
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-mysql
spec:
  serviceName: "mysql"
  replicas: 2
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - name: mysql
          image: mysql:latest
          ports:
            - containerPort: 3306
          env:
            - name: MYSQL_ROOT_PASSWORD
              value: admin123
          volumeMounts:
            - name: mysql-storage
              mountPath: /var/lib/mysql
  volumeClaimTemplates:
    - metadata:
        name: mysql-storage
      spec:
        accessModes: ["ReadWriteOnce"]
        resources:
          requests:
            storage: 1Gi
```

### `service.yaml` (Headless Service for StatefulSet)
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
    - port: 3306
      targetPort: 3306
  clusterIP: None
  selector:
    app: mysql
```

---

## Deploying the StatefulSet
Apply the YAML files:
```sh
kubectl apply -f service.yaml
kubectl apply -f statefulset.yaml
```

## Verifying the StatefulSet
Check the status of the StatefulSet:
```sh
kubectl get statefulsets
```

List the pods to ensure they have unique, ordered names:
```sh
kubectl get pods -o wide
```

Describe a specific pod:
```sh
kubectl describe pod my-mysql-0
```

## Scaling the StatefulSet
Increase the number of replicas:
```sh
kubectl scale statefulset my-mysql --replicas=3
```

## Deleting the StatefulSet
Delete without removing persistent data:
```sh
kubectl delete statefulset my-mysql --cascade=orphan
```

To delete the storage (Warning: This will erase data!):
```sh
kubectl delete pvc -l app=mysql
```

---

## StatefulSet vs Deployment
| Feature | Deployment | StatefulSet |
|---------|-----------|------------|
| **Pod Naming** | Random (`pod-xyz`) | Fixed (`pod-0`, `pod-1`, etc.) |
| **Storage** | Shared between pods | Each pod gets its own storage |
| **Scaling** | Any pod can replace another | Follows a strict order |
| **Use Case** | Stateless apps | Databases, clustered systems |

---

## Conclusion
StatefulSets are essential for **stateful applications** that require persistent data, stable network identities, and ordered scaling. They are widely used in databases, message queues, and distributed systems.

For more details, refer to the [Kubernetes StatefulSet Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/).

