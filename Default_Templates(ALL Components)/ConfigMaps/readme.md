# Kubernetes ConfigMap

## Overview
A **ConfigMap** in Kubernetes is used to store configuration data separately from application code. This helps in managing environment-specific configurations without modifying the container image.

## Use Cases
- Storing environment variables
- Providing configuration files to applications
- Separating secrets from application logic (though for sensitive data, **Secrets** should be used instead of ConfigMaps)

## Creating a ConfigMap
The following YAML file creates a **ConfigMap** containing MySQL database credentials.

### `configmap.yaml`
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: mysql-cm
data:
  MYSQL_ROOT_PASSWORD: admin123
  MYSQL_DATABASE: Ara
  MYSQL_USER: Ara
  MYSQL_PASSWORD: admin123
```

## Using ConfigMap in a Pod
The following **Pod definition** references the `mysql-cm` ConfigMap to inject environment variables into a MySQL container.

### `pod.yaml`
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mysql-db
spec:
  containers:
    - name: mysql
      image: mysql:latest
      envFrom:
        - configMapRef:
            name: mysql-cm
```

## Deploying the ConfigMap and Pod
Apply the ConfigMap and Pod using:
```sh
kubectl apply -f configmap.yaml
kubectl apply -f pod.yaml
```

## Verifying the ConfigMap
To list all ConfigMaps:
```sh
kubectl get configmaps
```

To describe a specific ConfigMap:
```sh
kubectl describe configmap mysql-cm
```

To view the data inside the ConfigMap:
```sh
kubectl get configmap mysql-cm -o yaml
```

## Checking Environment Variables in the Pod
To verify that the environment variables are correctly set in the Pod:
```sh
kubectl exec -it mysql-db -- env | grep MYSQL
```

## Updating a ConfigMap
If you need to modify the ConfigMap:
1. Edit the `configmap.yaml` file.
2. Reapply it using:
   ```sh
   kubectl apply -f configmap.yaml
   ```
   **Note:** Updating a ConfigMap will not automatically update running Pods. You may need to delete and recreate the Pod for changes to take effect:
   ```sh
   kubectl delete pod mysql-db
   kubectl apply -f pod.yaml
   ```

## Deleting the ConfigMap
To delete the ConfigMap:
```sh
kubectl delete configmap mysql-cm
```

## Conclusion
ConfigMaps provide a flexible way to manage application configurations separately from the container image, making deployments more portable and maintainable.

For more details, refer to the [Kubernetes ConfigMap Documentation](https://kubernetes.io/docs/concepts/configuration/configmap/).

