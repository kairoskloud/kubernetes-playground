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
kubectl apply -f https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/main/chaos-mesh/app/deployment.yaml
```

---

## 3. StressChaos Cases

StressChaos is used to simulate CPU and memory stress on pods. You can find YAML manifests for various CPU and memory stress scenarios under the `stress/` directory:

**StressChaos Cases:**

- **CPU Stress:**
  - All pods CPU stress ([all.yaml](stress/cpu/all.yaml))
  - Fixed percent pods CPU stress ([fixed-percent.yaml](stress/cpu/fixed-percent.yaml))
  - One pod CPU stress ([one.yaml](stress/cpu/one.yaml))
  - Random max percent pods CPU stress ([random-max-percent.yaml](stress/cpu/random-max-percent.yaml))
- **Memory Stress:**
  - All pods memory stress ([all.yaml](stress/memory/all.yaml))
  - Fixed percent pods memory stress ([fixed-percent.yaml](stress/memory/fixed-percent.yaml))
  - One pod memory stress ([one.yaml](stress/memory/one.yaml))
  - Random max percent pods memory stress ([random-max-percent.yaml](stress/memory/random-max-percent.yaml))

---


## 4. Pod Fault Cases

This section covers the different Pod fault scenarios available in this playground. You can find YAML manifests for various pod failure and kill cases under the `pod-faults/` directory:

- **Pod Failure:**
  - All pods failure ([pod-failure-all.yaml](pod-faults/pod-failure/pod-failure-all.yaml))
  - Fixed percent pods failure ([pod-failure-fixed-percent.yaml](pod-faults/pod-failure/pod-failure-fixed-percent.yaml))
  - Fixed pods failure ([pod-faults/pod-failure/pod-failure-fixed.yaml))
  - One pod failure ([pod-failure-one.yaml](pod-faults/pod-failure/pod-failure-one.yaml))
  - Random max percent pods failure ([pod-failure-random-max-percent.yaml](pod-faults/pod-failure/pod-failure-random-max-percent.yaml))
- **Pod Kill:**
  - All pods kill ([pod-kill-all.yaml](pod-faults/pod-kill/pod-kill-all.yaml))
  - Fixed percent pods kill ([pod-kill-fixed-percent.yaml](pod-faults/pod-kill/pod-kill-fixed-percent.yaml))
  - Fixed pods kill ([pod-kill-fixed.yaml](pod-faults/pod-kill/pod-kill-fixed.yaml))
  - One pod kill ([pod-kill-one.yaml](pod-faults/pod-kill/pod-kill-one.yaml))
  - Random max percent pods kill ([pod-kill-random-max-percent.yaml](pod-faults/pod-kill/pod-kill-random-max-percent.yaml))
- **Container Kill:**
  - All containers kill ([container-kill-all.yaml](pod-faults/container-kill/container-kill-all.yaml))
  - Fixed percent containers kill ([container-kill-fixed-percent.yaml](pod-faults/container-kill/container-kill-fixed-percent.yaml))
  - Fixed containers kill ([container-kill-fixed.yaml](pod-faults/container-kill/container-kill-fixed.yaml))
  - One container kill ([container-kill-one.yaml](pod-faults/container-kill/container-kill-one.yaml))
  - Random max percent containers kill ([container-kill-random-max-percent.yaml](pod-faults/container-kill/container-kill-random-max-percent.yaml))

### Demo Video

Watch a demonstration of pod fault injection in action:

<video src="https://raw.githubusercontent.com/kairoskloud/kubernetes-playground/main/chaos-mesh/assets/chaos-mesh-pod-fault.mp4" controls width="600"></video>

---

## 5. References
- [Chaos Mesh Documentation](https://chaos-mesh.org/docs/)
- [StressChaos API Reference](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/)
- [Pod Fault API Reference](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/)
