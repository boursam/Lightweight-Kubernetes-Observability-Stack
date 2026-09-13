# K3s Setup
![K3s](https://img.shields.io/badge/K3s-Kubernetes-FFC61C?style=flat-square&logo=k3s&logoColor=black)

K3s is a fully certified, lightweight Kubernetes distribution, designed by Rancher for resource-constrained environments, edge computing, IoT, and local development.

---

### 1. System Requirements

Minimum requirements, and what I actually used:

| Resource | Minimum      | Used            |
|----------|--------------|-----------------|
| CPU      | 2+ vCPUs     | 4 vCPUs         |
| RAM      | 2GB+         | 4GB             |
| Storage  | 20GB+        | 30GB            |
| OS       | Ubuntu 20.04+| Ubuntu 24.04 LTS|


> [!NOTE]
> For this setup I used a single node, which acts as both control-plane and worker at the same time.

---

### 2. Install the Server Node

Install k3s with the official install script:

```bash
curl -sfL https://get.k3s.io | sh -
```

This installs k3s as a systemd service and starts it automatically. Check its status:

```bash
sudo systemctl status k3s
```

Confirm the install and check the version:

```bash
k3s --version
```

To verify the node is up and ready:

```bash
sudo k3s kubectl get nodes
```

You should see a single node in `Ready` state, with roles `control-plane,master`.

---

### 3. Open Required Ports (if a firewall is enabled)

If `ufw` or another firewall is active on the node, allow the ports k3s needs:

```bash
sudo ufw allow 6443/tcp        # Kubernetes API server
sudo ufw allow 10250/tcp       # kubelet
sudo ufw allow from <pod-cidr> # optional: allow traffic from the pod network (default: 10.42.0.0/16)
```

Skip this step if the firewall is disabled or already permissive.

---

### 4. Configure `kubectl` and `helm` for Local Use

By default the kubeconfig lives at `/etc/rancher/k3s/k3s.yaml` and is only readable by root. To use `kubectl`/`helm` as a regular user, copy it into your user profile:

```bash
mkdir -p ~/.kube
sudo cp /etc/rancher/k3s/k3s.yaml ~/.kube/config
sudo chown $(id -u):$(id -g) ~/.kube/config
chmod 600 ~/.kube/config
```

Alternatively, without copying the file, you can just point `KUBECONFIG` at it for the current session:

```bash
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

Then verify access:

```bash
kubectl get nodes
kubectl get pods -A
```

Since Helm reads the same `~/.kube/config` (or `KUBECONFIG`), it now works out of the box too, no extra setup needed.

---

### 5. Uninstalling (if needed)

k3s ships with an uninstall script for the server node:

```bash
sudo /usr/local/bin/k3s-uninstall.sh
```

---

And that's it, k3s is installed and ready for workloads :)
