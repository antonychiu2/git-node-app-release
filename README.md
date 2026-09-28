# git-node-app-release

Deployment configuration for [git-node-app-test2](https://github.com/antonychiu2/git-node-app-test2).
The app's container image is built by CI and published to Docker Hub as
`antonychiu2/git-node-app-test2:<tag>`; this repo decides which tag runs where.

## Layout

| Path | Purpose |
|---|---|
| `git-node-app/` | Helm chart (Deployment + Service) |
| `git-node-app/values.yaml` | Defaults shared by every environment |
| `git-node-app/values-staging.yaml` | `staging` namespace, NodePort 30081 |
| `git-node-app/values-prod.yaml` | `production` namespace, 2 replicas, NodePort 30082 |
| `git-node-app/values-argo-demo.yaml` | `argo-demo` namespace (Argo CD practice), NodePort 30084 |
| `argocd/` | Argo CD `Application` manifests |

## Deploy with Helm

```bash
helm upgrade --install git-node-app ./git-node-app -n staging --create-namespace \
  -f git-node-app/values-staging.yaml --set image.tag=<tag> --wait
```

## Deploy with Argo CD (GitOps)

```bash
kubectl apply -f argocd/git-node-app-argo-demo.yaml
```

Then change `image.tag` in `git-node-app/values-argo-demo.yaml` and commit; Argo CD syncs it.
