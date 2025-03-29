# Kubernetes Deployment

## Overview
A **Deployment** in Kubernetes is used to manage the lifecycle of a set of pods, ensuring that the desired number of replicas run at all times. It allows rolling updates, rollbacks, and scaling.

## Use Cases
- **Stateless applications** (e.g., Web servers, API services)
- **Rolling updates and rollbacks**
- **Ensuring high availability**

## Creating a Deployment
Below is an example **Deployment YAML** that runs an Nginx-based application.

### `v1/deployment.yaml`
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
  labels:
    app: demo
spec:
  minReadySeconds: 8
  replicas: 2
  selector:
    matchLabels:
      app: demo
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: demo
        version: "1.0"
    spec:
      terminationGracePeriodSeconds: 30
      containers:
        - name: con-1
          image: shaikkhajaibrahim/dmtest:1.0
          ports:
            - containerPort: 80
              protocol: "TCP"
          readinessProbe:
            initialDelaySeconds: 3
            successThreshold: 1
            failureThreshold: 2
            periodSeconds: 5
            timeoutSeconds: 2
            httpGet:
              path: '/'
              port: 80
```

### `v1/service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: app-svc
spec:
  type: NodePort
  ports:
    - targetPort: 80
      port: 80
      protocol: TCP
  selector:
    app: demo
```

## Deploying the Application
Apply the Deployment and Service YAMLs:
```sh
kubectl apply -f v1/deployment.yaml
kubectl apply -f v1/service.yaml
```

## Verifying the Deployment
Check the deployment status:
```sh
kubectl get deployments
```

Check running pods:
```sh
kubectl get pods -o wide
```

Check service details:
```sh
kubectl get svc app-svc
```

## Rolling Out Updates
To update the deployment, modify the YAML (e.g., change version `1.0` to `2.0`) and apply:
```sh
kubectl apply -f v2/deployment.yaml
```

Monitor the rollout:
```sh
kubectl rollout status deployment my-deployment
```

Check rollout history:
```sh
kubectl rollout history deployment my-deployment
```

Rollback if needed:
```sh
kubectl rollout undo deployment my-deployment --to-revision=1
```

## Scaling the Deployment
To scale the replicas:
```sh
kubectl scale deployment my-deployment --replicas=4
```

## Deleting the Deployment
To remove the deployment and service:
```sh
kubectl delete -f v1/deployment.yaml
kubectl delete -f v1/service.yaml
```

## Conclusion
Deployments are essential for managing containerized applications in Kubernetes. They allow for smooth updates, high availability, and rollback mechanisms.

For more details, refer to the [Kubernetes Deployment Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/).

