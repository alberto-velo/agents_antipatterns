# Auditor Anti-Patterns

Entries @Auditor must apply when reviewing source law, blueprints, tests, and code.

---

## AP-001: State Leakage Through Missing Lifecycle Primitives

```yaml
id: AP-001
title: State Leakage Through Missing Lifecycle Primitives
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [state, tests, code]
unit_domains: [agent-infra]
triggers: [missing_reset, missing_teardown, cross_task_state_leak]
summary: Stateful components need deterministic reset or teardown semantics and clean-slate proof.
```

### Invariant
Persistent task-boundary state must not leak between executions.

### Role Guidance
- @Auditor: For stateful components, verify the lifecycle primitive and clean-slate coverage.

---

## AP-002: Semantic Permissiveness in Security Metadata

```yaml
id: AP-002
title: Semantic Permissiveness in Security Metadata
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [security, validation]
triggers: [open_security_type, weak_regex, unbounded_clearance]
summary: Access-control metadata must reject unexpected values through closed or constrained types.
```

### Invariant
Open access-control types can let arbitrary values bypass validation.

### Role Guidance
- @Auditor: Open types in access paths are blocker-grade at Level 4-5.

---

## AP-003: Regressive Over-Restriction

```yaml
id: AP-003
title: Regressive Over-Restriction
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [security, validation]
triggers: [blocked_authorized_input, overstrict_regex, hardening_regression]
summary: Security hardening that blocks source-law-authorized behavior is a regression.
```

### Invariant
Hardening must account for all legitimate input shapes.

### Role Guidance
- @Auditor: Cross-check tightened validation against every authorized input class.

---

## AP-004: Specification Ambiguity in Transformation Steps

```yaml
id: AP-004
title: Specification Ambiguity in Transformation Steps
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [blueprint, transformation]
triggers: [unspecified_normalization, inferred_serialization, ambiguous_comparison]
summary: Non-trivial transformations must be explicitly specified before implementation is auditable.
```

### Invariant
Inferred normalization can produce divergent byte sequences or comparisons.

### Role Guidance
- @Auditor: Treat inferred transformation logic as a spec defect when the exact path matters.

---

## AP-005: Fail-Open Defaults in Security Boundaries

```yaml
id: AP-005
title: Fail-Open Defaults in Security Boundaries
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [security, code]
triggers: [fail_open_exception, implicit_allow, malformed_input_acceptance]
summary: Security-boundary malformed-input paths must fail closed, not return allow or pass-through.
```

### Invariant
Exception/default paths in security logic must explicitly deny.

### Role Guidance
- @Auditor: Search for `except` and default return paths that can accept malformed input.

---

## AP-006: Import Masking in Test Suites

```yaml
id: AP-006
title: Import Masking in Test Suites
roles: [QA-SDET, Auditor]
steps: [test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports]
unit_domains: [agent-infra]
triggers: [import_masking, false_green_tests, hidden_import_failure]
summary: Tests must fail loudly when implementation imports are missing or broken.
```

### Invariant
Masked imports create false green test signals.

### Role Guidance
- @Auditor: Reject test suites that wrap core implementation imports in fallback logic.

---

## AP-007: Canonical Artifact Path Drift

```yaml
id: AP-007
title: Canonical Artifact Path Drift
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, workflow, manifest]
unit_domains: [agent-infra]
triggers: [missing_canonical_artifact, path_drift, ambiguous_entrypoint]
summary: Handoff artifacts must live at canonical paths or expose deterministic entrypoints.
```

### Invariant
Review must not depend on filesystem discovery.

### Role Guidance
- @Auditor: Treat ambiguous artifact location as a traceability defect.

---

## AP-008: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: AP-008
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests]
triggers: [placeholder_test, uncollected_test, empty_assertion]
summary: Placeholder, skipped, or uncollected tests are missing coverage, not partial proof.
```

### Invariant
Coverage exists only when collected executable assertions run.

### Role Guidance
- @Auditor: Treat `pass`, TODO-only bodies, and nested uncollected tests as missing coverage.

---

## AP-009: Test Contract Invention

```yaml
id: AP-009
title: Test Contract Invention
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [test_audit]
risk_levels: [3, 4, 5]
domains: [tests, source_law]
triggers: [invented_error_literal, invented_route, unsourced_expected_output]
summary: Tests must not enforce literals or dependency surfaces absent from the approved contract.
```

### Invariant
Invented test expectations validate local guesses instead of source law.

### Role Guidance
- @Auditor: Compare asserted literals and patch targets to approved surfaces.

---

## AP-010: Partial Assertion of Structured Error Contracts

```yaml
id: AP-010
title: Partial Assertion of Structured Error Contracts
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [test_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, errors]
triggers: [partial_error_assertion, incomplete_payload_check, structured_exception_drift]
summary: Structured error contracts require assertions on every contract-bearing field.
```

### Invariant
Partial assertions can allow payload drift to pass.

### Role Guidance
- @Auditor: Treat subset-only checks as insufficient when payload shape is normative.

---

## AP-011: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: AP-011
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [policy_audit, blueprint_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [source_law, state]
triggers: [hidden_state_dependency, false_purity_claim, undeclared_runtime_context]
summary: Pure or argument-driven interfaces must not hide behavior-changing ambient dependencies.
```

### Invariant
Purity claims must match actual runtime dependencies.

### Role Guidance
- @Auditor: Cross-check interface claims against pseudocode and implementation inputs.

---

## AP-012: Fail-Closed Probe Semantics Lost Through Convenience APIs

```yaml
id: AP-012
title: Fail-Closed Probe Semantics Lost Through Convenience APIs
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [filesystem, security]
unit_domains: [agent-infra]
triggers: [collapsed_probe_error, os_path_exists_misuse, fail_closed_erasure]
summary: Convenience filesystem APIs must not collapse required probe-failure semantics.
```

### Invariant
Missing and failed probes are distinct when source law says so.

### Role Guidance
- @Auditor: Verify named operations can emit every required error branch.

---

## AP-013: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: AP-013
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [policy_audit, blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [validation, source_law]
triggers: [split_validator_semantics, shadow_failure_path, misattributed_failure_literal]
summary: Closed failure alphabets must account for every validation layer that can reject input.
```

### Invariant
Later-layer rejection cannot be mislabeled as an unrelated canonical cause.

### Role Guidance
- @Auditor: Compare every validation layer, not just the first validator.

---

## AP-014: Additive Blueprint Drift From Canonical Source Law

```yaml
id: AP-014
title: Additive Blueprint Drift From Canonical Source Law
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [blueprint, source_law]
triggers: [extra_alias, narrowed_type, partial_contract_restatement]
summary: Level 4-5 blueprint additions can be fidelity defects even when stricter-looking.
```

### Invariant
The blueprint must remain the same contract as source law.

### Role Guidance
- @Auditor: Treat unauthorized aliases, narrowings, and partial restatements as drift.

---

## AP-015: State-Matrix Coverage Collapse Through Subcomponent-Only Testing

```yaml
id: AP-015
title: State-Matrix Coverage Collapse Through Subcomponent-Only Testing
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [test_audit]
risk_levels: [4, 5]
domains: [tests, state]
triggers: [helper_only_coverage, missing_entrypoint_row, state_matrix_gap]
summary: Closed state matrix rows must be tested at the owning entrypoint.
```

### Invariant
Helper tests do not prove entrypoint payload mapping.

### Role Guidance
- @Auditor: Verify every published row is exercised where the visible payload is produced.

---

## AP-016: Constructor-Based Schema Validation on Untrusted Rows

```yaml
id: AP-016
title: Constructor-Based Schema Validation on Untrusted Rows
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [validation, schema, code]
unit_domains: [agent-infra]
triggers: [model_constructor_on_untrusted_input, typeerror_escape, schema_boundary_bypass]
summary: Arbitrary decoded input must use schema APIs that preserve declared validation failures.
```

### Invariant
Constructor forms can leak host-language exceptions outside declared channels.

### Role Guidance
- @Auditor: Search for `Model(**row)` around untrusted decoded data.

---

## AP-017: Unreachable Operational Transition From Valid Restart States

```yaml
id: AP-017
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [policy_audit, blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [state, lifecycle]
unit_domains: [agent-infra]
triggers: [missing_state_transition, unreachable_operational_state, restart_lifecycle_gap]
summary: Valid restart branches must be able to reach the declared operational state.
```

### Invariant
Every successful bootstrap path needs an explicit transition action.

### Role Guidance
- @Auditor: Cross-check bootstrap matrices against lifecycle transitions.

---

## AP-018: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: AP-018
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [test_audit]
risk_levels: [4, 5]
domains: [tests, module_surface]
triggers: [mocked_public_symbol, untested_public_api, closed_surface_gap]
summary: Every closed-surface public symbol needs direct executable coverage.
```

### Invariant
Mocking a public symbol leaves that public contract unverified.

### Role Guidance
- @Auditor: Compare the blueprint public symbol table against direct call sites.

---

## AP-019: Control-Plane Marker Leakage Into Executable Artifacts

```yaml
id: AP-019
title: Control-Plane Marker Leakage Into Executable Artifacts
roles: [QA-SDET, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, code, workflow]
unit_domains: [agent-infra]
triggers: [raw_agent_marker, raw_verdict_marker, syntax_breaking_metadata]
summary: Raw workflow control markers in executable files are syntax-breaking artifact leakage.
```

### Invariant
Protocol tags belong in chat or trace artifacts, not raw executable content.

### Role Guidance
- @Auditor: Scan executable artifact boundaries for raw `[AGENT:]` or `[VERDICT:]`.

---

## AP-020: Uncovered Defensive Fallback in Closed Branch Logic

```yaml
id: AP-020
title: Uncovered Defensive Fallback in Closed Branch Logic
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [4, 5]
domains: [tests, branch_logic]
triggers: [missing_defensive_fallback_test, untested_error_literal, total_function_gap]
summary: Blueprint-declared defensive fallbacks need direct tests and verbatim implementation.
```

### Invariant
Terminal fail-closed branches are contract-bearing even if rare.

### Role Guidance
- @Auditor: Compare the full branch set against the test matrix and code.

---

## AP-021: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: AP-021
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, imports, dependencies]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Tests must not bind to undeclared constructors, hooks, helpers, or dependency surfaces.
```

### Invariant
Green tests can still be invalid if they require drifted implementation surfaces.

### Role Guidance
- @Auditor: Compare patched targets and fixture constructors against approved surfaces.

---

## AP-022: Unproven Operational Side-Effect Invariants

```yaml
id: AP-022
title: Unproven Operational Side-Effect Invariants
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [4, 5]
domains: [tests, side_effects, observability]
triggers: [missing_negative_call_assertion, missing_order_assertion, unproven_side_effect]
summary: Operational side-effect obligations require direct observable assertions.
```

### Invariant
Payload-only testing does not prove forbidden calls, preservation, or ordering.

### Role Guidance
- @Auditor: Distinguish payload coverage from operational-invariant coverage.

---

## AP-023: Under-Specified Canonical Closure For Operational Observability

```yaml
id: AP-023
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [policy_audit, blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [source_law, observability]
unit_domains: [agent-infra]
triggers: [missing_observation_channel, impossible_side_effect, repeated_blueprint_oscillation]
summary: Required operational side effects need a legal implementation and observation path.
```

### Invariant
Repeated downstream oscillation can indicate insufficient source law.

### Role Guidance
- @Auditor: Reassess upstream sufficiency when fixes oscillate around the same obligation.

---

## AP-024: Source-Law Drift From Frozen Upstream APIs And Reachability Closure

```yaml
id: AP-024
title: Source-Law Drift From Frozen Upstream APIs And Reachability Closure
roles: [Architect, Staff-Engineer, QA-SDET, Auditor]
steps: [policy_audit, blueprint_audit, test_audit]
risk_levels: [3, 4, 5]
domains: [source_law, imports, tests]
unit_domains: [agent-infra]
triggers: [nonexistent_upstream_import, contradictory_reachability, impossible_test_row]
summary: Source law must use shipped upstream APIs and require only reachable rows.
```

### Invariant
Contradictory or nonexistent upstream contracts are upstream defects.

### Role Guidance
- @Auditor: Do not route QA/Coder for faithfully following contradictory source law.

---

## AP-025: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: AP-025
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Staff-Engineer, Coder, QA-SDET, Auditor]
steps: [blueprint_audit, test_audit, code_audit]
risk_levels: [4, 5]
domains: [blueprint, tests, implementation]
unit_domains: [agent-infra]
triggers: [adr_only_test_matrix, transitive_requirement, incomplete_blueprint_handoff]
summary: Level 4-5 blueprints must be self-contained for Blueprint-bound downstream roles.
```

### Invariant
Downstream roles should not read ADR sections to discover normative details.

### Role Guidance
- @Auditor: Treat transitive ADR dependencies as handoff-closure defects.

---

## AP-026: Unprobed Framework Error Literals In Source Law

```yaml
id: AP-026
title: Unprobed Framework Error Literals In Source Law
roles: [Architect, Staff-Engineer, QA-SDET, Auditor]
steps: [policy_audit, blueprint_audit, test_audit]
risk_levels: [4, 5]
domains: [source_law, framework, tests]
unit_domains: [agent-infra]
triggers: [unverified_framework_literal, unreachable_diagnostic, pydantic_error_drift]
summary: Exact framework diagnostics made normative must be probed against runtime.
```

### Invariant
Framework diagnostics are empirical runtime observables.

### Role Guidance
- @Auditor: Probe exact framework literals before approving source law that requires them.

---

## AP-027: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: AP-027
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Staff-Engineer, QA-SDET, Coder, Auditor]
steps: [blueprint_audit, test_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports, blueprint]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Pre-implementation tests must bind only through blueprint-approved import channels.
```

### Invariant
Tests cannot invent public module paths.

### Role Guidance
- @Auditor: Compare test imports against the approved blueprint binding.

---

## AP-028: Non-Falsifying Contract Assertions

```yaml
id: AP-028
title: Non-Falsifying Contract Assertions
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, code, cli, workflow]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Tests must fail against plausible implementations that omit required behavior.
```

### Invariant
Weak assertions are missing coverage, not partial coverage.

### Role Guidance
- @Auditor: Ask whether each required test would fail if the behavior were removed.

---

## AP-029: Unreachable Mocked Failure Paths

```yaml
id: AP-029
title: Unreachable Mocked Failure Paths
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [test_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, mocks, cli]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: Mocked error-path tests must prove the patched dependency was reached.
```

### Invariant
Earlier validation failure can masquerade as downstream branch coverage.

### Role Guidance
- @Auditor: Require mock reachability or a unique patched sentinel in asserted output.
