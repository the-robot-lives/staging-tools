# AGENT-README

> Orientation for AI coding agents working in this repo. Human docs: README.md.

## Type

scripts+cli-tool

## Purpose

Staging-environment lifecycle tools — `staging-up` (deploy via `helm-upgrade --env stage`), `staging-down` (uninstall Helm releases), `staging-logs` (tail a service), `staging-status` (pods, releases, KEDA ScaledObjects, resources) for the shared `staging` namespace.

## Stack

Bash CLIs installed to `~/.local/bin`; shared k8-lib config chain over `infra-config.yaml`; depends on kubectl cluster access, helm, and `helm-upgrade` (from helm-tools).

## Dev mode

```bash
make install     # installs staging-* tools to ~/.local/bin
staging-up       # deploy staging
staging-status   # full staging dashboard
```

## Ports / URLs

- none (library/CLI)

## Tests

```bash
make test
```

## Gotchas

- KEDA scale-to-zero: `staging-logs` reporting no pods may just mean scaled to zero — check `staging-status`, then curl the staging URL to wake it.
- Config keys: `.kubernetes.staging_namespace` (env `K8_STAGING_NAMESPACE`, default `staging`) and `.kubernetes.app_prefix` (env `K8_APP_PREFIX`, default `app`) from `infra-config.yaml`; every tool accepts `--config <path>`.
