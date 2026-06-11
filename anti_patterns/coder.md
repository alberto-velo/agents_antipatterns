# Coder Anti-Patterns

Entries @Coder must apply during implementation.

---

## AP-001: State Leakage Through Missing Lifecycle Primitives

```yaml
id: AP-001
title: State Leakage Through Missing Lifecycle Primitives
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [state, implementation, tests]
unit_domains: [agent-infra]
triggers: [missing_reset, missing_teardown, cross_task_state_leak]
summary: Stateful components that persist across task boundaries need deterministic reset or teardown semantics.
```

### Invariant
Stateful storage must provide clean-slate semantics.

### Role Guidance
- @Coder: If the blueprint introduces state without lifecycle semantics, request a blueprint revision.

---

## AP-002: Semantic Permissiveness in Security Metadata

```yaml
id: AP-002
title: Semantic Permissiveness in Security Metadata
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [security, validation, implementation]
triggers: [open_security_type, weak_regex, unbounded_clearance]
summary: Access-control metadata must use closed or constrained types that reject unexpected values.
```

### Invariant
Bare `str` or unbounded `int` fields in access paths allow poisoning.

### Role Guidance
- @Coder: Flag security-critical open types instead of implementing them as-is.

---

## AP-003: Regressive Over-Restriction

```yaml
id: AP-003
title: Regressive Over-Restriction
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [security, validation, implementation]
triggers: [blocked_authorized_input, overstrict_regex, hardening_regression]
summary: Security hardening must preserve all explicitly authorized input shapes.
```

### Invariant
Tightened validation is a regression if it blocks source-law-sanctioned values.

### Role Guidance
- @Coder: Include positive handling for every legitimate input shape listed in the contract.

---

## AP-004: Specification Ambiguity in Transformation Steps

```yaml
id: AP-004
title: Specification Ambiguity in Transformation Steps
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [transformation, serialization, implementation]
triggers: [unspecified_normalization, inferred_serialization, ambiguous_comparison]
summary: Non-trivial transformations must name the exact normalization path before implementation.
```

### Invariant
Different inferred normalization steps can produce different bytes or comparisons.

### Role Guidance
- @Coder: Halt on unspecified transformation paths rather than guessing.

---

## AP-005: Fail-Open Defaults in Security Boundaries

```yaml
id: AP-005
title: Fail-Open Defaults in Security Boundaries
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [security, implementation]
triggers: [fail_open_exception, implicit_allow, malformed_input_acceptance]
summary: Security boundaries must deny malformed or unexpected input by default.
```

### Invariant
Exceptions and default paths in security code must not lead to allow/pass-through.

### Role Guidance
- @Coder: Any security-critical `except` block must return or raise explicit denial.

---

## AP-011: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: AP-011
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [state, implementation, source_law]
triggers: [hidden_state_dependency, false_purity_claim, undeclared_runtime_context]
summary: A function described as pure must not depend on hidden runtime context unless the dependency is explicit.
```

### Invariant
Behavior-changing inputs must be declared.

### Role Guidance
- @Coder: Do not invent hidden lookups to satisfy an argument-driven contract.

---

## AP-012: Fail-Closed Probe Semantics Lost Through Convenience APIs

```yaml
id: AP-012
title: Fail-Closed Probe Semantics Lost Through Convenience APIs
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [filesystem, implementation, security]
unit_domains: [agent-infra]
triggers: [collapsed_probe_error, os_path_exists_misuse, fail_closed_erasure]
summary: Do not replace explicit probes with helpers that collapse I/O failure into false.
```

### Invariant
Missing artifact and failed probe are distinct outcomes when source law says so.

### Role Guidance
- @Coder: Refuse convenience APIs whose failure semantics erase required branches.

---

## AP-013: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: AP-013
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [validation, implementation, source_law]
triggers: [split_validator_semantics, shadow_failure_path, misattributed_failure_literal]
summary: Closed failure alphabets must account for every layer that can reject input.
```

### Invariant
Later schema rejection cannot be hidden behind a misleading canonical literal.

### Role Guidance
- @Coder: Request source-law clarification when validator layers contradict each other.

---

## AP-014: Additive Blueprint Drift From Canonical Source Law

```yaml
id: AP-014
title: Additive Blueprint Drift From Canonical Source Law
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [blueprint, implementation, source_law]
triggers: [extra_alias, narrowed_type, partial_contract_restatement]
summary: Level 4-5 implementations must follow the approved source surface, not helpful blueprint drift.
```

### Invariant
Stricter-looking additions can still be wrong if they diverge from source law.

### Role Guidance
- @Coder: Do not implement undeclared aliases or narrowed types as compatibility shims.

---

## AP-016: Constructor-Based Schema Validation on Untrusted Rows

```yaml
id: AP-016
title: Constructor-Based Schema Validation on Untrusted Rows
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [validation, implementation, schema]
unit_domains: [agent-infra]
triggers: [model_constructor_on_untrusted_input, typeerror_escape, schema_boundary_bypass]
summary: Arbitrary decoded input must use schema validation APIs that preserve declared failure channels.
```

### Invariant
`Model(**row)` can raise host-language exceptions before schema validation.

### Role Guidance
- @Coder: Prefer validation APIs accepting arbitrary objects for untrusted rows.

---

## AP-017: Unreachable Operational Transition From Valid Restart States

```yaml
id: AP-017
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [state, lifecycle, implementation]
unit_domains: [agent-infra]
triggers: [missing_state_transition, unreachable_operational_state, restart_lifecycle_gap]
summary: Do not invent lifecycle unlock behavior missing from source law.
```

### Invariant
Valid restart paths must have explicit operational transition actions.

### Role Guidance
- @Coder: Request policy revision instead of choosing an implicit unlock path.

---

## AP-020: Uncovered Defensive Fallback in Closed Branch Logic

```yaml
id: AP-020
title: Uncovered Defensive Fallback in Closed Branch Logic
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [implementation, branch_logic, tests]
triggers: [missing_defensive_fallback_test, untested_error_literal, total_function_gap]
summary: Blueprint-declared defensive fallback branches must be implemented verbatim.
```

### Invariant
Fallback branches remain normative even if upstream validation usually rejects bad input first.

### Role Guidance
- @Coder: Do not optimize away blueprint-declared fallback branches.

---

## AP-021: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: AP-021
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_authoring, test_audit, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, imports, implementation]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Do not add implementation shims solely to satisfy invented test-side dependency surfaces.
```

### Invariant
Tests do not amend the blueprint or upstream API.

### Role Guidance
- @Coder: Record a discrepancy instead of adding undeclared helpers or aliases.

---

## AP-022: Unproven Operational Side-Effect Invariants

```yaml
id: AP-022
title: Unproven Operational Side-Effect Invariants
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [implementation, tests, side_effects]
triggers: [missing_negative_call_assertion, missing_order_assertion, unproven_side_effect]
summary: Green tests that omit operational side-effect proof do not close the implementation contract.
```

### Invariant
Payload correctness does not prove forbidden calls, ordering, or preserved side effects.

### Role Guidance
- @Coder: Implement the blueprint even when tests prove only a weaker signal.

---

## AP-023: Under-Specified Canonical Closure For Operational Observability

```yaml
id: AP-023
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [adr_draft, policy_audit, blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [implementation, observability, source_law]
unit_domains: [agent-infra]
triggers: [missing_observation_channel, impossible_side_effect, repeated_blueprint_oscillation]
summary: Do not improvise observability mechanisms that the approved contract has not authorized.
```

### Invariant
Mandatory side effects need a legal implementation and observation path.

### Role Guidance
- @Coder: Halt if satisfying a side effect requires undeclared hooks or imports.

---

## AP-025: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: AP-025
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Staff-Engineer, Coder, QA-SDET, Auditor]
steps: [blueprint_draft, blueprint_audit, test_authoring, test_audit, implementation]
risk_levels: [4, 5]
domains: [blueprint, implementation, tests]
unit_domains: [agent-infra]
triggers: [adr_only_test_matrix, transitive_requirement, incomplete_blueprint_handoff]
summary: Coder and QA must be able to execute from the blueprint without chasing ADR-only requirements.
```

### Invariant
Blueprint-bound roles must not discover normative obligations by reading ADR sections.

### Role Guidance
- @Coder: Request blueprint revision when implementation behavior is only referenced by ADR section.

---

## AP-027: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: AP-027
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Staff-Engineer, QA-SDET, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, test_authoring, test_audit, implementation]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports, implementation]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Do not treat an invented test import path as an implicit blueprint amendment.
```

### Invariant
Pre-implementation tests cannot impose an unauthorized public import channel.

### Role Guidance
- @Coder: Implement the blueprint surface and record the binding discrepancy.

---

## AP-028: Non-Falsifying Contract Assertions

```yaml
id: AP-028
title: Non-Falsifying Contract Assertions
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, implementation, cli]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Do not treat weak green tests as proof that the blueprint obligation is satisfied.
```

### Invariant
Implementation must satisfy the blueprint, not merely the weakest passing assertion.

### Role Guidance
- @Coder: Implement the required behavior even if approved tests would miss its absence.

---

## AP-029: Unreachable Mocked Failure Paths

```yaml
id: AP-029
title: Unreachable Mocked Failure Paths
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, implementation, mocks]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: A green test that fails before the patched dependency does not prove downstream failure handling.
```

### Invariant
Mocked branch coverage requires valid preceding inputs and proof the mock was reached.

### Role Guidance
- @Coder: Do not infer downstream failure handling is covered by tests that never reach the dependency.
