# argocd-demo

## Tools:

* required: 
    * [kubectl](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/#installation-mit-homebrew-auf-macos): `brew install kubernetes-cli`
    * [helm](https://helm.sh/docs/intro/install/#from-homebrew-macos): `brew install helm`
* optional:
    * [kubectx](https://github.com/ahmetb/kubectx): `brew install kubectx`
    * [fzf](https://github.com/junegunn/fzf): `brew install fzf`

## Steps

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm install -n argocd --create-namespace  argo-cd argo/argo-cd
kubectl -n argocd apply -f bootstrap
kubectl -n argocd port-forward svc/argo-cd-argocd-server  8080:80
```
