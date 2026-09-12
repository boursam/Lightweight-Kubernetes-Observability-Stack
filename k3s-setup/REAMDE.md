# K3S Setup
![K3s](https://img.shields.io/badge/K3s-Kubernetes-FFC61C?style=flat-square&logo=k3s&logoColor=black)

K3s is a fully certified, lightweight Kubernetes distribution, designed by Rancher for resource-constrained environments, edge computing, IoT, and local development.

---

### 1. System Requirements
For requirements, i've worked with min
- 2+ vCPUs, 2GB+ RAM (Using 4 vCPUs, and 4GB RAM)
- Ubuntu 20.04+ (Using 24.04 LTS)
- +20 G of Storage (Using 30GB)

--- 

### 2. Install the Server Node

To install the Server Node 

```bash
curl -sfL https://get.k3s.io | sh -
```

This installs k3s as a systemd service and starts it automatically. Check status:

```bash
sudo systemctl status k3s
```

So we verify that the node is ready, we execute the following command:

```bash
sudo k3s kubectl get nodes
```

```bash
[NOTE] For the setup, i used only one node, which will act as control node and worker node at the same time.
```

---

### 4. Configure 'kubectl' and 'helm' for Local Use

In order to use in a non root state, by our user, copy the kubeconfig to our local machine, or user profile:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
```

Then we need to verify the access:
```bash
kubectl get nodes
kubectl get pods -A
```

---
And here we ended the configuration of k3s :)
