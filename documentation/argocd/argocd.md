# [Install Argo CD](https://argo-cd.readthedocs.io/en/stable/getting_started/)

### Prepare Kubernetes

Create namespace 
```shell
kubectl create namespace argocd
```

OR
<details>
<summary>use argocd.namespace.yaml</summary>

```yaml
apiVersion: v1
kind: Namespace
metadata:
    name: argocd
```
</details>
 

### Install ArgoCD into Kubernetes

install core components for argocd 
```shell
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/core-install.yaml
```
    

adjust ingress for argocd ui
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: argocd-ingress
  namespace: argocd
spec:
  ingressClassName: traefik
  rules:
  - host: argocd.klab.home
    http:
      paths:
      - backend:
          service:
            name: argocd-server
            port:
              number: 80
        path: /
        pathType: Prefix
  tls:
  - hosts:
    - argocd.klab.home
```

>[!NOTE] Your environment shoulb be awere about domain __argocd.klab.home__

Check host is available
```shell
curl argocd.klab.home
```


install argocd cli for Linux(arm64) or check official site
```shell
wget https://github.com/argoproj/argo-cd/releases/latest/download/argocd-linux-arm64
sudo mv argocd-linux-arm64 /usr/local/bin/argocd
sudo chmod +x /usr/local/bin/argocd
```


How to get default password
```shell
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
```

Initialize admin password
```shell
argocd admin initial-password -n argocd
```

Or update admin password 
```shell
argocd account update-password
```



## Create first application

See example in [klab.yaml](../../argocd/dev/klab.yaml) file.

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: klab-environment
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  destination:
    name: in-cluster
    namespace: argocd
  source:
    path: argocd/dev/apps
    repoURL: https://github.com/KLab-env/environment.git
    targetRevision: HEAD
    directory:
      recurse: false
  sources: []
  project: klab
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
      - Validate=false
```

When the first application is created we can use git repository to deploy our own apps. Just put the configuration for new app by path `<git>/argocd/dev/apps`