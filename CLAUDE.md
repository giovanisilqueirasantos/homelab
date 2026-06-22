# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Tooling

Tools are managed via [mise](https://mise.jdx.dev/) and installed automatically by the devcontainer setup. Key tools: `kubectl`, `flux`, `k9s`, `terraform`, `tflint`.

Useful day-to-day commands:

```bash
# Validate kustomize overlays locally
kubectl kustomize apps/production/linkding

# Check FluxCD reconciliation status
flux get kustomizations -A

# Force Flux to reconcile immediately
flux reconcile kustomization apps --with-source

# Encrypt a new secret file with SOPS
sops --encrypt --in-place apps/production/<app>/<secret>.yaml

# Decrypt a secret for inspection
sops --decrypt apps/production/<app>/<secret>.yaml
```

## Architecture

This is a **GitOps homelab** managed by FluxCD on a k3s cluster. Pushing to `main` triggers automatic reconciliation.

### How Flux wires everything together

`clusters/production/` holds three Flux `Kustomization` resources that point to the three top-level directories:

- `apps.yaml` → `./apps/production` (reconciles every 10 min)
- `databases.yaml` → `./databases/production` (reconciles every 1 h)
- `infrastructure.yaml` → `./infrastructure/production` (reconciles every 1 h)

All three use SOPS+AGE decryption via a cluster secret named `sops-age`.

### apps/ — two-layer kustomize overlay

- `apps/base/<app>/` — environment-agnostic manifests: `Namespace`, `Deployment`, `Service`, `PersistentVolumeClaim`.
- `apps/production/<app>/` — extends the base and adds production concerns:
  - `<app>-configmap.yaml` — non-secret env vars for the app container.
  - `<app>-container-env-secret.yaml` — SOPS-encrypted `Secret` supplying sensitive env vars.
  - `cloudflared-deployment.yaml` + `cloudflared-configmap.yaml` + `cloudflared-creds-secret.yaml` — a Cloudflare Tunnel sidecar that exposes the app at `<app>.eniohomelab.com` without opening firewall ports.

Every `apps/production/<app>/kustomization.yaml` references `../../base/<app>/` as a resource.

### databases/ — CloudNativePG clusters

Each app that needs Postgres gets its own CloudNativePG `Cluster` under `databases/production/<app>/`:

- `database.yaml` — defines the `Cluster` (1 instance, Postgres 16, bootstraps from backup).
- `scheduled-backup.yaml` — daily backup at 03:00 UTC via `ScheduledBackup`.
- `secret.yaml` — SOPS-encrypted S3 credentials (Cloudflare R2) and DB credentials.

Backups go to Cloudflare R2 buckets (one per app); retention is 14 days.

### infrastructure/ — operators

`infrastructure/base/cloudnativepg/` installs the CloudNativePG operator via a Flux `HelmRelease` (chart `cloudnative-pg` v0.23.0 from its Helm repository). The production overlay in `infrastructure/production/cloudnativepg/` pins this to the cluster namespace `cnpg-system`.

### Secrets management

All `Secret` resources are encrypted in-place with SOPS+AGE. The SOPS rule in `clusters/production/.sops.yaml` encrypts only fields matching `^(data|stringData)$`. The AGE recipient public key is stored in that file; the private key lives only in the cluster as the `sops-age` secret.

Never commit a plaintext `Secret`. Always run `sops --encrypt --in-place <file>` before staging secret files.
