# argocd-demo

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install -n argocd --create-namespace  argo-cd argo/argo-cd
kubectl -n argocd apply -f bootstrap
kubectl -n argocd port-forward svc/argo-cd-argocd-server  8080:80
```
