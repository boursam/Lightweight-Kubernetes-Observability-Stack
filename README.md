# Lightweight-Kubernetes-Observability-Stack

![K3s](https://img.shields.io/badge/K3s-Kubernetes-FFC61C?style=flat-square&logo=k3s&logoColor=black)
![Helm](https://img.shields.io/badge/Helm-Charts-277A9F?style=flat-square&logo=helm&logoColor=white)
![Prometheus](https://img.shields.io/badge/Prometheus-Metrics-DA4E31?style=flat-square&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/Grafana-Dashboards-2E3440?style=flat-square&logo=grafana&logoColor=F8B600)

This project represent a lightweight, self-hosted Kubernetes observability platform built on **k3s**, deployed and managed via **Helm**, with **Prometheus** for metrics collection and **Grafana** from visualization and dashboarding. It's designed for environment where you need production-style observability without the overhead of a full Kubernetes distribution.

## Stack
- **[k3s](https://k3s.io/)** - Lightweight, certified Kubernetes distribution
- **[Helm](https://helm.sh/)** - Kubernetes package manager for deploying and managing charts
- **[Prometheus](https://prometheus.io/)** - Metrics collection, storage, and alerting
- **[Grafana](https://grafana.com/)** - Dashboards and visualization

## Prerequisites
- A Linux host (or VM) for k3s node(s).
- Sufficient permissions to install and run what we need.
