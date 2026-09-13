# helm setup
![Helm](https://img.shields.io/badge/Helm-Charts-277A9F?style=flat-square&logo=helm&logoColor=white)

Helm is a package manager for kubernetes that uses charts as its package format. Charts are primarily defined using YAML and can leverage Go templating to dynamically generate Kubernetes resource manifests.

## 1. Installing Helm
The installation of Helm is easy, i used the Apt package for Debian/Ubuntu (There're other methods for installation on the official page of Helm)

```bash
HELM_BUILDKITE_APT_KEY_ID="DDF78C3E6EBB2D2CC223C95C62BA89D07698DBC6"

sudo apt-get install curl gpg apt-transport-https --yes

curl -fsSL https://packages.buildkite.com/helm-linux/helm-debian/gpgkey > "${TMPDIR:-/tmp}/helm.gpg"

# Ensure that the key ID matches to prevent a repository compromise from establishing an attacker controlled key
if [ "$(gpg --show-keys --with-colons "${TMPDIR:-/tmp}/helm.gpg" | awk -F: '$1 == "fpr" {print $10}' | head -n 1)" != "${HELM_BUILDKITE_APT_KEY_ID}" ]; then echo "ERROR: Unexpected Helm APT key ID: potential key compromise"; exit 1; fi

cat "${TMPDIR:-/tmp}/helm.gpg" | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/helm.gpg] https://packages.buildkite.com/helm-linux/helm-debian/any/ any main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

sudo apt-get update
sudo apt-get install helm
```

Then we check if everything is correctly installed, we execute:

```bash
helm -v
```

## 2. Add upstream chart repositories

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

To check available charts:
```bash
helm repo list
```

To find the most recent version, we use the 'helm search repo' command:

```bash
helm search repo -l prometheus-community/kube-prometheus-stack | head -3
```

For the one used, it's the version 89.2.2, i used the default values yaml file, to see the values file, i used this command:

```bash
helm show values prometheus-community/kube-prometheus-stack --version 89.2.2 > values.yml
```

## 3. Installation
To install the chart, i used the helm upgrade command, we need to set the correct version correctly:
```bash
helm upgrade --install \
            --namespace monitoring \
            --create-namespace \
            --debug \
            --timeout 300s \
            --values values.yml \
            prometheus-stack prometheus-community/kube-prometheus-stack --version 89.2.2
```

Afterwards, we wait for image to be pulled up, and for containers to start, till they become in a 'Running' state:
```bash
kubectl get pods -n monitoring
```
The result should be as following:

```bash
NAME                                                     READY   STATUS    RESTARTS     AGE
alertmanager-prometheus-stack-kube-prom-alertmanager-0   2/2     Running   0            67s
prometheus-prometheus-stack-kube-prom-prometheus-0       2/2     Running   0            67s
prometheus-stack-grafana-6996f75d67-jv8pc                3/3     Running   0            33h
prometheus-stack-grafana-787798b6df-swwhv                2/3     Running   0            72s
prometheus-stack-kube-prom-operator-6c7dfd94bc-kfq86     1/1     Running   0            72s
prometheus-stack-kube-state-metrics-5c75657748-bmgm5     1/1     Running   0            72s
prometheus-stack-prometheus-node-exporter-l5khg          1/1     Running   0            69s
```

## 4. Accessing Grafana and Prometheus

To access the WEB UI of Grafana and Prometheus, i used the port forwarding technique. The release name is `prometheus-stack`, so that's the prefix on every service name — Grafana and Prometheus each get their own service:

```bash
# Grafana (service listens on port 80, container on 3000)
kubectl port-forward -n monitoring svc/prometheus-stack-grafana 3000:80 --address 0.0.0.0

# Prometheus
kubectl port-forward -n monitoring svc/prometheus-stack-kube-prom-prometheus 9090:9090 --address 0.0.0.0
```

Grafana needs a login. `kube-prometheus-stack` generates an admin password by default (unless you set one in `values.yml`) — grab it with:

```bash
kubectl get secret prometheus-stack-grafana -n monitoring -o jsonpath="{.data.admin-password}" | base64 --decode
```

Username is `admin`.

Access the WEB UI with `http://<host-IP>:<PORT>` and voilà !

- Grafana: `http://<host-IP>:3000`
- Prometheus: `http://<host-IP>:9090`

> [!NOTE]
> - The chart already wires Grafana to Prometheus as a default datasource and ships pre-built dashboards, so there's nothing extra to configure there.
> - To upgrade later, re-run `helm search repo -l prometheus-community/kube-prometheus-stack` for the newest version, regenerate `values.yml` against it, then re-run the same `helm upgrade --install` command with the new `--version`.
> - To uninstall:
>  ```bash
>  helm uninstall prometheus-stack -n monitoring
>  kubectl delete namespace monitoring
>  ```
