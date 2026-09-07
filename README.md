# staging-utils

**Repo:** https://github.com/the-robot-lives/staging-tools

Staging environment lifecycle tools — deploy, tear down, tail logs, and inspect status for the local staging namespace.

## What

Four bash CLI tools installed to `~/.local/bin`:

| Command | Purpose |
|---------|---------|
| `staging-up` | Deploy all staging services (delegates to `helm-upgrade --env stage`) |
| `staging-down` | Uninstall all Helm releases in the staging namespace |
| `staging-logs` | Tail logs for a staging service by name (default: frontend) |
| `staging-status` | Show pods, Helm releases, KEDA ScaledObjects, and resource usage |

## Why

Staging environments need fast spin-up/teardown and debugging without touching production tooling. These wrappers give a consistent, config-driven interface over kubectl/helm for the shared `staging` namespace, used during integration testing before release.

## Getting Started

Prerequisites: `kubectl` with cluster access, `helm`, and `helm-upgrade` (from helm-tools) for `staging-up`.

```bash
make install    # installs staging-* tools to ~/.local/bin (make test to verify)
```

```bash
staging-up                          # Deploy staging environment
staging-down                        # Tear down all staging releases
staging-logs backend                # Tail any service's logs by name
staging-status                      # Full staging dashboard
```

Every tool accepts `--config <path>` to override the config file.

## How It Works

- All tools load settings from `infra-config.yaml` via the shared k8-lib config chain (`~/.local/share/k8-lib/README.md` for setup).
- Relevant keys: `.kubernetes.staging_namespace` (env `K8_STAGING_NAMESPACE`, default `staging`) and `.kubernetes.app_prefix` (env `K8_APP_PREFIX`, default `app`, used for pod label selectors).

### KEDA scale-to-zero

If `staging-logs` reports no pods, KEDA may have scaled the deployment to zero. Run `staging-status` to check ScaledObject state, then curl your staging URL to wake it up.

## Docs

`docs/` carries PROJ-ARCH / PROJ-HOWTO / PROJ-LAYOUT / PROJ-SCHEMA / PROJ-FAQ digests and full docs.
