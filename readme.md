# ArgoCD Sample App — Nginx Demo

A minimal Kubernetes manifest set used to demonstrate GitOps deployment with [Argo CD](https://argo-cd.readthedocs.io/).

This repo contains a simple **Nginx Deployment** and a **NodePort Service**, intended to be synced by Argo CD from Git to a Kubernetes cluster.

## Contents

| File | Description |
|------|--------------|
| `deployment.yaml` | Deploys 2 replicas of `nginx:1.27` |
| `service.yaml` | Exposes the Nginx pods via a NodePort Service |

## Manifests

### Deployment
- **Name:** `nginx`
- **Replicas:** 2
- **Image:** `nginx:1.27`
- **Container Port:** 80

### Service
- **Name:** `nginx`
- **Type:** `NodePort`
- **Port:** 80 → **Target Port:** 80
- **Selector:** `app: nginx`

## Prerequisites

- A running Kubernetes cluster (e.g., Minikube, Kind, or a cloud cluster)
- `kubectl` configured to access the cluster
- Argo CD installed in the `argocd` namespace

## Deploying Manually (without Argo CD)

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

Check the pods:

```bash
kubectl get pods -l app=nginx
```

Check the service and find the assigned NodePort:

```bash
kubectl get svc nginx
```

## Deploying via Argo CD

1. Push this repo to GitHub (or your Git provider of choice).
2. Create an Argo CD Application pointing to this repo, e.g.:

```bash
argocd app create nginx-demo \
  --repo https://github.com/Jigar-Chudasama-01/argocd-sample.git \
  --path . \
  --dest-server https://kubernetes.default.svc \
  --dest-namespace default
```

3. Sync the application:

```bash
argocd app sync nginx-demo
```

4. Watch the pods come up:

```bash
kubectl get pods -w
```

## Accessing the App

Once the Service is running, get the NodePort and your node's IP:

```bash
kubectl get svc nginx
```

Then visit:

```
http://<NODE_IP>:<NODE_PORT>
```

## Notes

- This is a **sample/demo project** meant for learning Argo CD's GitOps workflow — not production-ready.
- Any change pushed to this repo (e.g., bumping the image tag or replica count) can be auto-synced by Argo CD if configured with automated sync policies.
