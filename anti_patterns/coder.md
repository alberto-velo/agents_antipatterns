# Coder Anti-Patterns

Entries @Coder must apply during implementation.

---

## shared.stateful-component-missing-lifecycle: State Leakage Through Missing Lifecycle Primitives

```yaml
id: shared.stateful-component-missing-lifecycle
title: State Leakage Through Missing Lifecycle Primitives
roles: [Architect, Coder, Auditor]
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

## shared.security-metadata-open-type: Semantic Permissiveness in Security Metadata

```yaml
id: shared.security-metadata-open-type
title: Semantic Permissiveness in Security Metadata
roles: [Architect, Coder, Auditor]
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

## shared.hardening-blocks-authorized-input: Regressive Over-Restriction

```yaml
id: shared.hardening-blocks-authorized-input
title: Regressive Over-Restriction
roles: [Architect, Coder, Auditor]
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

## shared.unspecified-transformation-path: Specification Ambiguity in Transformation Steps

```yaml
id: shared.unspecified-transformation-path
title: Specification Ambiguity in Transformation Steps
roles: [Architect, Coder, Auditor]
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

## shared.security-boundary-fail-open: Fail-Open Defaults in Security Boundaries

```yaml
id: shared.security-boundary-fail-open
title: Fail-Open Defaults in Security Boundaries
roles: [Architect, Coder, Auditor]
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

## shared.hidden-ambient-dependency-pure-interface: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: shared.hidden-ambient-dependency-pure-interface
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
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

## coder.convenience-api-erases-probe-semantics: Fail-Closed Probe Semantics Lost Through Convenience APIs

```yaml
id: coder.convenience-api-erases-probe-semantics
title: Fail-Closed Probe Semantics Lost Through Convenience APIs
roles: [Architect, Coder, Auditor]
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

## shared.divergent-validators-closed-failure-alphabet: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: shared.divergent-validators-closed-failure-alphabet
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
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

## shared.blueprint-drift-adds-to-source-law: Additive Blueprint Drift From Canonical Source Law

```yaml
id: shared.blueprint-drift-adds-to-source-law
title: Additive Blueprint Drift From Canonical Source Law
roles: [Architect, Coder, Auditor]
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

## coder.constructor-bypasses-schema-validation: Constructor-Based Schema Validation on Untrusted Rows

```yaml
id: coder.constructor-bypasses-schema-validation
title: Constructor-Based Schema Validation on Untrusted Rows
roles: [Architect, Coder, Auditor]
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

## shared.unreachable-operational-transition-restart: Unreachable Operational Transition From Valid Restart States

```yaml
id: shared.unreachable-operational-transition-restart
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
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

## shared.defensive-fallback-branch-omitted: Uncovered Defensive Fallback in Closed Branch Logic

```yaml
id: shared.defensive-fallback-branch-omitted
title: Uncovered Defensive Fallback in Closed Branch Logic
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [implementation, branch_logic, tests]
triggers: [missing_defensive_fallback_test, untested_error_literal, total_function_gap]
summary: Blueprint-declared defensive fallback branches must be implemented verbatim.
```

### Invariant
Fallback branches remain normative even if upstream validation usually rejects bad input first.

### Role Guidance
- @Coder: Do not optimize away blueprint-declared fallback branches.
- @Coder (writing the tests): Reach the fallback with invalid input and assert the exact literal or payload.

---

## shared.test-invents-noncanonical-dependency: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: shared.test-invents-noncanonical-dependency
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, imports, implementation, dependencies]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Do not add implementation shims solely to satisfy invented test-side dependency surfaces.
```

### Invariant
Tests do not amend the blueprint or upstream API.

### Role Guidance
- @Coder: Record a discrepancy instead of adding undeclared helpers or aliases.
- @Coder (writing the tests): Instantiate upstream types and patch side-effect channels exactly as declared.

---

## shared.side-effect-invariant-unproven: Unproven Operational Side-Effect Invariants

```yaml
id: shared.side-effect-invariant-unproven
title: Unproven Operational Side-Effect Invariants
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [implementation, tests, side_effects, observability]
triggers: [missing_negative_call_assertion, missing_order_assertion, unproven_side_effect]
summary: Green tests that omit operational side-effect proof do not close the implementation contract.
```

### Invariant
Payload correctness does not prove forbidden calls, ordering, or preserved side effects.

### Role Guidance
- @Coder: Implement the blueprint even when tests prove only a weaker signal.
- @Coder (writing the tests): Assert side effects directly instead of inferring them from return values.

---

## shared.observability-closure-underspecified: Under-Specified Canonical Closure For Operational Observability

```yaml
id: shared.observability-closure-underspecified
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
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

## shared.blueprint-missing-transitive-source-law: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: shared.blueprint-missing-transitive-source-law
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [blueprint, implementation, tests]
unit_domains: [agent-infra]
triggers: [out_of_blueprint_requirement, transitive_requirement, incomplete_blueprint_handoff]
summary: The Coder must be able to write the tests and the code from the blueprint without chasing out-of-blueprint requirements.
```

### Invariant
Blueprint-bound roles must not discover normative obligations by reading documents outside the blueprint.

### Role Guidance
- @Coder: Request blueprint revision when implementation behavior is only referenced by documents outside the blueprint.
- @Coder (writing the tests): Halt rather than deriving tests from out-of-blueprint matrices or literals.

---

## shared.unanchored-import-surface-preimpl-tests: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: shared.unanchored-import-surface-preimpl-tests
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports, implementation, blueprint]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Do not treat an invented test import path as an implicit blueprint amendment.
```

### Invariant
Pre-implementation tests cannot impose an unauthorized public import channel.

### Role Guidance
- @Coder: Implement the blueprint surface and record the binding discrepancy.
- @Coder (writing the tests): Do not invent module paths; finish BLOCKED with the gap.

---

## shared.non-falsifying-contract-assertion: Non-Falsifying Contract Assertions

```yaml
id: shared.non-falsifying-contract-assertion
title: Non-Falsifying Contract Assertions
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, implementation, cli, workflow]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Do not treat weak green tests as proof that the blueprint obligation is satisfied.
```

### Invariant
Implementation must satisfy the blueprint, not merely the weakest passing assertion.

### Role Guidance
- @Coder: Implement the required behavior even if approved tests would miss its absence.
- @Coder (writing the tests): Name the observable signal and the plausible broken implementation each test catches.

---

## shared.unreachable-mocked-failure-path: Unreachable Mocked Failure Paths

```yaml
id: shared.unreachable-mocked-failure-path
title: Unreachable Mocked Failure Paths
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, implementation, mocks, cli]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: A green test that fails before the patched dependency does not prove downstream failure handling.
```

### Invariant
Mocked branch coverage requires valid preceding inputs and proof the mock was reached.

### Role Guidance
- @Coder: Do not infer downstream failure handling is covered by tests that never reach the dependency.
- @Coder (writing the tests): Use valid preceding inputs and assert the patched dependency was called.

---

## coder.import-masking-hides-failure: Import Masking in Test Suites

```yaml
id: coder.import-masking-hides-failure
title: Import Masking in Test Suites
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, imports]
unit_domains: [agent-infra]
triggers: [import_masking, false_green_tests, hidden_import_failure]
summary: Tests must fail loudly when implementation imports are missing or broken.
```

### Invariant
Core implementation imports must not be wrapped in `try/except ImportError`.

### Role Guidance
- @Coder (writing the tests): Use direct imports so missing implementation fails collection.

---

## shared.ghost-coverage-placeholder-tests: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: shared.ghost-coverage-placeholder-tests
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, blueprint]
triggers: [placeholder_test, uncollected_test, empty_assertion]
summary: Tests only count when executable assertions are collected and exercise the claimed contract.
```

### Invariant
`pass`, TODO-only bodies, comments, skipped placeholders, and nested uncollected
tests are missing coverage.

### Role Guidance
- @Coder (writing the tests): Map every required public-surface or mandatory assertion item to an executable assertion.

---

## coder.test-invents-unlicensed-contract: Test Contract Invention

```yaml
id: coder.test-invents-unlicensed-contract
title: Test Contract Invention
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [3, 4, 5]
domains: [tests, blueprint, source_law]
triggers: [invented_error_literal, invented_route, unsourced_expected_output]
summary: Tests must derive asserted literals and branches from the approved contract, not local guesses.
```

### Invariant
High-risk tests validate source law only when asserted outcomes trace back to
the blueprint or approved upstream behavior.

### Role Guidance
- @Coder (writing the tests): Do not invent error codes, route strings, helper names, or contract literals.

---

## coder.partial-structured-error-assertion: Partial Assertion of Structured Error Contracts

```yaml
id: coder.partial-structured-error-assertion
title: Partial Assertion of Structured Error Contracts
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [tests, errors]
triggers: [partial_error_assertion, incomplete_payload_check, structured_exception_drift]
summary: When the contract defines structured errors, tests must assert all contract-bearing fields.
```

### Invariant
Checking one convenient field can let non-verbatim error translations pass.

### Role Guidance
- @Coder (writing the tests): Assert every field or argument that distinguishes compliance from partial implementation.

---

## coder.state-matrix-subcomponent-only-coverage: State-Matrix Coverage Collapse Through Subcomponent-Only Testing

```yaml
id: coder.state-matrix-subcomponent-only-coverage
title: State-Matrix Coverage Collapse Through Subcomponent-Only Testing
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [tests, state]
triggers: [helper_only_coverage, missing_entrypoint_row, state_matrix_gap]
summary: Closed state matrix rows must be tested at the owning public entrypoint.
```

### Invariant
Helper-level validation does not prove the entrypoint emits the required
state-bearing payload.

### Role Guidance
- @Coder (writing the tests): Add entrypoint-level tests for every externally observable state row.

---

## shared.mocked-composition-misses-closed-surface: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: shared.mocked-composition-misses-closed-surface
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [Coder, Architect, Auditor]
steps: [implementation, code_audit, blueprint_draft]
risk_levels: [4, 5]
domains: [tests, module_surface]
triggers: [mocked_public_symbol, untested_public_api, closed_surface_gap]
summary: Every public symbol in a closed surface needs direct executable coverage.
```

### Invariant
An entrypoint test that mocks a public dependency does not verify that mocked
symbol's own contract.

### Role Guidance
- @Coder (writing the tests): Build a public-symbol checklist before expanding into matrix coverage.

---

## shared.source-law-uses-nonexistent-upstream-api: Source-Law Drift From Frozen Upstream APIs And Reachability Closure

```yaml
id: shared.source-law-uses-nonexistent-upstream-api
title: Source-Law Drift From Frozen Upstream APIs And Reachability Closure
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [source_law, imports, tests]
unit_domains: [agent-infra]
triggers: [nonexistent_upstream_import, contradictory_reachability, impossible_test_row]
summary: Source law that composes frozen units must use shipped APIs and require only reachable test rows.
```

### Invariant
The tests must not invent compatibility shims for impossible imports or unreachable rows.

### Role Guidance
- @Coder (writing the tests): Treat impossible imports and contradictory reachability as upstream defects.

---

## shared.unprobed-framework-error-literal: Unprobed Framework Error Literals In Source Law

```yaml
id: shared.unprobed-framework-error-literal
title: Unprobed Framework Error Literals In Source Law
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [source_law, framework, tests]
unit_domains: [agent-infra]
triggers: [unverified_framework_literal, unreachable_diagnostic, pydantic_error_drift]
summary: Exact framework diagnostics made normative must be empirically verified against the project runtime.
```

### Invariant
Framework diagnostic literals must be probed before the tests are asked to assert them.

### Role Guidance
- @Coder (writing the tests): Test exact framework diagnostics verbatim; a runtime mismatch is a source-law defect — finish BLOCKED with it.

---

## coder.anchored-regex-whole-file-matcher: Anchored Line-Shape Regex Applied To A Whole File

```yaml
id: coder.anchored-regex-whole-file-matcher
title: Anchored Line-Shape Regex Applied To A Whole File
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [0, 1, 2, 3, 4, 5]
domains: [tests, regex]
triggers: [anchored_regex_whole_string, unsatisfiable_assertion, single_line_fixture_only]
summary: A ^...$ line-shape pattern matched against whole-file content must be line-aware, or no correct implementation can satisfy it.
```

### Invariant
A line-shape pattern (`^<line>$`) applied through a whole-string matcher (Go `assert.Regexp(t, pattern, fileContents)`) anchors to the whole input, not to each line, so it never matches a multi-line file: the assertion is unsatisfiable by a correct implementation. The match must be line-aware — split the content into lines, or enable multiline (`(?m)`).

### Role Guidance
- @Coder: When writing the tests, make every line-shape match line-aware, and check each pattern against a representative multi-line file body, never only a one-line example.

---

## coder.test-file-static-analysis-gate: Test Files Must Pass The Repo's Static Analysis Before The Freeze

```yaml
id: coder.test-file-static-analysis-gate
title: Test Files Must Pass The Repo's Static Analysis Before The Freeze
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [0, 1, 2, 3, 4, 5]
domains: [tests, static_analysis]
triggers: [test_file_lint_failure, test_file_vet_failure, frozen_test_needs_fix]
summary: Test files are collected by the repo's vet/lint commands too; a finding in a frozen test costs a TESTS round.
```

### Invariant
The repository's static-analysis commands (`go vet ./...`, `golangci-lint run ./...`, or the equivalent) collect test files as well. A test file that passes its own assertions but fails vet/lint blocks the unit, and once `freeze_tests` has run the Coder can no longer change it — only a `TESTS` route reopens it, at the cost of a full audit round. Typical shapes: unreachable code after a terminating `for {}`, an unused import or helper, an unformatted file, an unchecked return, a type or argument mismatch against code that already exists. Before the freeze the code entries are not written yet, so a test that uses them cannot compile cleanly: an error that only names a symbol a `kind: code` entry declares and has not written yet is expected; every other finding is a defect in the test. Most compilers stop at the first error (`go vet` at a package's first type error), which hides the rest.

### Role Guidance
- @Coder: Before `freeze_tests`, compile and lint the test files with commands that report every error, not just the first (Go: build each package's test binary without running it, with `-gcflags=all=-e`). Fix every finding except an error that only names a symbol a `kind: code` entry declares and has not written yet; with no lint config, assume the default linter set.

---

## coder.unasserted-carried-over-behaviour: Carried-Over Behaviour Left Unasserted In A Move Or Split

```yaml
id: coder.unasserted-carried-over-behaviour
title: Carried-Over Behaviour Left Unasserted In A Move Or Split
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, refactor]
triggers: [preserve_clause_unasserted, carried_over_behaviour, move_only_unit]
summary: In a move/split unit, every “preserve the original's behaviour” clause needs an assertion, not only the clauses that are new.
```

### Invariant
When a unit recreates files under new paths and deletes the originals, a rewrite loses exactly the facts nobody checks. Every clause of a “preserve / carried over unchanged” mandatory assertion — startup and shutdown signalling, the exit path, error handling, unchanged literals — must be asserted, not only the clauses that distinguish the new files. A dropped runtime-behaviour clause ships green with changed behaviour in a unit whose contract is zero behaviour change.

### Role Guidance
- @Coder: When writing the tests, for each mandatory assertion that says preserve, carried over or same as before, assert every literal its observable signal names.

---

## coder.multi-clause-signal-partially-asserted: Compound Observable Signal Only Partly Asserted

```yaml
id: coder.multi-clause-signal-partially-asserted
title: Compound Observable Signal Only Partly Asserted
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, coverage]
triggers: [compound_signal, partial_assertion]
summary: Every fact a compound observable signal names must be traceable to an assertion; asserting one clause does not cover the rest.
```

### Invariant
A mandatory assertion whose observable signal names several files, literals or fields is covered only when each named fact is in some test's read and assert set. A file no test opens is an unasserted fact, and a whole-repository scanner that does not read that file does not cover it.

### Role Guidance
- @Coder: When writing the tests, list every file and literal a compound signal names and assert each one.

---

## coder.layering-assertion-without-matching-scan: Layering Assertion Without A Scan That Can Match It

```yaml
id: coder.layering-assertion-without-matching-scan
title: Layering Assertion Without A Scan That Can Match It
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, layering]
unit_domains: [agent-infra, api]
triggers: [layering_invariant_uncovered, transitive_dependency_scan, scan_cannot_match]
summary: A layering or dependency-direction assertion needs a test whose scan can actually match a violation.
```

### Invariant
Direction-of-dependency and layering invariants (no adapter or transport import in the core) change no statement, so every behavioural and compile-time test stays green when they break, and linters do not catch them without a lint config. A test covers such an invariant only if its literal pattern, symbol or scan set can match the violation; a scanner aimed at something else does not, whatever its name.

### Role Guidance
- @Coder: Give each layering assertion its own scan of the unit's own production import blocks (excluding `_test.go` when the contract scopes the ban to production files), proven on a synthetic tree in both directions; never scan a dependency graph (`go list -deps`), which reports transitively acquired packages and fails a compliant implementation.

---

## shared.incomplete-delete-set-test-orphan-directory: Incomplete Delete Set Leaves A Test-Only Directory

```yaml
id: shared.incomplete-delete-set-test-orphan-directory
title: Incomplete Delete Set Leaves A Test-Only Directory
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, implementation]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, deletions]
triggers: [orphaned_test_file, incomplete_delete_set, no_non_test_go_files]
summary: A blueprint that removes a directory's production files must delete every file left in it, including test files that reference only package-local symbols.
```

### Invariant
When a unit relocates a directory as create-at-new-path plus delete-at-old, `migration_scope` must delete every file left in the old directory — including test files whose only references are package-local symbols (a bare `New(...)`, a sibling's helper), which match no `removed_symbols` identifier and no moved path, so the mechanical blueprint checks cannot see them. An orphaned test-only directory fails the build and vet (“no non-test Go files”); the Coder then ships red gates or deletes files outside its declared scope, silently dropping carried-over assertions.

### Role Guidance
- @Coder: A leftover file you may not delete is a `finish(BLOCKED)` gap (`file_not_in_blueprint`), never a deletion outside your entries.

---

## shared.format-mandate-breaks-byte-identity-assertion: Formatting Mandate Contradicts A Byte-Identity Assertion

```yaml
id: shared.format-mandate-breaks-byte-identity-assertion
title: Formatting Mandate Contradicts A Byte-Identity Assertion
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, formatting, refactor]
triggers: [gofmt_mandate, byte_identity_assertion, preexisting_format_defect]
summary: A formatter mandate never overrides an assertion that pins a moved file's content; pre-existing formatting defects stay untouched.
```

### Invariant
A blueprint that both mandates a formatter on every changed file (`gofmt`) and pins a moved file's content byte for byte can contradict itself: the formatter rewrites a pre-existing, non-behavioural formatting defect (`(error)` → `error`, an import reorder) that the byte-identity assertion forbids. The byte-identity assertion wins.

### Role Guidance
- @Coder: Leave a pre-existing formatting defect in a byte-pinned file untouched; if that assertion goes red on a formatting-only diff, revert the formatting — never the test.

---

## shared.test-literal-trips-retirement-scan: Asserted Literal Trips A Repository-Wide Forbidden-Token Scan

```yaml
id: shared.test-literal-trips-retirement-scan
title: Asserted Literal Trips A Repository-Wide Forbidden-Token Scan
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, blueprint]
triggers: [banned_token_literal, self_scanning_test, retirement_scan]
summary: A test must not state, as one contiguous literal, a token a repository-wide forbidden-token scan bans — even when the blueprint mandates the value.
```

### Invariant
A repository with a forbidden-token scan (a test that walks every source file for retired names and self-excludes only its own file) fails as a whole when a new test states an expected value with the banned token contiguous, even when the blueprint mandates that value: the unit's own test command passes while the repository's full test command fails. Building the expected value from fragments (`"…as-ccm-" + "us-…"`) keeps the compared string byte-identical and the scan green.

### Role Guidance
- @Coder: Before `freeze_tests`, run the repository's full declared test command, not only the unit's package, and spell any asserted literal that trips a forbidden-token scan from fragments.

---

## coder.derivative-surface-asserts-deltas-only: Derivative Surface Entry Asserted Only For Its Differences

```yaml
id: coder.derivative-surface-asserts-deltas-only
title: Derivative Surface Entry Asserted Only For Its Differences
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [tests, coverage]
triggers: [derivative_surface_entry, delta_only_assertions, mirrored_artifact]
summary: When a surface entry is defined as identical to another except named differences, the tests must assert the carried-over facts for it too.
```

### Invariant
When a Public Surface entry defines an artifact by reference to another (“identical to X except …”), every carried-over fact of X binds the mirror as well. A suite that asserts only the named differences leaves the mirror's remaining facts unverified while the per-entry assertion mapping looks complete — mirrored Deployments or Dockerfiles in infra, duplicated handlers in api, mirrored components in frontend.

### Role Guidance
- @Coder: When writing the tests, assert each carried-over fact of the base entry against the mirror as its own assertion, not only the named differences.

---

## architect.unreachable-mandatory-outcome-under-closed-surface: Mandatory Outcome With No Reachable Path Under the Blueprint's Own Mapping

```yaml
id: architect.unreachable-mandatory-outcome-under-closed-surface
title: Mandatory Outcome With No Reachable Path Under the Blueprint's Own Mapping
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, implementation]
risk_levels: [3, 4, 5]
domains: [blueprint, tests, api]
unit_domains: [api]
triggers: [closed_failure_alphabet, fail_closed_error_mapping, frozen_mandatory_assertion, fake_failure_switch, existence_check_before_permission]
summary: Every mandatory outcome must be producible by the blueprint's own step-level error mapping, with a test hook that reaches it.
```

### Invariant
For a unit with a closed outcome alphabet, each mandated outcome has at least one execution path in the deterministic pseudocode that produces it, and at least one authorized observation/failure-control hook that makes that path reachable from a test. Where an earlier step swallows an error and maps it to a different canonical outcome, an assertion that demands the original cause for the same event is self-contradictory, and the test hook the assertions rely on must itself be in the closed surface.

### Role Guidance
- @Coder: If a frozen assertion demands an outcome the pseudocode cannot produce, do not silently pick one of the two contradicting clauses or invent an unauthorised fake hook: finish BLOCKED naming both clauses, so the blueprint is reconciled upstream.

---

## coder.reflect-method-set-order-is-alphabetical: Reflect Interface Method Set Cannot Report Declaration Order

```yaml
id: coder.reflect-method-set-order-is-alphabetical
title: Reflect Interface Method Set Cannot Report Declaration Order
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [tests, api]
triggers: [reflect_method_set_assertion, interface_declaration_order, closed_interface_surface]
summary: Never assert an interface's mandated declaration order through reflect.Type.Method(i) — reflect returns methods sorted by name, so the assertion is unsatisfiable.
```

### Invariant
Go's reflect orders an interface's method set by name: `reflect.TypeOf((*I)(nil)).Elem().Method(i)` yields methods alphabetically, regardless of declaration order. A test that maps a mandated declaration-order list onto reflect's index therefore fails for every compliant interface. Assert the method-name SET via reflect (sort before comparing); where declaration order itself is the contract, observe it from the interface's source text.

### Role Guidance
- @Coder: For a Go interface method-set test, compare reflect names as a sorted set, and prove declaration order by scanning the interface's source text with the package's existing helpers; never bind an assertion to reflect's index order.

---

## architect.closed-edit-set-vs-repo-absence-scan: Closed Edit Set Collides With a Pre-existing Whole-Repository Absence Scan

```yaml
id: architect.closed-edit-set-vs-repo-absence-scan
title: Closed Edit Set Collides With a Pre-existing Whole-Repository Absence Scan
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [blueprint, tests, api]
triggers: [whole_repository_absence_scan, closed_file_set, cross_unit_test_collision, definition_of_done_red]
summary: Before closing a unit's edit set, search the repository for whole-repo absence scans the mandated artifact necessarily trips; authorize the scope change or record the collision.
```

### Invariant
A blueprint that closes its file set ("no file outside migration_scope may be touched") must show that no pre-existing whole-repository absence/forbidden-pattern scan is tripped by the artifact it mandates. If one is, the scan file enters migration_scope with an explicit bound, or the collision is recorded as a numbered discrepancy with the developer's remedy. Otherwise the unit's Definition of Done (repo test command green, only manifest files changed) is unsatisfiable.

### Role Guidance
- @Coder: Do not rename a blueprint-mandated field or symbol to slip past a repository absence scan, and do not edit a sibling unit's frozen test that lies outside migration_scope; report the collision as a gap instead.

---

## coder.derived-order-assertion-over-random-keys: List-Order Assertion Over Random Keys Assumes Seeding Order

```yaml
id: coder.derived-order-assertion-over-random-keys
title: List-Order Assertion Over Random Keys Assumes Seeding Order
roles: [Auditor, Coder]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, api]
triggers: [list_order_assertion, random_uuid_fixtures, sorted_collaborator_output, flaky_test]
summary: A test asserting list order over uuid.New() keys must derive the expected order from the sort rule; comparing against seeding order flakes about half the time.
```

### Invariant
When a test's fixtures are keyed by randomly generated identifiers (uuid.New()) and the collaborator returns them in a derived order (sorted by identifier), the expected order must be derived by the test from the same rule — sorting the seeded keys, or comparing the set plus the collaborator's own returned order. Asserting seeded/insertion order against a sorted result fails about half the runs against a fully compliant implementation.

### Role Guidance
- @Coder: In a test whose collaborator returns a derived order, build the expectation from the same ordering rule — sort the seeded keys before comparing, or compare the element-ID set and the collaborator's own returned order; never assume seeding order equals returned order when keys come from uuid.New().

---

## architect.prescribed-test-construction-must-match-the-code-it-names: Prescribed Test Construction Must Type-Check Against The Code It Names

```yaml
id: architect.prescribed-test-construction-must-match-the-code-it-names
title: Prescribed Test Construction Must Type-Check Against The Code It Names
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [3, 4, 5]
domains: [blueprint, tests]
triggers: [prescribed_constructor_returns_port_interface, test_scaffolding_would_not_compile, prescribed_helper_signature_mismatch]
summary: Blueprint-prescribed test scaffolding must match each named constructor's real signature; a constructor returning the port interface cannot be assigned to a concrete fake pointer.
```

### Invariant
Every constructor, receiver, parameter list and result list a blueprint prescribes for a test file must be the one the named code actually has. A frozen test built on a construction that does not type-check cannot be satisfied by any change the blueprint permits, so it fails at implementation and reopens the blueprint; likewise a helper whose prescribed usage contradicts the language (comparing `reflect`'s alphabetically sorted interface methods by declaration index) is unsatisfiable by a compliant implementation.

### Role Guidance
- @Coder: Finish BLOCKED naming the prescribed call whose type does not match the code, rather than inventing a shim, a constructor change, or an undeclared fake member.

---

## architect.restated-rule-predicates-must-match-across-sections: Cross-Section Rule Restatements Must Use the Pseudocode's Predicate

```yaml
id: architect.restated-rule-predicates-must-match-across-sections
title: Cross-Section Rule Restatements Must Use the Pseudocode's Predicate
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [4, 5]
domains: [blueprint, implementation]
unit_domains: [api]
triggers: [closed_state_transition_table, closed_failure_alphabet, mandatory_assertion_restates_pseudocode, ordering_invariant_permission_check, outcome_mapping_multiple_sections]
summary: A rule restated across blueprint sections by a positional proxy rather than the pseudocode's predicate double-maps one reachable input and authorizes reordered implementations.
```

### Invariant
Where a blueprint states one outcome or ordering rule in several closed sections (Mandatory Assertion, State Transition Table, outcome alphabet), every restatement must use the same semantic predicate and apply at the same step the pseudocode places it; no reachable input may fall in two rows with different canonical outcomes, and no table row may assert an observable (e.g. "zero permission calls") that the pseudocode's own step order contradicts.

### Role Guidance
- @Coder: Where the pseudocode and a table row disagree about which step a failure occurs at, the pseudocode's step order governs and the source-law ordering obligation (e.g. permission before data read) must not be reordered to satisfy the table; finish BLOCKED naming the two sections instead.

---

## architect.narrowed-inherited-absence-scan-must-assert-its-scan-set: A Narrowed Inherited Absence Scan Must Assert Its Own Scan Set

```yaml
id: architect.narrowed-inherited-absence-scan-must-assert-its-scan-set
title: A Narrowed Inherited Absence Scan Must Assert Its Own Scan Set
roles: [Architect, Auditor, Coder]
steps: [blueprint_draft, blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [blueprint, implementation, tests]
unit_domains: [api, agent-infra]
triggers: [amend_frozen_repository_wide_test, scope_absence_scan_to_packages, conflict_with_sibling_invariant]
summary: When a unit narrows another unit's whole-repository absence test to a declared scan set, the blueprint must assert the scan set itself, or the narrowed scan passes vacuously while the sibling invariant silently dies.
```

### Invariant
A blueprint that resolves a conflict with a frozen repository-wide absence invariant by scoping that scan to a declared set of paths must make the scan set itself an observable: an assertion that the amended scan walks exactly those declared paths, each of which exists. Then a narrowing to an empty, renamed or over-narrow set fails, and the invariant keeps provable force over the paths it was narrowed to. The amended test's synthetic-tree subtest proves only the scanner logic, never the scan set.

### Role Guidance
- @Coder: Do not narrow an inherited scan beyond the paths the blueprint declares, and do not delete the scan's self-exclusion or its synthetic-tree subtest to make it green: those are what keep a narrowed invariant falsifiable.

---

## coder.aliasing-non-mutation-assertion-hides-mutation: Non-mutation assertion compared against the double's stored object cannot fail

```yaml
id: coder.aliasing-non-mutation-assertion-hides-mutation
title: Non-mutation assertion compared against the double's stored object cannot fail
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [tests]
triggers: [non_mutation_assertion, side_effect_absence_assertion, fake_stores_seeded_pointer]
summary: A "writes nothing" check that re-reads a double and compares against the value it seeded cannot fail when the double stores the seeded pointer: snapshot values before the call.
```

### Invariant
A non-mutation / no-side-effect assertion is falsifiable only when its expectation is a value snapshot (or literal) taken BEFORE the call and it runs on every path the assertion names, covering the collaborator's whole observable state including the record count. If the test double keeps the caller's pointer (a map keyed by ID storing the seeded struct pointer), the seeded value IS the stored object, so any after-only re-read compared against it passes against an implementation that writes, updates or deletes.

### Role Guidance
- @Coder: When a mandatory assertion says a path must not write or publish: copy the fields the assertion names (plus the stored record count) out of the collaborator into a local value before the act, or seed a copy and assert literals after; never compare a re-read against the struct you passed to Save*/seeded, because pointer-storing fakes make that self-comparison. Run it over a success and a failure case of every operation the assertion names, not one representative path.

---

## shared.created-sibling-breaks-frozen-closed-set-scan: Created Sibling Breaks A Frozen Closed-Set Assertion

```yaml
id: shared.created-sibling-breaks-frozen-closed-set-scan
title: Created Sibling Breaks A Frozen Closed-Set Assertion
roles: [Architect, Auditor, Coder]
steps: [blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [tests, blueprint]
triggers: [creates_new_sibling_directory_or_file, frozen_test_enumerates_closed_set, migration_scope_missing_test_update, blueprint_forbids_editing_tests]
summary: A unit that creates a new sibling under a path a frozen test enumerates as a closed set must own that test's expectation update, or defer it explicitly with the red-gate interval disclosed.
```

### Invariant
Every structural test whose expected set grows because of this unit's new file or directory is a manifest entry (modify) or an explicit deferred hand-off; a unit may not both add the member and forbid the edit that admits it.

### Role Guidance
- @Coder: If creating a declared file turns a test outside the declared scope red because that test counts the directory or set you joined, stop and report it rather than editing the unowned test or dropping the required file.

---

## shared.source-scan-needle-must-pin-its-clause: Source-Scan Needle Must Pin Its Own Clause

```yaml
id: shared.source-scan-needle-must-pin-its-clause
title: Source-Scan Needle Must Pin Its Own Clause
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [2, 3, 4, 5]
domains: [tests, blueprint, implementation]
triggers: [source_scan_contract_assertion, multi_clause_behaviour_assertion, presence_needle_for_multi_site_literal, blueprint_test_scope_prescribes_needles]
summary: A test that reads a file's source covers a behaviour clause only with a needle that pins that clause's site, never a token every implementation or another clause's path contains.
```

### Invariant
When a Mandatory Assertion names several behaviour clauses (panics on X, cancels on Y, exits on Z) and is discharged by a test that reads the file's own source (a `main()` entrypoint has no runtime seam), each clause needs a needle only that clause satisfies: the exact statement (`signal.Notify(sigChan, syscall.SIGINT, syscall.SIGTERM)`), an occurrence count the Blueprint's pseudocode fixes (`strings.Count(src, "cancelFunc()") == 2`), or the adjacency of the guarded call and its handler (`if err != nil {` then `panic(err)`). A bare token such as `panic(` or `cancelFunc()` is present in any plausible implementation, and a literal that also occurs on another clause's path is satisfied by that occurrence, so an implementation that drops the clause still passes.

### Role Guidance
- @Coder: Before freezing a source-scan test, list every occurrence of each asserted literal in the target file; for a literal that occurs more than once, assert the count or the adjacency, not `Contains`, and prove each clause by deleting its statement from a scratch copy and watching the test fail. Never invent a literal the Blueprint does not give.

---

## architect.mandatory-assertion-unmapped-in-test-scope: Mandatory Assertion With No Test Named For It In Test Scope

```yaml
id: architect.mandatory-assertion-unmapped-in-test-scope
title: Mandatory Assertion With No Test Named For It In Test Scope
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, infra]
triggers: [mandatory_assertion_without_named_test, test_scope_lists_extensions_only, absence_assertion_uncovered]
summary: Every mandatory assertion must be mapped to a named test in Test Scope, or the Coder ships it unverified: the Coder writes to the test list, not the assertion list.
```

### Invariant
# Test Scope must account for every id in # Mandatory Assertions: each one either names the test (new or extended) that verifies it, or is explicitly declared a code_audit-only bound check. Absence-shaped and carried-over assertions are the ones usually dropped, because no Test Scope bullet names a file for them, and they ship unfalsifiable: a suite written to the Test Scope list passes green while the assertion's own text is contradicted.

### Role Guidance
- @Coder: Before freeze_tests, walk the # Mandatory Assertions list itself, not only the # Test Scope bullets: for each assertion, name the test that would fail if its fact were removed, and add the missing assertion (inside the already-declared test files) instead of assuming the scope list is complete. Absence clauses (no ENTRYPOINT, no env literal, no queue-shaped key) need an explicit read-and-assert.
