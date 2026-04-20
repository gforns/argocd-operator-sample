
## Install ArgoCD

```
kubectl create ns argocd

kubectl apply -n argocd \
  --server-side \
  -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

## Get Admin password

```
kubectl get secret argocd-initial-admin-secret \
  -n argocd \
  -o jsonpath="{.data.password}" | base64 -d && echo

rHhaHmahxucdmiIM
```

## Port forward Admin web

```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

https://localhost:8080/