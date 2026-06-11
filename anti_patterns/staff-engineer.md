# Staff-Engineer Anti-Patterns

Entries @Staff-Engineer must apply when drafting roadmaps and blueprints.

---

## shared.stateful-component-missing-lifecycle: State Leakage Through Missing Lifecycle Primitives

```yaml
id: shared.stateful-component-missing-lifecycle
title: State Leakage Through Missing Lifecycle Primitives
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [blueprint, state]
unit_domains: [agent-infra]
triggers: [missing_reset, missing_teardown, cross_task_state_leak]
summary: Stateful components need explicit reset or teardown semantics in the blueprint.
```

### Invariant
Persistent task-boundary state must define clean-slate behavior.

### Role Guidance
- @Staff-Engineer: Add a lifecycle/reset section when blueprinting stateful storage.

---

## shared.security-metadata-open-type: Semantic Permissiveness in Security Metadata

```yaml
id: shared.security-metadata-open-type
title: Semantic Permissiveness in Security Metadata
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, security, validation]
triggers: [open_security_type, weak_regex, unbounded_clearance]
summary: Security metadata fields need closed or constrained types in the blueprint.
```

### Invariant
Access-control fields must reject unexpected values at parse time.

### Role Guidance
- @Staff-Engineer: Specify `Literal`, `Enum`, or constrained validation for access-path fields.

---

## shared.unspecified-transformation-path: Specification Ambiguity in Transformation Steps

```yaml
id: shared.unspecified-transformation-path
title: Specification Ambiguity in Transformation Steps
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [2, 3, 4, 5]
domains: [blueprint, transformation]
triggers: [unspecified_normalization, inferred_serialization, ambiguous_comparison]
summary: Blueprints must name exact transformation or normalization paths when bytes or comparisons matter.
```

### Invariant
Non-trivial transformations are not safe to leave to implementation inference.

### Role Guidance
- @Staff-Engineer: Spell out intermediate serialization, encoding, hashing, or normalization steps.

---

## shared.security-boundary-fail-open: Fail-Open Defaults in Security Boundaries

```yaml
id: shared.security-boundary-fail-open
title: Fail-Open Defaults in Security Boundaries
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, security]
triggers: [fail_open_exception, implicit_allow, malformed_input_acceptance]
summary: Security boundary blueprints must specify explicit denial for malformed or unexpected input.
```

### Invariant
Malformed input at a security boundary must not fall through to allow.

### Role Guidance
- @Staff-Engineer: Define fail-closed behavior for every malformed/unparsable input class.

---

## shared.ghost-coverage-placeholder-tests: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: shared.ghost-coverage-placeholder-tests
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests]
triggers: [placeholder_test, uncollected_test, empty_assertion]
summary: Blueprint assertions should be phrased so QA can map them to executable tests.
```

### Invariant
Untestable prose encourages placeholder or comment-only coverage.

### Role Guidance
- @Staff-Engineer: State required observable outcomes clearly enough for QA to assert directly.

---

## shared.blueprint-drift-adds-to-source-law: Additive Blueprint Drift From Canonical Source Law

```yaml
id: shared.blueprint-drift-adds-to-source-law
title: Additive Blueprint Drift From Canonical Source Law
roles: [Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, source_law]
triggers: [extra_alias, narrowed_type, partial_contract_restatement]
summary: Level 4-5 blueprints must reproduce source-law surfaces without helpful local additions.
```

### Invariant
Extra aliases, stronger annotations, or partial restatements can create a second contract.

### Role Guidance
- @Staff-Engineer: Copy canonical surfaces and subcontracts verbatim unless the ADR is revised.

---

## shared.mocked-composition-misses-closed-surface: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: shared.mocked-composition-misses-closed-surface
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [QA-SDET, Staff-Engineer, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [4, 5]
domains: [blueprint, tests, module_surface]
triggers: [mocked_public_symbol, untested_public_api, closed_surface_gap]
summary: High-risk blueprints must distinguish public symbol coverage from entrypoint matrix coverage.
```

### Invariant
Mocked composition does not prove each published public symbol.

### Role Guidance
- @Staff-Engineer: Separate symbol-surface obligations from entrypoint scenario obligations.

---

## shared.test-invents-noncanonical-dependency: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: shared.test-invents-noncanonical-dependency
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [3, 4, 5]
domains: [blueprint, tests, imports]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Blueprints must name canonical dependency and observation surfaces when tests need them.
```

### Invariant
If QA must spy on a side effect or consume upstream types, the blueprint needs a canonical channel.

### Role Guidance
- @Staff-Engineer: Specify canonical observable channels and upstream constructors where they are test-relevant.

---

## shared.observability-closure-underspecified: Under-Specified Canonical Closure For Operational Observability

```yaml
id: shared.observability-closure-underspecified
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [blueprint, observability]
unit_domains: [agent-infra]
triggers: [missing_observation_channel, impossible_side_effect, repeated_blueprint_oscillation]
summary: Do not encode workarounds when source law lacks a legal implementation or observation path.
```

### Invariant
Mandatory operational side effects need a compliant mechanism and test channel.

### Role Guidance
- @Staff-Engineer: Escalate upstream if satisfying the ADR requires invented hooks or imports.

---

## shared.blueprint-missing-transitive-source-law: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: shared.blueprint-missing-transitive-source-law
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Staff-Engineer, Coder, QA-SDET, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, source_law]
unit_domains: [agent-infra]
triggers: [adr_only_test_matrix, transitive_requirement, incomplete_blueprint_handoff]
summary: Level 4-5 blueprints must contain every downstream normative requirement locally.
```

### Invariant
Coder and QA must not need ADR sections to discover fixture matrices, literals, or behavior.

### Role Guidance
- @Staff-Engineer: Copy implementable and testable obligations into the blueprint itself.

---

## shared.unanchored-import-surface-preimpl-tests: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: shared.unanchored-import-surface-preimpl-tests
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Staff-Engineer, QA-SDET, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, test_authoring, test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, imports]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Blueprints requiring pre-implementation tests must provide an import path or binding mechanism.
```

### Invariant
QA cannot execute public-surface tests without a sanctioned binding.

### Role Guidance
- @Staff-Engineer: Do not leave module path discretionary unless the test binding is also defined.

---

## shared.non-falsifying-contract-assertion: Non-Falsifying Contract Assertions

```yaml
id: shared.non-falsifying-contract-assertion
title: Non-Falsifying Contract Assertions
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, cli]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Mandatory assertions should identify observable signals that weak tests cannot fake.
```

### Invariant
Assertions that only prove a scenario ran create false confidence.

### Role Guidance
- @Staff-Engineer: Phrase mandatory assertions with contract-bearing observable outputs.

---

## shared.unreachable-mocked-failure-path: Unreachable Mocked Failure Paths

```yaml
id: shared.unreachable-mocked-failure-path
title: Unreachable Mocked Failure Paths
roles: [QA-SDET, Staff-Engineer, Coder, Auditor]
steps: [blueprint_draft, test_authoring, test_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, mocks]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: When a mandatory assertion distinguishes failure sources, name the source-specific observable.
```

### Invariant
A test can cover an earlier failure path while claiming to cover a later patched branch.

### Role Guidance
- @Staff-Engineer: Specify source-specific observables when failure-source distinction matters.
