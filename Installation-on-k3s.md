# Argo CD

Install Argo CD on k3s server with following commands

Ref: 
 - https://argo-cd.readthedocs.io/en/stable/getting_started/
 - https://argoproj.github.io/

Free course
- https://en.git.ir/linkedin-kubernetes-gitops-with-argocd/

## Install

```bash
sudo kubectl create namespace argocd
sudo kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Port Forwarding

```sh
# Update service
sudo kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

# local
sudo kubectl port-forward svc/argocd-server -n argocd xxxx:xxx

# Remote
ssh -L 8002:127.0.0.1:xxxxx xxxxx@xxx.xxx.xxx.xxx -p xx
```

The API server can then be accessed using https://127.0.0.1:8002

## Get apssword and Login to UI

```sh
# URL: https://127.0.0.1:8002
# Username: admin

# Password: 
echo $(sudo kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" -n argocd | base64 --decode)
```

# Argo Events

Event-Based Dependency Manager for Kubernetes: https://www.youtube.com/watch?v=sUPkGChvD54&ab_channel=DevOpsToolkit

# Argo Workflows and Pipelines

CI/CD, Machine Learning, and Other Kubernetes Workflows: https://www.youtube.com/watch?v=UMaivwrAyTA&ab_channel=DevOpsToolkit

# Argo Rollouts

Canary Deployments Made Easy In Kubernetes: https://www.youtube.com/watch?v=84Ky0aPbHvY&ab_channel=DevOpsToolkit
