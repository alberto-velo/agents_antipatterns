# Recorder Anti-Patterns

Entries @Recorder must apply when updating digests, indexes, and registry history.

---

## AP-007: Canonical Artifact Path Drift

```yaml
id: AP-007
title: Canonical Artifact Path Drift
roles: [Recorder, Auditor]
steps: [digest_update, code_audit, blueprint_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [governance, manifest, workflow]
unit_domains: [agent-infra]
triggers: [missing_canonical_artifact, path_drift, ambiguous_entrypoint]
summary: Final status updates must point to canonical unit-scoped artifacts, not discovered fragments.
```

### Invariant
Completion history must preserve deterministic links to the operative artifacts.

### Role Guidance
- @Recorder: Reference only canonical versioned traces, blueprints, manifests, and indexes.

---

## AP-023: Under-Specified Canonical Closure For Operational Observability

```yaml
id: AP-023
title: Under-Specified Canonical Closure For Operational Observability
roles: [Recorder, Auditor]
steps: [digest_update, blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [governance, observability, source_law]
unit_domains: [agent-infra]
triggers: [repeated_blueprint_oscillation, unresolved_root_cause, escalation_needed]
summary: Repeated same-obligation loops should be recorded as evidence of upstream closure risk.
```

### Invariant
Repeated downstream oscillation is workflow evidence, not just retry count.

### Role Guidance
- @Recorder: Preserve only the final reusable lesson or governance breach, not a full retry narrative.

---

## AP-028: Non-Falsifying Contract Assertions

```yaml
id: AP-028
title: Non-Falsifying Contract Assertions
roles: [Recorder, Auditor]
steps: [digest_update, test_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [governance, tests]
triggers: [weak_assertion, repeated_qa_failure, anti_pattern_needed]
summary: Recorder flags missing Auditor anti-pattern updates but does not author registry entries.
```

### Invariant
Ordinary one-off missing assertions belong in audit reports, not the registry, and registry authoring remains Auditor-owned.

### Role Guidance
- @Recorder: Flag missing required Auditor updates as governance breaches; do not add or edit anti-pattern entries.
