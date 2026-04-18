# Argo CD layout

This directory lets Argo CD manage the existing manifests without changing the original files.

## Structure

- `apps`: child Argo CD `Application` resources
- `root-application.yaml`: optional app-of-apps entrypoint

Each child application points either to the repository root for the raw manifests or to the Kong Helm chart for the operator bootstrap.

## Important prerequisites

Before syncing these applications, the cluster must already have:

- Argo CD installed

The `kong-bootstrap` application installs the Kong Operator Helm chart and enables:

- Kong Operator CRDs
- Gateway API standard CRDs

The bootstrap application now sets those Helm values directly in the Argo CD `Application` manifest.

Until `kong-bootstrap` is healthy, Argo CD will not be able to reconcile resources such as `GatewayConfiguration`, `KongPlugin`, and `KongConsumer` correctly.

## Secrets

The current demo manifest does not include any Kubernetes `Secret` objects or hardcoded credentials.

If you later add API keys, certificates, or other sensitive values to this repository, store them with Sealed Secrets, External Secrets, or SOPS instead of committing plaintext secrets to Git.

## Recommended usage

Apply the parent application:

```bash
kubectl apply -f argocd/root-application.yaml -n argocd
```

The child applications are synced in waves:

- `kong-bootstrap` first
- `kong-gateway` second
- `kong-demo` third

## Repository settings

The `repoURL` values point to the current Git remote:

`https://github.com/gforns/argocd-operator-sample.git`

The bootstrap application also pulls the Helm chart from:

`https://charts.konghq.com`

If you deploy from a fork or a different branch, update `repoURL` and `targetRevision` in the Application manifests.