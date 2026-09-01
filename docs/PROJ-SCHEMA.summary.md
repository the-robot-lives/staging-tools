# Project Schema — Summary

**No persistence layer** — bash CLI package; no DB/SQL, no local state files, no owned file formats. Cluster state (Helm releases, pods, KEDA ScaledObjects) owned by k8s.

Config surface (read-only):

- **Config file**: `infra-config.yaml` (external, k8-lib chain; `--config <path>` overrides)
  - `.kubernetes.staging_namespace` (env `K8_STAGING_NAMESPACE`, default `staging`)
  - `.kubernetes.app_prefix` (env `K8_APP_PREFIX`, default `app`)
- **Env vars**: `K8_LIB_DIR` (default `~/.local/share/k8-lib`), `K8_CONFIG`, `INFRA_ROOT`, plus the two above
- **Secrets**: none handled here — ambient kubectl/helm context
- **Installed files**: `~/.local/bin/staging-*` (via `make install`); deps: `~/.local/share/k8-lib`, `helm-upgrade`

ERD: N/A.
