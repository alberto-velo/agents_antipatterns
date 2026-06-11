# Architect Anti-Patterns

Entries @Architect must apply when drafting source law or classifying risk.

---

## AP-011: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: AP-011
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, code_audit]
risk_levels: [3, 4, 5]
domains: [source_law, policy, state]
triggers: [hidden_state_dependency, false_purity_claim, undeclared_runtime_context]
summary: A function described as pure must not depend on hidden runtime context unless the dependency is explicit in source law.
```

### Invariant
Every behavior-changing input must appear in the contract or be explicitly
modeled as an ambient dependency.

### Role Guidance
- @Architect: Put runtime context such as tier, mode, or phase in the interface or declare the ambient dependency.

---

## AP-013: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: AP-013
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, code_audit]
risk_levels: [4, 5]
domains: [source_law, policy, validation]
triggers: [split_validator_semantics, shadow_failure_path, misattributed_failure_literal]
summary: A closed failure alphabet must account for every validation layer that can reject input.
```

### Invariant
If source law names a canonical validator, later validation layers must be
acceptance-equivalent or map stricter failures truthfully.

### Role Guidance
- @Architect: Specify whether later validators are representational only or may reject additional inputs.

---

## AP-017: Unreachable Operational Transition From Valid Restart States

```yaml
id: AP-017
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [source_law, policy, state]
unit_domains: [agent-infra]
triggers: [missing_state_transition, unreachable_operational_state, restart_lifecycle_gap]
summary: Every successful bootstrap path must define how the system reaches its declared operational state.
```

### Invariant
Each valid startup/restart path that yields a live snapshot must define the
transition out of deny-all startup mode.

### Role Guidance
- @Architect: Enumerate every successful transition source, not only first-boot behavior.

---

## AP-023: Under-Specified Canonical Closure For Operational Observability

```yaml
id: AP-023
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [source_law, policy, observability]
unit_domains: [agent-infra]
triggers: [missing_observation_channel, impossible_side_effect, repeated_blueprint_oscillation]
summary: High-risk side effects need both an authorized implementation path and an authorized observation path.
```

### Invariant
Logging, alerting, audit, or ordering requirements must name a legal mechanism
that satisfies all other surface constraints.

### Role Guidance
- @Architect: Close the implementation and observation paths, not just the desired outcome.

---

## AP-024: Source-Law Drift From Frozen Upstream APIs And Reachability Closure

```yaml
id: AP-024
title: Source-Law Drift From Frozen Upstream APIs And Reachability Closure
roles: [Architect, Staff-Engineer, QA-SDET, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, test_authoring, test_audit]
risk_levels: [3, 4, 5]
domains: [source_law, imports, tests]
unit_domains: [agent-infra]
triggers: [nonexistent_upstream_import, contradictory_reachability, impossible_test_row]
summary: Source law that composes frozen units must use shipped APIs and require only reachable test rows.
```

### Invariant
Frozen dependency paths and reachability obligations must match the shipped
upstream surface and the unit's own ordering rules.

### Role Guidance
- @Architect: Verify every consumed symbol against actual upstream exports and reconcile row reachability.

---

## AP-026: Unprobed Framework Error Literals In Source Law

```yaml
id: AP-026
title: Unprobed Framework Error Literals In Source Law
roles: [Architect, Staff-Engineer, QA-SDET, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, test_authoring, test_audit]
risk_levels: [4, 5]
domains: [source_law, framework, tests]
unit_domains: [agent-infra]
triggers: [unverified_framework_literal, unreachable_diagnostic, pydantic_error_drift]
summary: Exact framework diagnostics made normative must be empirically verified against the project runtime.
```

### Invariant
Framework-specific error codes, locations, and payloads are runtime facts, not
deductions from annotations.

### Role Guidance
- @Architect: Probe exact framework observables or mark them non-normative.
