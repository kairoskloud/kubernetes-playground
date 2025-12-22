```sh
kubectl apply -f ./pod.v0.kyaml
```

```sh
kubectl get pod springboot-resize-in-place -o json| jq '.status.containerStatuses[0] | { resources, restartCount }'
```

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

## Adjust CPU limit for the pod

```sh
kubectl apply -f ./pod.v1.kyaml --subresource resize --server-side
```

```sh
kubectl get pod springboot-resize-in-place -o json | jq '.status.containerStatuses[0] | { resources, restartCount }'
```

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

## Adjust Memory limit for the pod

```sh
kubectl apply -f ./pod.v2.kyaml --subresource resize --server-side
```

```sh
kubectl get pod springboot-resize-in-place -o json | jq '.status.containerStatuses[0] | { resources, restartCount }'
```

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

```sh
kubectl delete -f ./pod.v2.kyaml
```