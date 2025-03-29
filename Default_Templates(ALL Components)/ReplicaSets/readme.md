# Kubernetes ReplicaSet

## Overview
A **ReplicaSet** ensures that a specified number of identical pods are running at all times in a Kubernetes cluster. It replaces failed pods and maintains the desired state automatically.

## Use Cases
- Ensuring high availability by running multiple instances of an application.
- Automatically recovering pods if they fail.
- Load balancing traffic across multiple replicas.

## Creating a ReplicaSet
Below is an example **ReplicaSet YAML** file that deploys an application with two replicas.

### `replicaset.yaml`
```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: demo-rs
  labels:
    app: demoapp
    env: dev
spec:
  minReadySeconds: 5
  replicas: 2
  selector:
    matchLabels:
      app: demoapp
  template:
    metadata:
      labels:
        app: demoapp
    spec:
      containers:
        - name: con-1
          image: aravindh146/dbconnectiontestapp:latest
          ports:
            - containerPort: 8080
              protocol: TCP
          readinessProbe:
            failureThreshold: 1
            httpGet:
              path: /
              port: 8080
            periodSeconds: 10
            successThreshold: 1
```

### `service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: rs-service
spec:
  type: NodePort
  ports:
    - targetPort: 8080
      port: 8080
  selector:
    app: demoapp
```

## Deploying the ReplicaSet
Apply the YAML file using:
```sh
kubectl apply -f replicaset.yaml
```

## Verifying the ReplicaSet
Check the status of the ReplicaSet:
```sh
kubectl get replicasets
```

List the running pods:
```sh
kubectl get pods
```

Describe the ReplicaSet for more details:
```sh
kubectl describe replicaset demo-rs
```

## Scaling the ReplicaSet
To scale the ReplicaSet (e.g., change the number of replicas to 3):
```sh
kubectl scale replicaset demo-rs --replicas=3
```

## Rolling Out Updates
To update the ReplicaSet (e.g., changing the container image):
```sh
kubectl set image replicaset/demo-rs con-1=aravindh146/dbconnectiontestapp:v2
```

Check rollout history:
```sh
kubectl rollout history replicaset demo-rs
```

## Deleting the ReplicaSet
To remove the ReplicaSet and its pods:
```sh
kubectl delete -f replicaset.yaml
```

## Conclusion
ReplicaSets provide **automatic self-healing** and ensure that the desired number of pods are always running. They are often used in combination with **Deployments** for advanced rolling updates and rollbacks.

For more details, refer to the [Kubernetes ReplicaSet Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/).

