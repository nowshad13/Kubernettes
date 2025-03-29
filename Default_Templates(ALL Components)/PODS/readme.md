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

## Conclusion
This setup demonstrates how to deploy a basic Nginx Pod in Kubernetes and expose it via a **NodePort Service** for external access. 🚀

