# Kubernetes Guestbook Monitoring with Pulumi, Prometheus, and Grafana

## Project Overview

This project extends the Kubernetes Guestbook application with monitoring using Prometheus and Grafana. The infrastructure is deployed and managed using Pulumi and Kubernetes.

The monitoring solution helps track Kubernetes pod health, application availability, and Prometheus targets.

## Architecture

The project includes:

- Guestbook frontend with 3 replicas
- Redis leader
- Redis replica
- Prometheus for metrics collection
- Grafana for monitoring dashboards
- Node Exporter for node metrics
- kube-state-metrics for Kubernetes resource metrics

## Technologies

- AWS-style cloud-native tools: Kubernetes, Docker
- Pulumi with TypeScript
- Minikube
- Helm
- Prometheus
- Grafana
- Node Exporter
- kube-state-metrics

## Project Structure

```text
kubernetes-ts-guestbook/
├── components/
├── simple/
│   ├── grafana/
│   ├── prometheus/
│   ├── index.ts
│   ├── package.json
│   └── Pulumi.yaml
└── README.md
```

## Prerequisites

- Docker Desktop
- Minikube
- Kubernetes CLI (`kubectl`)
- Node.js and npm
- Pulumi CLI
- Helm

## Deployment

Start Minikube:

```bash
minikube start --driver=docker
```

Go to the Pulumi project:

```bash
cd simple
npm install
```

Configure the Grafana password as a Pulumi secret. Replace `YOUR_PASSWORD` with your own password:

```bash
pulumi config set --secret grafanaPassword YOUR_PASSWORD
```

Preview and deploy:

```bash
pulumi preview
pulumi up
```

## Access Grafana

Grafana is exposed through NodePort 30080.

For local access from Windows, run:

```bash
kubectl port-forward svc/grafana-f35e58bd 3001:80 -n monitoring
```

Open:

```text
http://localhost:3001
```

Login:
- Username: `admin`
- Password: the password configured in Pulumi

Keep the port-forward terminal running while using Grafana.

## Access Prometheus

Run:

```bash
kubectl port-forward svc/prometheus-978e0f70-server 9090:80 -n monitoring
```

Open:

```text
http://localhost:9090
```

## Monitoring and Verification

The Grafana dashboard includes panels for:

1. Guestbook Pod Readiness
2. Prometheus Target Health
3. Guestbook Pod Status

Example PromQL queries:

Check Prometheus targets:

```promql
up
```

Check Guestbook pod readiness:

```promql
kube_pod_status_ready{namespace="default",condition="true",pod=~"frontend.*|redis-leader.*|redis-replica.*"}
```

Check Guestbook pods in Running state:

```promql
kube_pod_status_phase{namespace="default",pod=~"frontend.*|redis-leader.*|redis-replica.*",phase="Running"}
```

The dashboard was verified with healthy Prometheus targets and running Guestbook pods.

## Useful Kubernetes Commands

```bash
kubectl get pods
kubectl get services
kubectl get pods -n monitoring
kubectl get services -n monitoring
```

## Cleanup

To remove the deployed resources:

```bash
pulumi destroy
```

## Author

Alice Blanche Ekane

GitHub: https://github.com/aliceekane/guestbook-monitoring
