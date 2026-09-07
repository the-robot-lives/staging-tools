# Project Schema — Data & Config Artifacts

> **No persistence layer.** `staging-utils` is a bash CLI utility package. It defines
> **no database, no SQL schema, no local state files, and no file formats of its own.**
> All state lives in the Kubernetes cluster (Helm releases, pods, KEDA ScaledObjects)
> and in configuration owned by other packages, consumed read-only through the shared
> k8-lib config chain. This doc therefore records the **configuration surface** the
> tools read and the **runtime file locations** they rely on.

## Configuration sources (read-only)

### `infra-config.yaml` (external — infra repo root)

Loaded via k8-lib `config.sh`; every tool accepts `--config <path>` to point at an
alternative file (sets `K8_CONFIG` before k8-lib sourcing).

| YAML Path | Env Override | Default | Used by | Purpose |
|-----------|-------------|---------|---------|---------|
| `.kubernetes.staging_namespace` | `K8_STAGING_NAMESPACE` | `staging` | all tools | Target k8s namespace |
| `.kubernetes.app_prefix` | `K8_APP_PREFIX` | `app` | staging-logs | Label selector prefix (`app.kubernetes.io/name=<prefix>-<service>`) |

### Environment variables

| Variable | Default | Purpose |
|----------|---------|---------|
| `K8_LIB_DIR` | `~/.local/share/k8-lib` | Location of shared k8-lib (`config.sh`, `common.sh`, `assist.sh`) |
| `K8_CONFIG` | unset | Alternative config file path (set via `--config`) |
| `INFRA_ROOT` | `$(pwd)` | Infra repo root (convention; reserved for k8-lib resolution) |
| `K8_STAGING_NAMESPACE` | `staging` | Namespace override |
| `K8_APP_PREFIX` | `app` | Service label prefix override |

Secrets are **not** handled by this package; cluster credentials come from the
ambient `kubectl`/`helm` context (upstream k8-lib / Infisical flow).

### CLI argument convention

- `--config <path>` or `--config=<path>` — shared by all tools, pre-parsed into `K8_CONFIG`
- `staging-logs [frontend|backend|<service>]` — first non-flag argument selects the service label
- `--assist` — AI help hook via k8-lib `assist.sh` (`_k8_check_assist`)

## Runtime file locations

| Path | Producer | Role |
|------|----------|------|
| `~/.local/bin/staging-{up,down,logs,status}` | `make install` | Installed executables |
| `~/.local/share/k8-lib/` | k8-lib package (`make install-utilities`) | Shared config/common/assist libraries |
| `~/.local/bin/helm-upgrade` | sibling `helm-tools` package | Required by `staging-up` |

## External state (not owned here, read/written via kubectl/helm)

| Object | Written by | Read by |
|--------|-----------|---------|
| Helm releases in staging namespace | `staging-up` (deploy), `staging-down` (uninstall) | `staging-down`, `staging-status` |
| Pods / labels `app.kubernetes.io/name=<prefix>-<service>` | `staging-up` → helm | `staging-logs`, `staging-status` |
| KEDA ScaledObjects | cluster/KEDA controller | `staging-status` |

ERD diagrams: N/A — no relational model exists.
