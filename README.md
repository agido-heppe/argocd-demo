# argocd-demo

```bash
helm install -n argocd --create-namespace  argo-cd argo/argo-cd
k -n argocd apply -f bootstrap
kubectl -n argocd port-forward svc/argo-cd-argocd-server  8080:80
```
