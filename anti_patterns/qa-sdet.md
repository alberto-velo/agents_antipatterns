# QA-SDET Anti-Patterns

Entries @QA-SDET must apply when authoring or executing tests.

---

## qa-sdet.import-masking-hides-failure: Import Masking in Test Suites

```yaml
id: qa-sdet.import-masking-hides-failure
title: Import Masking in Test Suites
roles: [QA-SDET, Auditor]
steps: [test_authoring, test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports]
unit_domains: [agent-infra]
triggers: [import_masking, false_green_tests, hidden_import_failure]
summary: Tests must fail loudly when implementation imports are missing or broken.
```

### Invariant
Core implementation imports must not be wrapped in `try/except ImportError`.

### Role Guidance
- @QA-SDET: Use direct imports so missing implementation fails collection.

---

## shared.ghost-coverage-placeholder-tests: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: shared.ghost-coverage-placeholder-tests
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [QA-SDET, Architect, Auditor]
steps: [test_authoring, test_audit, blueprint_draft]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, blueprint]
triggers: [placeholder_test, uncollected_test, empty_assertion]
summary: Tests only count when executable assertions are collected and exercise the claimed contract.
```

### Invariant
`pass`, TODO-only bodies, comments, skipped placeholders, and nested uncollected
tests are missing coverage.

### Role Guidance
- @QA-SDET: Map every required public-surface or mandatory assertion item to an executable assertion.

---

## qa-sdet.test-invents-unlicensed-contract: Test Contract Invention

```yaml
id: qa-sdet.test-invents-unlicensed-contract
title: Test Contract Invention
roles: [QA-SDET, Architect, Auditor]
steps: [test_authoring, test_audit, blueprint_draft]
risk_levels: [3, 4, 5]
domains: [tests, blueprint, source_law]
triggers: [invented_error_literal, invented_route, unsourced_expected_output]
summary: Tests must derive asserted literals and branches from the approved contract, not local guesses.
```

### Invariant
High-risk tests validate source law only when asserted outcomes trace back to
the blueprint or approved upstream behavior.

### Role Guidance
- @QA-SDET: Do not invent error codes, route strings, helper names, or contract literals.

---

## qa-sdet.partial-structured-error-assertion: Partial Assertion of Structured Error Contracts

```yaml
id: qa-sdet.partial-structured-error-assertion
title: Partial Assertion of Structured Error Contracts
roles: [QA-SDET, Architect, Auditor]
steps: [test_authoring, test_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, errors]
triggers: [partial_error_assertion, incomplete_payload_check, structured_exception_drift]
summary: When the contract defines structured errors, tests must assert all contract-bearing fields.
```

### Invariant
Checking one convenient field can let non-verbatim error translations pass.

### Role Guidance
- @QA-SDET: Assert every field or argument that distinguishes compliance from partial implementation.

---

## qa-sdet.state-matrix-subcomponent-only-coverage: State-Matrix Coverage Collapse Through Subcomponent-Only Testing

```yaml
id: qa-sdet.state-matrix-subcomponent-only-coverage
title: State-Matrix Coverage Collapse Through Subcomponent-Only Testing
roles: [QA-SDET, Architect, Auditor]
steps: [test_authoring, test_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [tests, state]
triggers: [helper_only_coverage, missing_entrypoint_row, state_matrix_gap]
summary: Closed state matrix rows must be tested at the owning public entrypoint.
```

### Invariant
Helper-level validation does not prove the entrypoint emits the required
state-bearing payload.

### Role Guidance
- @QA-SDET: Add entrypoint-level tests for every externally observable state row.

---

## shared.mocked-composition-misses-closed-surface: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: shared.mocked-composition-misses-closed-surface
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [QA-SDET, Architect, Auditor]
steps: [test_authoring, test_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [tests, module_surface]
triggers: [mocked_public_symbol, untested_public_api, closed_surface_gap]
summary: Every public symbol in a closed surface needs direct executable coverage.
```

### Invariant
An entrypoint test that mocks a public dependency does not verify that mocked
symbol's own contract.

### Role Guidance
- @QA-SDET: Build a public-symbol checklist before expanding into matrix coverage.

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
Protocol tags belong in chat or trace artifacts unless represented as valid
language comments.

### Role Guidance
- @QA-SDET: Never paste raw agent protocol headers or verdict footers into test modules.

---

## shared.defensive-fallback-branch-omitted: Uncovered Defensive Fallback in Closed Branch Logic

```yaml
id: shared.defensive-fallback-branch-omitted
title: Uncovered Defensive Fallback in Closed Branch Logic
roles: [QA-SDET, Architect, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [tests, branch_logic]
triggers: [missing_defensive_fallback_test, untested_error_literal, total_function_gap]
summary: Blueprint-declared defensive fallback branches need direct runtime-reachable tests.
```

### Invariant
Happy-path and named-branch coverage do not prove a terminal fail-closed branch
still exists.

### Role Guidance
- @QA-SDET: Reach the fallback with invalid input and assert the exact literal or payload.

---

## shared.test-invents-noncanonical-dependency: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: shared.test-invents-noncanonical-dependency
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [QA-SDET, Architect, Coder, Auditor]
steps: [test_authoring, test_audit, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, imports, dependencies]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Tests must bind to approved or shipped dependency surfaces, not invented local conveniences.
```

### Invariant
Tests that require undeclared helpers or compressed constructor shapes pressure
implementation to drift.

### Role Guidance
- @QA-SDET: Instantiate upstream types and patch side-effect channels exactly as declared.

---

## shared.side-effect-invariant-unproven: Unproven Operational Side-Effect Invariants

```yaml
id: shared.side-effect-invariant-unproven
title: Unproven Operational Side-Effect Invariants
roles: [QA-SDET, Architect, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [tests, side_effects, observability]
triggers: [missing_negative_call_assertion, missing_order_assertion, unproven_side_effect]
summary: Operational side-effect obligations need direct spy, ordering, or negative-call assertions.
```

### Invariant
Returned payload coverage does not prove forbidden calls, preserved flags, or
alert ordering.

### Role Guidance
- @QA-SDET: Assert side effects directly instead of inferring them from return values.

---

## shared.source-law-uses-nonexistent-upstream-api: Source-Law Drift From Frozen Upstream APIs And Reachability Closure

```yaml
id: shared.source-law-uses-nonexistent-upstream-api
title: Source-Law Drift From Frozen Upstream APIs And Reachability Closure
roles: [Architect, QA-SDET, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [3, 4, 5]
domains: [source_law, imports, tests]
unit_domains: [agent-infra]
triggers: [nonexistent_upstream_import, contradictory_reachability, impossible_test_row]
summary: Source law that composes frozen units must use shipped APIs and require only reachable test rows.
```

### Invariant
QA must not invent compatibility shims for impossible imports or unreachable rows.

### Role Guidance
- @QA-SDET: Treat impossible imports and contradictory reachability as upstream defects.

---

## shared.blueprint-missing-transitive-source-law: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: shared.blueprint-missing-transitive-source-law
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Architect, Coder, QA-SDET, Auditor]
steps: [blueprint_draft, blueprint_audit, test_authoring, test_audit, implementation]
risk_levels: [4, 5]
domains: [blueprint, tests, implementation]
unit_domains: [agent-infra]
triggers: [out_of_blueprint_requirement, transitive_requirement, incomplete_blueprint_handoff]
summary: Blueprint-bound roles must not need documents outside the blueprint to discover normative test or implementation details.
```

### Invariant
A Level 4-5 blueprint must be self-contained for Coder and QA.

### Role Guidance
- @QA-SDET: Halt rather than deriving tests from out-of-blueprint matrices or literals.

---

## shared.unprobed-framework-error-literal: Unprobed Framework Error Literals In Source Law

```yaml
id: shared.unprobed-framework-error-literal
title: Unprobed Framework Error Literals In Source Law
roles: [Architect, QA-SDET, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [4, 5]
domains: [source_law, framework, tests]
unit_domains: [agent-infra]
triggers: [unverified_framework_literal, unreachable_diagnostic, pydantic_error_drift]
summary: Exact framework diagnostics made normative must be empirically verified against the project runtime.
```

### Invariant
Framework diagnostic literals must be probed before QA is asked to assert them.

### Role Guidance
- @QA-SDET: Test exact framework diagnostics verbatim, but route runtime mismatch as source-law defect.

---

## shared.unanchored-import-surface-preimpl-tests: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: shared.unanchored-import-surface-preimpl-tests
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Architect, QA-SDET, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, test_authoring, test_audit, implementation]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports, blueprint]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Pre-implementation tests need a blueprint-approved import path or binding mechanism.
```

### Invariant
QA cannot write executable public-surface tests without an approved import channel.

### Role Guidance
- @QA-SDET: Do not invent module paths; record the blocker and route upstream.

---

## shared.non-falsifying-contract-assertion: Non-Falsifying Contract Assertions

```yaml
id: shared.non-falsifying-contract-assertion
title: Non-Falsifying Contract Assertions
roles: [QA-SDET, Architect, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, cli, workflow]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Tests for mandatory assertions must fail against plausible implementations that omit the behavior.
```

### Invariant
Asserting that something returned or still exists is not contract coverage.

### Role Guidance
- @QA-SDET: Name the observable signal and the plausible broken implementation each test catches.

---

## shared.unreachable-mocked-failure-path: Unreachable Mocked Failure Paths

```yaml
id: shared.unreachable-mocked-failure-path
title: Unreachable Mocked Failure Paths
roles: [QA-SDET, Architect, Coder, Auditor]
steps: [test_authoring, test_audit, blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, mocks, cli]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: Mocked failure-path tests must supply inputs that actually reach the patched dependency.
```

### Invariant
An earlier parser/import/setup failure can masquerade as coverage for a patched branch.

### Role Guidance
- @QA-SDET: Use valid preceding inputs and assert the patched dependency was called.
