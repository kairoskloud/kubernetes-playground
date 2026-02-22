# Chaos Mesh & StressChaos Guide

## 1. Install Chaos Mesh

Chaos Mesh is a powerful cloud-native chaos engineering platform for Kubernetes. To install it using [KillerCoda](https://killercoda.com/):

### Prerequisites
- Kubernetes cluster
- kubectl installed

### Installation Steps
1. Add Chaos Mesh Helm repo:
   ```bash
   helm repo add chaos-mesh https://charts.chaos-mesh.org
   helm repo update
   ```
2. Install Chaos Mesh:
   ```bash
   kubectl create namespace chaos-mesh
   helm install chaos-mesh chaos-mesh/chaos-mesh --namespace=chaos-mesh --set chaosMeshVersion=v2.8.1
   ```

For more details, see the [official docs](https://chaos-mesh.org/docs/).

---


## 2. Deploy the Target Application

Before running Chaos Mesh experiments, deploy the target application. For example, to deploy the `springboot-starterkit` used in the StressChaos resources:

### Deploy the Application
You can deploy the target application directly with an inline command using a heredoc:

```bash
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: springboot-starterkit
  namespace: default
  labels:
    app: springboot-starterkit
    app.kubernetes.io/name: springboot-starterkit
spec:
  replicas: 4
  selector:
    matchLabels:
      app: springboot-starterkit
  template:
    metadata:
      labels:
        app: springboot-starterkit
        app.kubernetes.io/name: springboot-starterkit
        version: v1  # Add version label for traffic management
      annotations:
        sidecar.istio.io/inject: "true"  # Enable Istio sidecar injection
    spec:
      containers:
      - name: springboot-starterkit
        image: ghcr.io/josephrodriguez/springboot-starterkit:1.4.1
        ports:
        - containerPort: 8080
          name: http
        resources:
          requests:
            cpu: 800m
            memory: 512Mi
          limits:
            cpu: 1000m
            memory: 512Mi
        livenessProbe:
          httpGet:
            path: /actuator/health
            port: 8080
          failureThreshold: 5
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /actuator/health/readiness
            port: 8080
          failureThreshold: 5
          initialDelaySeconds: 15
          periodSeconds: 10
EOF
```

---

## 3. Deploy StressChaos Resources

StressChaos is used to simulate CPU and memory stress on pods. Example resources are provided in the `/chaos-mesh/stress/` directory.

### Deploying a StressChaos Resource
1. Choose a YAML file from:
   - `/chaos-mesh/stress/cpu/`
   - `/chaos-mesh/stress/memory/`
2. Apply the resource:
   ```bash
   kubectl apply -f <path-to-yaml>
   ```
   Example:
   ```bash
   kubectl apply -f chaos-mesh/stress/memory/fixed-percent.yaml
   ```

### Resource Structure
- `cpu/` and `memory/` folders contain YAMLs for different stress modes:
  - `all.yaml`: Stress all pods
  - `fixed-percent.yaml`: Stress a fixed percentage
  - `one.yaml`: Stress a single pod
  - `random-max-percent.yaml`: Stress random pods up to a max percent

---

## 4. Clean Up
To remove a StressChaos experiment:
```bash
kubectl delete -f <path-to-yaml>
```

---

## References
- [Chaos Mesh Documentation](https://chaos-mesh.org/docs/)
- [StressChaos API Reference](https://chaos-mesh.org/docs/basic-features/)
