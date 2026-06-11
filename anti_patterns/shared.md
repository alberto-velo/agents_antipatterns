# Shared Anti-Patterns

Cross-role entries that protect workflow integrity across multiple agents.

---

## shared.canonical-artifact-path-drift: Canonical Artifact Path Drift

```yaml
id: shared.canonical-artifact-path-drift
title: Canonical Artifact Path Drift
roles: [Staff-Engineer, Coder, Auditor]
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
- @Staff-Engineer: Store blueprints under the canonical unit directory.
- @Coder: Do not implement from discovered-by-search blueprint fragments.
- @Auditor: Treat review-by-filesystem-discovery as a traceability defect.

---

## shared.control-plane-marker-in-executable-artifact: Control-Plane Marker Leakage Into Executable Artifacts

```yaml
id: shared.control-plane-marker-in-executable-artifact
title: Control-Plane Marker Leakage Into Executable Artifacts
roles: [QA-SDET, Coder, Auditor]
steps: [test_authoring, test_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, code, workflow]
unit_domains: [agent-infra]
triggers: [raw_agent_marker, raw_verdict_marker, syntax_breaking_metadata]
summary: Workflow headers and verdict tags must not appear as raw executable source content.
```

### Invariant
Protocol tags such as `[AGENT: ...]` and `[VERDICT: ...]` belong in chat or
trace artifacts, not as raw top-level code/test content.

### Role Guidance
- @QA-SDET: Never paste protocol markers directly into `.py` test files.
- @Coder: Treat handed-off executable files with raw markers as broken artifacts.
- @Auditor: Scan executable artifact boundaries for syntax-breaking markers.
