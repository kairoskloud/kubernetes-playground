
# Chaos Mesh Playground

This guide demonstrates how to use [Chaos Mesh](https://chaos-mesh.org/) for chaos engineering in Kubernetes, including both StressChaos and Pod Fault scenarios.

---

## 1. Installation of Chaos Mesh

Chaos Mesh is a powerful cloud-native chaos engineering platform for Kubernetes. To install it using [KillerCoda](https://killercoda.com/):

**Prerequisites:**
- Kubernetes cluster
- kubectl installed

**Installation Steps:**
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

## 2. Deploy the Deployment

Before running Chaos Mesh experiments, deploy the target application. For example, to deploy the `springboot-starterkit` used in the StressChaos resources:

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
        version: v1
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

## 3. StressChaos Cases

StressChaos is used to simulate CPU and memory stress on pods. Example resources are provided in the `/chaos-mesh/stress/` directory.

**Deploying a StressChaos Resource:**
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

**Resource Structure:**
- `cpu/` and `memory/` folders contain YAMLs for different stress modes:
  - `all.yaml`: Stress all pods
  - `fixed-percent.yaml`: Stress a fixed percentage
  - `one.yaml`: Stress a single pod
  - `random-max-percent.yaml`: Stress random pods up to a max percent

---


## 4. Pod Fault Cases

This section covers the different Pod fault scenarios available in this playground. You can find YAML manifests for various pod failure and kill cases under the `pod-faults/` directory:

- **Pod Failure:**
  - [pod-failure-all.yaml](pod-faults/pod-failure/pod-failure-all.yaml)
  - [pod-failure-fixed-percent.yaml](pod-faults/pod-failure/pod-failure-fixed-percent.yaml)
  - [pod-failure-fixed.yaml](pod-faults/pod-failure/pod-failure-fixed.yaml)
  - [pod-failure-one.yaml](pod-faults/pod-failure/pod-failure-one.yaml)
  - [pod-failure-random-max-percent.yaml](pod-faults/pod-failure/pod-failure-random-max-percent.yaml)
- **Pod Kill:**
  - [pod-kill-all.yaml](pod-faults/pod-kill/pod-kill-all.yaml)
  - [pod-kill-fixed-percent.yaml](pod-faults/pod-kill/pod-kill-fixed-percent.yaml)
  - [pod-kill-fixed.yaml](pod-faults/pod-kill/pod-kill-fixed.yaml)
  - [pod-kill-one.yaml](pod-faults/pod-kill/pod-kill-one.yaml)
  - [pod-kill-random-max-percent.yaml](pod-faults/pod-kill/pod-kill-random-max-percent.yaml)
- **Container Kill:**
  - See manifests in [pod-faults/container-kill/](pod-faults/container-kill/)

### Demo Video

Watch a demonstration of pod fault injection in action:

<video src="https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/refs/heads/chaos/implement-pod-failure/chaos-mesh/assets/chaos-mesh-pod-fault.mp4" controls width="600"></video>

---

## 5. References
- [Chaos Mesh Documentation](https://chaos-mesh.org/docs/)
- [StressChaos API Reference](https://chaos-mesh.org/docs/basic-features/)
