# Progressive Delivery Masterclass

## GitOps for Argo

kind delete clusters demo-cluster
kind create cluster --config /tmp/progressiveDelivery-masterclass/cluster/kind-cluster.yaml

### Install Agro CD

```
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

### Patch ArgoCD to support Application Heath Status

```
kubectl patch cm/argocd-cm -n argocd --type=merge -p='{"data":{"resource.customizations.health.argoproj.io_Application":"hs = {}\nhs.status = \"Progressing\"\nhs.message = \"\"\nif obj.status ~= nil then\n  if obj.status.health ~= nil then\n    hs.status = obj.status.health.status\n    if obj.status.health.message ~= nil then\n      hs.message = obj.status.health.message\n    end\n  end\nend\nreturn hs\n"}}'
```

### Get Argo Password and Port-Forward Argo UI

```
argocd admin initial-password -n argocd
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

### Apply the App of Apps

```
kubectl -n argocd apply -f app-of-apps.yaml
```

The App of Apps will create all other apps which are included in the `applications` folder from the GtiHub Repository.

