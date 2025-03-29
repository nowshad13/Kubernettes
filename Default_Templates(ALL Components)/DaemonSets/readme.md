# Kubernetes DaemonSet

## Overview
A **DaemonSet** ensures that a specific pod runs on **all (or some) nodes** in a Kubernetes cluster. It is typically used for running system services such as log collection, monitoring agents, or networking components on every node.

## Use Cases
- **Log collection** (e.g., Fluentd, Filebeat)
- **Monitoring agents** (e.g., Prometheus Node Exporter)
- **Networking components** (e.g., CNI plugins, kube-proxy)

## Creating a DaemonSet
Below is an example **DaemonSet YAML** that deploys Fluentd on all nodes.

### `daemonset.yaml`
```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-daemonset
  labels:
    app: fluentd
spec:
  selector:
    matchLabels:
      app: fluentd
  template:
    metadata:
      labels:
        app: fluentd
    spec:
      containers:
        - name: fluentd
          image: fluent/fluentd:v1.3-debian-1
          ports:
            - containerPort: 24224
              protocol: TCP
            - containerPort: 24224
              protocol: UDP
```

## Deploying the DaemonSet
Apply the YAML file using:
```sh
kubectl apply -f daemonset.yaml
```

## Verifying the DaemonSet
Check the status of the DaemonSet:
```sh
kubectl get daemonsets
```

List the pods and verify that they are running on all nodes:
```sh
kubectl get pods -o wide
```

Describe the DaemonSet for more details:
```sh
kubectl describe daemonset fluentd-daemonset
```

## Rolling Out Updates
To update the DaemonSet (e.g., changing the image version):
```sh
kubectl set image daemonset/fluentd-daemonset fluentd=fluent/fluentd:v1.4-debian-1
```

Check rollout history:
```sh
kubectl rollout history daemonset fluentd-daemonset
```

## Deleting the DaemonSet
To remove the DaemonSet and its pods:
```sh
kubectl delete -f daemonset.yaml
```

## Conclusion
DaemonSets are essential for running background services on every node in a Kubernetes cluster. They ensure that critical workloads, like logging and monitoring, remain highly available across all nodes.

For more details, refer to the [Kubernetes DaemonSet Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

