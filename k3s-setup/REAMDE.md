## K3S Setup

K3s is a fully certified, lightweight Kubernetes distribution, designed by Rancher for resource-constrained environments, edge computing, IoT, and local development.

### 1. System Requirements
For requirements, i've worked with min
- 2+ vCPUs, 2GB+ RAM (Using 4 vCPUs, and 4GB RAM)
- Ubuntu 20.04+ (Using 24.04 LTS)
- +20 G of Storage (Using 30GB)

### 2. Install the Server Node

To install the Server Node 

```bash
curl -sfL https://get.k3s.io | sh -
```

This installs k3s as a systemd service and starts it automatically. Check status:

```bash
sudo systemctl status k3s
```

So we verify that the node is ready, we execute the following command :

```bash
sudo k3s kubectl get nodes
```

```bash
[NOTE] For the setup, i used only one node, which will act as control node and worker node at the same time.
```

