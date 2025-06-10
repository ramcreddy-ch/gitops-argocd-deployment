# GitOps with ArgoCD

A demonstration of GitOps workflow using ArgoCD to manage multi-environment Kubernetes deployments.

## Architecture

```
┌───────┐     ┌────────┐     ┌────────────┐     ┌─────────────┐
│ Git   │────▶│ ArgoCD │────▶│ Kubernetes │────▶│ Deployments │
│ Repo  │     │ Server │     │  Cluster   │     │ (Dev/Prod)  │
└───────┘     └────────┘     └────────────┘     └─────────────┘
```

## Structure

- **base/**: Base Kubernetes manifests (Deployment, Service)
- **apps/**: Environment-specific overlays using Kustomize
  - **dev/**: Development environment (1 replica)
  - **staging/**: Staging environment (2 replicas)
  - **prod/**: Production environment (3 replicas)
- **argocd/**: ArgoCD Application manifests

## Prerequisites

- Kubernetes Cluster (Minikube, Kind, or Cloud)
- kubectl
- ArgoCD installed on the cluster

## Quick Start

### 1. Install ArgoCD

```bash
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### 2. Access ArgoCD UI

```bash
# Port forward
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

- **URL**: https://localhost:8080
- **Username**: `admin`
- **Password**: `kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d`

### 3. Deploy Applications

Apply the Application manifests to create the GitOps sync:

```bash
kubectl apply -f argocd/applications.yaml
```

ArgoCD will automatically sync the `apps/dev`, `apps/staging`, and `apps/prod` directories to your cluster namespaces.

## How it Works

1. **Commit Changes**: Change `replicas` in `apps/dev/deployment-patch.yaml` and push to Git.
2. **Auto Sync**: ArgoCD detects the change and updates the deployment in the `dev` namespace automatically.
3. **Self-Healing**: If you manually delete a pod or deployment, ArgoCD restores it to the Git state.

## Author

Ramchandra Chintala
