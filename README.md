# Lightweight-Kubernetes-Observability-Stack

![K3s](https://img.shields.io/badge/K3s-Kubernetes-FFC61C?style=flat-square&logo=k3s&logoColor=black)
![Helm](https://img.shields.io/badge/Helm-Charts-277A9F?style=flat-square&logo=helm&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Metrics-DA4E31?style=flat-square&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboards-2E3440?style=flat-square&logo=grafana&logoColor=F8B600)

This project is a lightweight, self-hosted Kubernetes observability platform built on **k3s**, deployed and managed via **Helm**, with **Prometheus** for metrics collection and **Grafana** for visualization and dashboarding. It's designed for environments where you need production-style observability without the overhead of running a full Kubernetes distribution.

---

## Stack

| Component | Role |
|---|---|
| **[k3s](https://k3s.io/)** | Lightweight, certified Kubernetes distribution |
| **[Helm](https://helm.sh/)** | Kubernetes package manager for deploying and managing charts |
| **[Prometheus](https://prometheus.io/)** | Metrics collection, storage, and alerting |
| **[Grafana](https://grafana.com/)** | Dashboards and visualization |

## Architecture 
```
┌────────────────────────────────────────────────┐
│                  k3s node                      │
│                                                │
│   ┌────────────────────────────────────────┐   │
│   │   kube-prometheus-stack (Helm chart)   │   │
│   │                                        │   │
│   │   ┌────────────┐      ┌─────────────┐  │   │
│   │   │ Prometheus │◄─────┤   Grafana   │  │   │
│   │   └─────┬──────┘      └─────────────┘  │   │
│   │         │  scrapes                     │   │
│   │   ┌─────┴──────┐  ┌──────────────────┐ │   │
│   │   │ kube-state-│  │ node-exporter    │ │   │
│   │   │ metrics    │  │ (per node)       │ │   │
│   │   └────────────┘  └──────────────────┘ │   │
│   └────────────────────────────────────────┘   │
└────────────────────────────────────────────────┘
```

---

## Prerequisites

- A Linux host (or VM) to run the k3s node — see [System Requirements](#system-requirements) below.
- `sudo`/root access to install k3s and system packages.
- Basic familiarity with `kubectl` and the command line.

### System Requirements

| Resource | Minimum       |
|----------|---------------|
| CPU      | 2+ vCPUs      |
| RAM      | 2GB+          |
| Storage  | 20GB+         |
| OS       | Ubuntu 20.04+ |

---

## Setup Guide

Follow these in order — each builds on the previous one:

1. **[K3s Setup](./k3s-README.md)** : install and configure the single-node k3s cluster, and set up `kubectl`/`helm` for local use.
2. **[Helm / Prometheus / Grafana Setup](./README.md)** : install Helm, add the `prometheus-community` repo, and deploy `kube-prometheus-stack` onto the cluster.

Once both are done, you'll have a running Prometheus + Grafana stack, reachable via `kubectl port-forward` (see the Helm guide for exact commands and default credentials).

---

## Repository Structure

```
.
├── README.md                # this file — project overview
├── k3s/
│   └── README.md             # k3s installation and local kubectl/helm setup
└── helm/
    └── README.md             # Helm install + kube-prometheus-stack deployment
```

> Adjust the links in [Setup Guide](#setup-guide) above to match wherever these files actually live in your repo (e.g. `k3s/README.md`, `helm/README.md`, or flat filenames like `k3s-README.md`).

---

> [!NOTE]
> This stack targets a **single-node** k3s setup (control-plane + worker combined).
> `kube-prometheus-stack` bundles Prometheus, Grafana, Alertmanager, node-exporter, and kube-state-metrics in one chart, with Grafana pre-wired to Prometheus as a datasource
> no manual data source configuration needed.
