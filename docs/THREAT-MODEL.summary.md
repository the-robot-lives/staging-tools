# Threat Model — Summary

Four local CLI wrappers over kubectl/helm; no secrets, no state, no listeners of
its own. Risk is borrowed authority: ambient kubeconfig credentials and runtime-
sourced k8-lib code.

Trust boundaries: workstation ↔ cluster API (ambient creds) · workstation ↔
k8-lib (sourced shell, `K8_LIB_DIR`) · config ↔ target selection (namespace not
validated) · cluster log plane ↔ terminal (unfiltered stream).

Register: T-001 `staging-down` namespace sweep, no confirmation (High) ·
T-002 tampered k8-lib runs with cluster creds (Medium) · T-003 unfiltered log
disclosure (Medium) · T-004 PATH-resolved `helm-upgrade` exec (Medium) ·
T-005 no audit trail (Low) · T-006 `--force` redeploy DoS (Low).

All six open and accepted as staging-grade trade-offs (2026-10-26); no Critical
findings. Full detail: `docs/THREAT-MODEL.md`.
