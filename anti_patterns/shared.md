# Shared Anti-Patterns

Cross-role entries that protect workflow integrity across multiple agents.

---

## shared.canonical-artifact-path-drift: Canonical Artifact Path Drift

```yaml
id: shared.canonical-artifact-path-drift
title: Canonical Artifact Path Drift
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, workflow, manifest]
unit_domains: [agent-infra]
triggers: [missing_canonical_artifact, path_drift, ambiguous_entrypoint]
summary: Workflow artifacts must live at the canonical handoff path or provide a deterministic entrypoint.
```

### Invariant
Pipeline artifacts that serve as handoff contracts must be discoverable at a
single canonical path.

### Role Guidance
- @Architect: Store blueprints under the canonical unit directory.
- @Coder: Do not implement from discovered-by-search blueprint fragments.
- @Auditor: Treat review-by-filesystem-discovery as a traceability defect.

