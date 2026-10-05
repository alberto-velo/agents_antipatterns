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
The repository's static-analysis commands (`go vet ./...`, `golangci-lint run ./...`, or the equivalent) collect test files as well. A test file that passes its own assertions but fails vet/lint blocks the unit, and once `freeze_tests` has run the Coder can no longer change it — only a `TESTS` route reopens it, at the cost of a full audit round. Typical shapes: unreachable code after a terminating `for {}`, an unused import or helper, an unformatted file, an unchecked return.

### Role Guidance
- @Coder: Run the repo's declared vet/lint commands over the test files before `freeze_tests` and fix every finding in them; with no lint config, assume the default linter set.

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
