# argocd-demo

## Tools:

* required: 
    * [kubectl](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/#installation-mit-homebrew-auf-macos): `brew install kubernetes-cli`
    * [helm](https://helm.sh/docs/intro/install/#from-homebrew-macos): `brew install helm`
* optional:
    * [kubectx](https://github.com/ahmetb/kubectx): `brew install kubectx`
    * [fzf](https://github.com/junegunn/fzf): `brew install fzf`


## Workflow


1. Develop in the test cluster using a branch
2. Stop the application and the test cluster
3. Change the targetRevision with the commit (optional: rebase)
4. Merge PR

### Repository secrets 

There are two ways to add the correct credentials to the repository:

* use the Argo CD UI
* use the `repository.yaml`

If you want to deploy secrets with the code, the suggested way is to 
* generate a token in your Github/Gitlab account
* uncomment the secrets part of the `repository.yaml`
* change the `username` and `password` (token)
* use `kubectl apply ...` command (it will be otherwise applied in the below steps automatically)


## Steps

1. Make sure to select the correct kubernets cluster: ``kubectx``
2. Install our helm chart:
    ```bash
    helm repo add argo https://argoproj.github.io/argo-helm
    helm upgrade --install -n argocd --create-namespace argo-cd argo/argo-cd
    ```
3. Apply Bootstrap file:
    ```bash
    kubectl -n argocd apply -f bootstrap
    ```
4. Retrieve password for Argo CD UI (command displayed after the above command):
    ```bash
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
    ```
5. Use portforwarding to access ArgoCD:
    ```bash
    kubectl -n argocd port-forward svc/argo-cd-argocd-server  8080:80
    ```


## FAQ


#### _How do you stop the local stack?_

Basically, you can uninstall the helm chart:
```bash
helm uninstall argo-cd -n argocd
```

In order to verify, that no resources are still blocked by any ghost applications, check the list of all or specific workspaces:
```bash
kubectl get all -A 
kubectl get all -n NAMESPACE
```
If there are any apps still running, use the advice above to remove everything from a namespace.

---

#### _After a delete, there are still some pods running_

In order to remove everything from a certain namespace (assume, everything for one application is in one namespace):

```
kubectl delete all --all -n NAMESPACE
```

---

#### _Application stuck in deletion_

https://stackoverflow.com/questions/71164538/argocd-application-resource-stuck-at-deletion

```bash
kubectl patch app APP_NAME -n argocd -p '{"metadata": {"finalizers": null}}' --type merge
```