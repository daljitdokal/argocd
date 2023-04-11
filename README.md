# Install Argo CD

Install Argo CD on k3s server with folling commands

## Install

```bash
sudo kubectl create namespace argocd
sudo kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Port Forwarding

```bash
// Update service
sudo kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "NodePort"}}'

// local
sudo kubectl port-forward svc/argocd-server -n argocd 8080:443

// Remote
ssh -L 8002:127.0.0.1:xxxxx xxxxx@xxx.xxx.xxx.xxx -p xx
```
