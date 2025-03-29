# Kubernetes Annotations

## Overview
Annotations in Kubernetes allow adding non-identifying metadata to objects like Pods, Services, and Deployments. They help store additional information without affecting the core functionality.

## Key Features
- Store **custom metadata** like descriptions, ownership, and monitoring details.
- Used by **monitoring tools, logging systems, and automation scripts**.
- Unlike labels, **annotations are not used for selection or grouping**.

## Example Usage
The following example defines a **Pod with annotations**:
```yaml
metadata:
  annotations:
    description: "This is a sample pod with annotations."
    owner: "Aravindh Kumar Naryana"
    environment: "Development"
```

## Deploy the Pod
```sh
kubectl apply -f annotated-pod.yaml
```

## Verify Annotations
```sh
kubectl get pod annotated-pod --show-labels
kubectl describe pod annotated-pod | grep -A5 "Annotations"
```

## When to Use Annotations?
- **Documentation**: Add descriptions or change logs.
- **Monitoring**: Store Prometheus scraping info.
- **Debugging**: Add tracking information.

## Deleting the Pod
```sh
kubectl delete -f annotated-pod.yaml
```

For more details, visit [Kubernetes Docs](https://kubernetes.io/docs/concepts/overview/working-with-objects/annotations/).

