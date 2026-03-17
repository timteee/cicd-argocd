# CI/CD with ArgoCD — Learning Project

## Project Structure
```
cicd-argocd/
├── app/        # Sample application source code (nginx or python)
├── k8s/        # Kubernetes manifests (Deployment, Service, etc.)
├── gitops/     # ArgoCD Application manifests (what ArgoCD watches)
└── README.md   # This file
```

## Installed Tools
| Tool      | Version  | Purpose                                      |
|-----------|----------|----------------------------------------------|
| kubectl   | v1.35.2  | Talk to k8s cluster                          |
| minikube  | v1.38.1  | Local single-node k8s cluster                |
| helm      | v4.1.3   | k8s package manager (used to install ArgoCD) |
| argocd    | v3.3.4   | ArgoCD CLI                                   |
| colima    | v0.10.1  | Lightweight Docker runtime for M1 Mac        |
| docker    | v29.3.0  | Container runtime (via Colima)               |

## Daily Startup (if you've rebooted)
```bash
# 1. Start Colima (Docker runtime)
colima start

# 2. Start minikube
minikube start --cpus=4 --memory=5500 --driver=docker

# 3. Verify cluster
kubectl get nodes

# 4. If ArgoCD is installed, port-forward the UI
kubectl port-forward svc/argocd-server -n argocd 8080:443
# Then open https://localhost:8080 (accept the self-signed cert)
```

## Architecture
```
Your code (local git repo)
        │
        │ git push
        ▼
  GitHub repo  ◄──── ArgoCD watches this
        │
        │ ArgoCD detects change (polls every 3 min or via webhook)
        ▼
  minikube (local k8s cluster)
        │
        │ applies k8s/ manifests
        ▼
  Running app (nginx)
```

## Key Commands Reference
```bash
# Cluster
kubectl get nodes
kubectl get all -n argocd
minikube status

# ArgoCD
argocd app list
argocd app get <app-name>
argocd app sync <app-name>
argocd app diff <app-name>

# Git
git status
git log --oneline --graph --all
git diff
```

## ArgoCD UI
- URL: https://localhost:8080 (when port-forwarding is active)
- Username: admin
- Password: stored in secret → run:
  kubectl get secret argocd-initial-admin-secret -n argocd \
    -o jsonpath='{.data.password}' | base64 -d && echo
```
