# Kubernetes Ingress

## Overview
Kubernetes **Ingress** is a resource that manages external access to services within a cluster. It allows you to expose HTTP and HTTPS routes and provides features like load balancing, SSL termination, and name-based virtual hosting.

## Why Use Ingress?
- **Single Entry Point:** Manages multiple services behind a single load balancer.
- **Path-Based Routing:** Direct traffic to different services based on request paths.
- **Name-Based Routing:** Supports multiple domain names in a single cluster.
- **SSL/TLS Termination:** Secure HTTPS communication using certificates.
- **Rewrite & Redirect:** Modify request paths before forwarding to services.

## Basic Example

### `ingress.yaml`
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /app1
        pathType: Prefix
        backend:
          service:
            name: app1-service
            port:
              number: 80
      - path: /app2
        pathType: Prefix
        backend:
          service:
            name: app2-service
            port:
              number: 80
```

## Deploying the Ingress
Apply the YAML file using:
```sh
kubectl apply -f ingress.yaml
```

## Verifying the Ingress
Check the status of the Ingress:
```sh
kubectl get ingress
```
Describe the Ingress for detailed information:
```sh
kubectl describe ingress my-ingress
```

## Notes
- An **Ingress Controller** (e.g., NGINX Ingress Controller, Traefik, or HAProxy) must be installed in the cluster for Ingress to function.
- DNS should be configured to resolve the host (e.g., `myapp.example.com`) to the Ingress Controller's external IP.

## Deleting the Ingress
To remove the Ingress configuration:
```sh
kubectl delete -f ingress.yaml
```

## Conclusion
Kubernetes Ingress simplifies service exposure and provides advanced traffic routing capabilities, making it a powerful tool for managing web applications in a cluster.

For more details, refer to the [Kubernetes Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/).

