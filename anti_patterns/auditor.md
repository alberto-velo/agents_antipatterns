# Auditor Anti-Patterns

Entries @Auditor must apply when reviewing source law, blueprints, tests, and code.

---

## shared.stateful-component-missing-lifecycle: State Leakage Through Missing Lifecycle Primitives

```yaml
id: shared.stateful-component-missing-lifecycle
title: State Leakage Through Missing Lifecycle Primitives
roles: [Architect, Coder, Auditor]
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

## shared.security-metadata-open-type: Semantic Permissiveness in Security Metadata

```yaml
id: shared.security-metadata-open-type
title: Semantic Permissiveness in Security Metadata
roles: [Architect, Coder, Auditor]
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

## shared.hardening-blocks-authorized-input: Regressive Over-Restriction

```yaml
id: shared.hardening-blocks-authorized-input
title: Regressive Over-Restriction
roles: [Architect, Coder, Auditor]
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

## shared.unspecified-transformation-path: Specification Ambiguity in Transformation Steps

```yaml
id: shared.unspecified-transformation-path
title: Specification Ambiguity in Transformation Steps
roles: [Architect, Coder, Auditor]
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

## shared.security-boundary-fail-open: Fail-Open Defaults in Security Boundaries

```yaml
id: shared.security-boundary-fail-open
title: Fail-Open Defaults in Security Boundaries
roles: [Architect, Coder, Auditor]
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

## coder.import-masking-hides-failure: Import Masking in Test Suites

```yaml
id: coder.import-masking-hides-failure
title: Import Masking in Test Suites
roles: [Coder, Auditor]
steps: [code_audit]
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

## shared.canonical-artifact-path-drift: Canonical Artifact Path Drift

```yaml
id: shared.canonical-artifact-path-drift
title: Canonical Artifact Path Drift
roles: [Architect, Coder, Auditor]
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

## shared.ghost-coverage-placeholder-tests: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: shared.ghost-coverage-placeholder-tests
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## coder.test-invents-unlicensed-contract: Test Contract Invention

```yaml
id: coder.test-invents-unlicensed-contract
title: Test Contract Invention
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## coder.partial-structured-error-assertion: Partial Assertion of Structured Error Contracts

```yaml
id: coder.partial-structured-error-assertion
title: Partial Assertion of Structured Error Contracts
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.hidden-ambient-dependency-pure-interface: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: shared.hidden-ambient-dependency-pure-interface
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## coder.convenience-api-erases-probe-semantics: Fail-Closed Probe Semantics Lost Through Convenience APIs

```yaml
id: coder.convenience-api-erases-probe-semantics
title: Fail-Closed Probe Semantics Lost Through Convenience APIs
roles: [Architect, Coder, Auditor]
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

## shared.divergent-validators-closed-failure-alphabet: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: shared.divergent-validators-closed-failure-alphabet
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## shared.blueprint-drift-adds-to-source-law: Additive Blueprint Drift From Canonical Source Law

```yaml
id: shared.blueprint-drift-adds-to-source-law
title: Additive Blueprint Drift From Canonical Source Law
roles: [Architect, Coder, Auditor]
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

## coder.state-matrix-subcomponent-only-coverage: State-Matrix Coverage Collapse Through Subcomponent-Only Testing

```yaml
id: coder.state-matrix-subcomponent-only-coverage
title: State-Matrix Coverage Collapse Through Subcomponent-Only Testing
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## coder.constructor-bypasses-schema-validation: Constructor-Based Schema Validation on Untrusted Rows

```yaml
id: coder.constructor-bypasses-schema-validation
title: Constructor-Based Schema Validation on Untrusted Rows
roles: [Architect, Coder, Auditor]
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

## shared.unreachable-operational-transition-restart: Unreachable Operational Transition From Valid Restart States

```yaml
id: shared.unreachable-operational-transition-restart
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## shared.mocked-composition-misses-closed-surface: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: shared.mocked-composition-misses-closed-surface
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.defensive-fallback-branch-omitted: Uncovered Defensive Fallback in Closed Branch Logic

```yaml
id: shared.defensive-fallback-branch-omitted
title: Uncovered Defensive Fallback in Closed Branch Logic
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.test-invents-noncanonical-dependency: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: shared.test-invents-noncanonical-dependency
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.side-effect-invariant-unproven: Unproven Operational Side-Effect Invariants

```yaml
id: shared.side-effect-invariant-unproven
title: Unproven Operational Side-Effect Invariants
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.observability-closure-underspecified: Under-Specified Canonical Closure For Operational Observability

```yaml
id: shared.observability-closure-underspecified
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## shared.source-law-uses-nonexistent-upstream-api: Source-Law Drift From Frozen Upstream APIs And Reachability Closure

```yaml
id: shared.source-law-uses-nonexistent-upstream-api
title: Source-Law Drift From Frozen Upstream APIs And Reachability Closure
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [3, 4, 5]
domains: [source_law, imports, tests]
unit_domains: [agent-infra]
triggers: [nonexistent_upstream_import, contradictory_reachability, impossible_test_row]
summary: Source law must use shipped upstream APIs and require only reachable rows.
```

### Invariant
Contradictory or nonexistent upstream contracts are upstream defects.

### Role Guidance
- @Auditor: Do not route TESTS/CODER for faithfully following contradictory source law.

---

## shared.blueprint-missing-transitive-source-law: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: shared.blueprint-missing-transitive-source-law
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
risk_levels: [4, 5]
domains: [blueprint, tests, implementation]
unit_domains: [agent-infra]
triggers: [out_of_blueprint_requirement, transitive_requirement, incomplete_blueprint_handoff]
summary: Level 4-5 blueprints must be self-contained for Blueprint-bound downstream roles.
```

### Invariant
Downstream roles should not read documents outside the blueprint to discover normative details.

### Role Guidance
- @Auditor: Treat transitive dependencies on documents outside the blueprint as handoff-closure defects.

---

## shared.unprobed-framework-error-literal: Unprobed Framework Error Literals In Source Law

```yaml
id: shared.unprobed-framework-error-literal
title: Unprobed Framework Error Literals In Source Law
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## shared.unanchored-import-surface-preimpl-tests: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: shared.unanchored-import-surface-preimpl-tests
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Architect, Coder, Auditor]
steps: [blueprint_audit, code_audit]
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

## shared.non-falsifying-contract-assertion: Non-Falsifying Contract Assertions

```yaml
id: shared.non-falsifying-contract-assertion
title: Non-Falsifying Contract Assertions
roles: [Coder, Architect, Auditor]
steps: [code_audit]
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

## shared.unreachable-mocked-failure-path: Unreachable Mocked Failure Paths

```yaml
id: shared.unreachable-mocked-failure-path
title: Unreachable Mocked Failure Paths
roles: [Coder, Architect, Auditor]
steps: [code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, mocks, cli]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: Mocked error-path tests must prove the patched dependency was reached.
```

### Invariant
Earlier validation failure can masquerade as downstream branch coverage.

### Role Guidance
- @Auditor: Require mock reachability or a unique patched sentinel in asserted output.

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
- @Auditor: At `code_audit`, run each anchored pattern against a real multi-line target; a pattern no correct file can match is a blocking defect routed `TESTS`.

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
- @Auditor: At `code_audit`, a static-analysis finding in a frozen test file is a blocking defect routed `TESTS`, not `CODER`.

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
- @Auditor: At `code_audit`, search the test files for each literal such a signal names; a literal no test asserts is missing coverage, routed `TESTS`.

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
- @Auditor: Check each named fact against the tests' actual read sets and prove falsifiability by mutating it with `run_sandbox_bash`; an unasserted clause is missing coverage, routed `TESTS`.

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
- @Auditor: Check each layering assertion against the test's actual match target, not its name, by injecting a violating import with `run_sandbox_bash`; a scan that cannot match is missing coverage, routed `TESTS`.

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
- @Auditor: At `blueprint_audit`, list each directory the unit empties and compare it with the delete entries; a leftover file is a blocking defect routed `ARCHITECT`.

---

## shared.grep-absence-signal-self-matches-compliant-code: Raw-Grep Absence Signal Matches Compliant Code

```yaml
id: shared.grep-absence-signal-self-matches-compliant-code
title: Raw-Grep Absence Signal Matches Compliant Code
roles: [Architect, Auditor]
steps: [blueprint_draft, blueprint_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, assertions]
triggers: [raw_grep_absence_signal, self_matching_signal, false_implementation_fail]
summary: An absence signal for a retired identifier must be structural, never a raw substring grep that the tests and comments themselves match.
```

### Invariant
A repository-wide raw-substring grep for a retired name necessarily matches the test that asserts its absence, a synthetic fixture written to prove a detector fires, and any stale comment — so no correct implementation satisfies it as written. An absence signal must be declaration-shaped or structural: an AST or line-shape check, a needle built by concatenation (`"type "+NAME+" interface"`), or a symbol query.

### Role Guidance
- @Auditor: Read an absence signal as “not declared / not referenced by live code”; never FAIL the Coder for a hit inside a test string literal, an assertion argument or a comment, and route a raw-grep signal at `blueprint_audit` as `ARCHITECT`.

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
- @Auditor: At `blueprint_audit`, a formatter mandate over files the blueprint also pins byte for byte is a blocking defect routed `ARCHITECT`; at `code_audit`, a formatting finding on such a file is advisory.

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
- @Auditor: When the full test command fails and the only offender is the unit's own test file, route `TESTS` — a spelling change, not a contract change — and confirm the asserted value is unchanged by the fix.

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
- @Auditor: For each derivative surface entry, compare the base entry's facts with the mirror's assertions and prove a gap by deleting a carried-over fact with `run_sandbox_bash`; a delta-only set is missing coverage, routed `TESTS`.
