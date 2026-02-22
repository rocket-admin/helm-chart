# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Helm chart for deploying **RocketAdmin** (a web-based database administration tool) on Kubernetes. The chart is published to https://charts.rocketadmin.com via GitHub Pages.

- **Chart path:** `charts/rocketadmin/`
- **Current chart version:** defined in `charts/rocketadmin/Chart.yaml`
- **App version:** tracks upstream `rocketadmin/rocketadmin` Docker image tags

## Common Commands

```bash
# Lint the chart
helm lint ./charts/rocketadmin

# Render templates locally (debug)
helm template my-release ./charts/rocketadmin

# Render with custom values
helm template my-release ./charts/rocketadmin -f charts/rocketadmin/ci/rocketadmin-values.yaml

# Dry-run install (validates against K8s API)
helm install --dry-run --debug my-release ./charts/rocketadmin

# Update dependencies (PostgreSQL subchart)
helm dependency update ./charts/rocketadmin

# Run Helm test (requires a running release)
helm test my-release
```

CI uses [chart-testing](https://github.com/helm/chart-testing) (`ct lint` and `ct install`) with a kind cluster. Test values live in `charts/rocketadmin/ci/rocketadmin-values.yaml`.

## Architecture

### Storage Modes (mutually exclusive)

- **pglite (default since v1.0):** Embedded PostgreSQL using a PVC (10Gi). Forces `Recreate` deployment strategy since pglite is single-writer. Storage mounts at `/app/backend/dist/pglite-storage`.
- **PostgreSQL subchart:** Optional Bitnami PostgreSQL (`postgresql.enabled: true`). Uses the bundled `charts/postgresql-15.5.20.tgz`.

### Secret Management

`templates/secret.yaml` auto-generates JWT_SECRET and PRIVATE_KEY (65-char random strings) on first install. The `gen.secret` helper in `_helpers.tpl` uses Helm `lookup` to preserve existing secrets across upgrades — never regenerates if the secret already exists.

A separate `postgres-secret.yaml` handles PostgreSQL credentials when that subchart is enabled.

### Key Templates

| Template | Conditional on |
|---|---|
| `deployment.yaml` | pglite volumes/strategy when `pglite.enabled` |
| `pvc.yaml` | `pglite.enabled` |
| `postgres-secret.yaml` | `postgresql.enabled` |
| `ingress.yaml` | `ingress.enabled` |
| `hpa.yaml` | `autoscaling.enabled` |
| `secret.yaml` | always |
| `service.yaml` | always |
| `serviceaccount.yaml` | `serviceAccount.create` |

### Template Helpers (`_helpers.tpl`)

Key helpers beyond standard Helm boilerplate:
- `app-domain-address` — resolves domain from `hostname`, first ingress host, or falls back to service name
- `gen.secret` — generates random secret only if not already present in cluster (lookup-based)
- `rocketadmin.dbPassword` — random 16-char password for PostgreSQL

## CI/CD

Three GitHub Actions workflows in `.github/workflows/`:

1. **test.yaml** — Runs on PRs. Lints with `ct lint`, installs in a kind cluster with `ct install`.
2. **release.yaml** — Runs on push to main. Packages and publishes the chart via `chart-releaser-action`.
3. **update-chart-version.yml** — Daily cron (9 AM UTC). Checks upstream RocketAdmin releases, bumps `appVersion` and chart patch version, creates a PR automatically.

## Versioning Convention

Chart version and appVersion are independent. Chart patch version increments when appVersion changes (automated via `update-chart-version.yml`). PRs follow the pattern: "Update RocketAdmin to {version}".
