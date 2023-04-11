# Install Argo CD

Install Argo CD on k3s server with following commands

Ref: https://argo-cd.readthedocs.io/en/stable/getting_started/

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
