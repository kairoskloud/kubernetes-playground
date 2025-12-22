# Kubernetes Playground Examples

## Overview

This repository contains **simple, practical examples** that demonstrate **Kubernetes features in action**.  
The goal is to help users quickly understand how specific Kubernetes capabilities work by providing **minimal manifests and reproducible commands**.

## Purpose

- Showcase new and existing Kubernetes features
- Provide hands-on, easy-to-follow examples
- Serve as a learning and experimentation reference

Examples are designed for clarity and exploration, not direct production use.

---

## 🧪 Kubernetes Playground Options

You can try the examples in this repository using the following Kubernetes playgrounds and local environments:

### Online Playgrounds
- [**KillerCoda Kubernetes Playground**](https://killercoda.com/playgrounds/scenario/kubernetes)
  A browser-based, interactive Kubernetes environment with no local setup required. Ideal for quick experiments and learning.  

### Local Clusters
- [**kind (Kubernetes IN Docker)** ](https://kind.sigs.k8s.io/)
  Run a real Kubernetes cluster locally using Docker. Great for testing, CI, and feature exploration.

These options allow you to safely explore Kubernetes features without impacting production clusters.

---

## ⚠️ Security Disclaimer

For security and stability reasons, **never apply YAML or KYAML files downloaded from the internet directly into your Kubernetes cluster without a thorough review**.

**Best practice:** Treat every external YAML or KYAML file as untrusted until it has been carefully inspected and validated.

Before installing any resource definitions, always:

- Review **all manifests** for unexpected or malicious configurations
- Verify:
  - Container images and image sources
  - Privileged containers or elevated permissions
  - RBAC roles and cluster-wide permissions
  - Network policies and exposed services
- Ensure the manifests align with your organization’s **security and compliance standards**
- Test changes in a **non-production environment** first

Blindly applying untrusted manifests can result in:
- Unauthorized access to your cluster
- Privilege escalation
- Data exfiltration
- Service disruption