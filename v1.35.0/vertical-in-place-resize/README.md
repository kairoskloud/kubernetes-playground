# Vertical In-Place Pod Scalin

This document demonstrates **Vertical In-Place Pod Scaling**, a feature introduced in **Kubernetes v1.35**.  
It shows how CPU and memory limits can be updated on a **running Pod** using the `resize` subresource, and how Kubernetes behaves when applying these changes.

## Pod Resize Policy Configuration

The Pod is configured with a **`resizePolicy`** that defines how Kubernetes should handle resource changes for each resource type:

```yaml
resizePolicy:
  - resourceName: cpu
    restartPolicy: NotRequired
  - resourceName: memory
    restartPolicy: RestartContainer
```

### Why this configuration matters

The CPU configuration `restartPolicy: NotRequired`, allows CPU limits to be adjusted in-place, the container continues running with no restart.

While for memory `restartPolicy: RestartContainer`, requires the container to be restarted when memory limits change, ensures safety when live memory resizing is not supported or desired.

---

## 1. Create the Pod (Initial State)

### Description

Creates a Pod named springboot-resize-in-place defining the initial CPU and memory requests/limits. This serves as the baseline configuration before any resizing.

```sh
kubectl apply -f https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/refs/heads/main/v1.35.0/vertical-in-place-resize/pod.v0.kyaml
```

### Inspect Container Resources and Restart Count

```sh
kubectl get pod springboot-resize-in-place -o json | jq '.status.containerStatuses[0] | { resources, restartCount }'
```
### Output

```json
{
  "resources": {
    "limits": {
      "cpu": "100m",
      "memory": "384Mi"
    },
    "requests": {
      "cpu": "100m",
      "memory": "256Mi"
    }
  },
  "restartCount": 0
}
```
### Explanation

The container starts with:

- CPU limit: 100m
- Memory limit: 384Mi
- restartCount = 0, confirming the container has not restarted

This represents the initial running state of the Pod.

## 2. Adjust CPU limit for the pod

### Description

Updates the CPU limit of the running Pod. Uses the resize subresource, enabling in-place resource changes `--server-side` ensures the API server manages the update safely.


```sh
kubectl apply -f https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/refs/heads/main/v1.35.0/vertical-in-place-resize/pod.v1.kyaml --subresource resize --server-side
```

### Verification 

```sh
kubectl get pod springboot-resize-in-place -o json | jq '.status.containerStatuses[0] | { resources, restartCount }'
```

### Output

```json
{
  "resources": {
    "limits": {
      "cpu": "200m",
      "memory": "384Mi"
    },
    "requests": {
      "cpu": "100m",
      "memory": "256Mi"
    }
  },
  "restartCount": 0
}
```

### Explanation

The CPU limit increased from 100m to 200m while memory settings remain unchanged and the container continues running without interruption.

## 3. Adjust Memory limit for the pod

```sh
kubectl apply -f https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/refs/heads/main/v1.35.0/vertical-in-place-resize/pod.v2.kyaml --subresource resize --server-side
```

### Verification

```sh
kubectl get pod springboot-resize-in-place -o json | jq '.status.containerStatuses[0] | { resources, restartCount }'
```

### Output

```json
{
  "resources": {
    "limits": {
      "cpu": "200m",
      "memory": "512Mi"
    },
    "requests": {
      "cpu": "100m",
      "memory": "256Mi"
    }
  },
  "restartCount": 1
}
```

## 4. Cleanup

```sh
kubectl delete -f https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/refs/heads/main/v1.35.0/vertical-in-place-resize/pod.v2.kyaml
```