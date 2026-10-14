# Architect Anti-Patterns

Entries @Architect must apply when drafting blueprints.

---

## shared.stateful-component-missing-lifecycle: State Leakage Through Missing Lifecycle Primitives

```yaml
id: shared.stateful-component-missing-lifecycle
title: State Leakage Through Missing Lifecycle Primitives
roles: [Architect, Coder, Auditor]
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
- @Architect: Add a lifecycle/reset section when blueprinting stateful storage.

---

## shared.security-metadata-open-type: Semantic Permissiveness in Security Metadata

```yaml
id: shared.security-metadata-open-type
title: Semantic Permissiveness in Security Metadata
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, security, validation]
triggers: [open_security_type, weak_regex, unbounded_clearance]
summary: Security metadata fields need closed or constrained types in the blueprint.
```

### Invariant
Access-control fields must reject unexpected values at parse time.

### Role Guidance
- @Architect: Specify `Literal`, `Enum`, or constrained validation for access-path fields.

---

## shared.unspecified-transformation-path: Specification Ambiguity in Transformation Steps

```yaml
id: shared.unspecified-transformation-path
title: Specification Ambiguity in Transformation Steps
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [2, 3, 4, 5]
domains: [blueprint, transformation]
triggers: [unspecified_normalization, inferred_serialization, ambiguous_comparison]
summary: Blueprints must name exact transformation or normalization paths when bytes or comparisons matter.
```

### Invariant
Non-trivial transformations are not safe to leave to implementation inference.

### Role Guidance
- @Architect: Spell out intermediate serialization, encoding, hashing, or normalization steps.

---

## shared.security-boundary-fail-open: Fail-Open Defaults in Security Boundaries

```yaml
id: shared.security-boundary-fail-open
title: Fail-Open Defaults in Security Boundaries
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, security]
triggers: [fail_open_exception, implicit_allow, malformed_input_acceptance]
summary: Security boundary blueprints must specify explicit denial for malformed or unexpected input.
```

### Invariant
Malformed input at a security boundary must not fall through to allow.

### Role Guidance
- @Architect: Define fail-closed behavior for every malformed/unparsable input class.

---

## shared.ghost-coverage-placeholder-tests: Ghost Coverage From Placeholder or Uncollected Tests

```yaml
id: shared.ghost-coverage-placeholder-tests
title: Ghost Coverage From Placeholder or Uncollected Tests
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests]
triggers: [placeholder_test, uncollected_test, empty_assertion]
summary: Blueprint assertions should be phrased so the Coder can map them to executable tests.
```

### Invariant
Untestable prose encourages placeholder or comment-only coverage.

### Role Guidance
- @Architect: State required observable outcomes clearly enough for the tests to assert directly.

---

## shared.blueprint-drift-adds-to-source-law: Additive Blueprint Drift From Canonical Source Law

```yaml
id: shared.blueprint-drift-adds-to-source-law
title: Additive Blueprint Drift From Canonical Source Law
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, source_law]
triggers: [extra_alias, narrowed_type, partial_contract_restatement]
summary: Level 4-5 blueprints must reproduce source-law surfaces without helpful local additions.
```

### Invariant
Extra aliases, stronger annotations, or partial restatements can create a second contract.

### Role Guidance
- @Architect: Copy canonical surfaces and subcontracts verbatim unless the developer's input is revised.

---

## shared.mocked-composition-misses-closed-surface: Closed-Surface Coverage Gaps Behind Mocked Composition

```yaml
id: shared.mocked-composition-misses-closed-surface
title: Closed-Surface Coverage Gaps Behind Mocked Composition
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [4, 5]
domains: [blueprint, tests, module_surface]
triggers: [mocked_public_symbol, untested_public_api, closed_surface_gap]
summary: High-risk blueprints must distinguish public symbol coverage from entrypoint matrix coverage.
```

### Invariant
Mocked composition does not prove each published public symbol.

### Role Guidance
- @Architect: Separate symbol-surface obligations from entrypoint scenario obligations.

---

## shared.test-invents-noncanonical-dependency: Test-Side Invention of Non-Canonical Dependency Surfaces

```yaml
id: shared.test-invents-noncanonical-dependency
title: Test-Side Invention of Non-Canonical Dependency Surfaces
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [3, 4, 5]
domains: [blueprint, tests, imports]
triggers: [invented_fixture_constructor, undeclared_patch_target, noncanonical_dependency_surface]
summary: Blueprints must name canonical dependency and observation surfaces when tests need them.
```

### Invariant
If the tests must spy on a side effect or consume upstream types, the blueprint needs a canonical channel.

### Role Guidance
- @Architect: Specify canonical observable channels and upstream constructors where they are test-relevant.

---

## shared.observability-closure-underspecified: Under-Specified Canonical Closure For Operational Observability

```yaml
id: shared.observability-closure-underspecified
title: Under-Specified Canonical Closure For Operational Observability
roles: [Architect, Coder, Auditor]
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
- @Architect: Escalate upstream if satisfying the developer's input requires invented hooks or imports.

---

## shared.blueprint-missing-transitive-source-law: Transitive Source-Law Dependency In Blueprint Handoffs

```yaml
id: shared.blueprint-missing-transitive-source-law
title: Transitive Source-Law Dependency In Blueprint Handoffs
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [4, 5]
domains: [blueprint, source_law]
unit_domains: [agent-infra]
triggers: [out_of_blueprint_requirement, transitive_requirement, incomplete_blueprint_handoff]
summary: Level 4-5 blueprints must contain every downstream normative requirement locally.
```

### Invariant
The Coder must not need documents outside the blueprint to discover fixture matrices, literals, or behavior.

### Role Guidance
- @Architect: Copy implementable and testable obligations into the blueprint itself.

---

## shared.unanchored-import-surface-preimpl-tests: Unanchored Import Surface In Pre-Implementation Tests

```yaml
id: shared.unanchored-import-surface-preimpl-tests
title: Unanchored Import Surface In Pre-Implementation Tests
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, imports]
unit_domains: [agent-infra]
triggers: [missing_import_binding, invented_module_path, unauthorized_test_import]
summary: Blueprints requiring pre-implementation tests must provide an import path or binding mechanism.
```

### Invariant
The tests cannot exercise the public surface without a sanctioned binding.

### Role Guidance
- @Architect: Do not leave module path discretionary unless the test binding is also defined.

---

## shared.non-falsifying-contract-assertion: Non-Falsifying Contract Assertions

```yaml
id: shared.non-falsifying-contract-assertion
title: Non-Falsifying Contract Assertions
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, cli]
triggers: [weak_assertion, non_falsifying_test, generic_failure]
summary: Mandatory assertions should identify observable signals that weak tests cannot fake.
```

### Invariant
Assertions that only prove a scenario ran create false confidence.

### Role Guidance
- @Architect: Phrase mandatory assertions with contract-bearing observable outputs.

---

## shared.unreachable-mocked-failure-path: Unreachable Mocked Failure Paths

```yaml
id: shared.unreachable-mocked-failure-path
title: Unreachable Mocked Failure Paths
roles: [Coder, Architect, Auditor]
steps: [blueprint_draft, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests, mocks]
triggers: [unreached_mock, earlier_parser_failure, uncalled_patch]
summary: When a mandatory assertion distinguishes failure sources, name the source-specific observable.
```

### Invariant
A test can cover an earlier failure path while claiming to cover a later patched branch.

### Role Guidance
- @Architect: Specify source-specific observables when failure-source distinction matters.

---

## shared.hidden-ambient-dependency-pure-interface: Hidden Ambient Dependencies in Declared Pure Interfaces

```yaml
id: shared.hidden-ambient-dependency-pure-interface
title: Hidden Ambient Dependencies in Declared Pure Interfaces
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, code_audit]
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

## shared.divergent-validators-closed-failure-alphabet: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet

```yaml
id: shared.divergent-validators-closed-failure-alphabet
title: Divergent Validation Layers Behind a Claimed Closed Failure Alphabet
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, code_audit]
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

## shared.unreachable-operational-transition-restart: Unreachable Operational Transition From Valid Restart States

```yaml
id: shared.unreachable-operational-transition-restart
title: Unreachable Operational Transition From Valid Restart States
roles: [Architect, Coder, Auditor]
steps: [blueprint_draft, implementation, code_audit]
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
Frozen dependency paths and reachability obligations must match the shipped
upstream surface and the unit's own ordering rules.

### Role Guidance
- @Architect: Verify every consumed symbol against actual upstream exports and reconcile row reachability.

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
Framework-specific error codes, locations, and payloads are runtime facts, not
deductions from annotations.

### Role Guidance
- @Architect: Probe exact framework observables or mark them non-normative.

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
- @Architect: List the old directory and declare a delete (or a `deferred` entry) for every file in it; make the directory no longer existing part of the unit's observable signal.

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
- @Architect: State every absence signal in a form that cannot match compliant code, in the blueprint's own wording.

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
- @Architect: Scope a formatting mandate to new and rewritten code, and name the moved files it does not apply to.

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
- @Architect: When the blueprint mandates a value containing a token an existing forbidden-token scan bans, name that scan and require the tests to spell the value without the contiguous token.

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
- @Architect: After writing the outcome-alphabet table, walk it row by row against the pseudocode and ask 'which numbered step returns this value, and how does a test get the code there?'. If an early existence check maps an infrastructure error onto the not-found literal, either add a step that distinguishes the causes or scope the internal-error assertion to the post-check call only — and then add the per-operation failure control the test needs to your closed fake-change list, naming its signature and its error string. Never leave an assertion whose cause is erased by your own earlier step, and never require the Coder to compare against a driver sentinel that needs an import your forbidden list bans.

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
- @Architect: When a Public Surface mandates method order, state which observation channel reports order: reflect can only report the name set; order requires a source-text scan of the declaring file.

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
- @Architect: Before closing the edit set, grep for whole-repository scans (WalkDir helpers that self-exclude only their own file) whose forbidden pattern the mandated artifact necessarily matches; add the offending scan file to migration_scope with a bounded note, or record the collision as a numbered discrepancy — never leave the Definition of Done unsatisfiable.

---

## architect.assertion-count-needs-declared-observation-channel: A Call-Count Assertion Needs A Double That Records The Count

```yaml
id: architect.assertion-count-needs-declared-observation-channel
title: A Call-Count Assertion Needs A Double That Records The Count
roles: [Architect, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [3, 4, 5]
domains: [blueprint, tests, security]
triggers: [mandatory_assertion_names_call_count, closed_test_double_surface, observation_channel_table]
summary: Every quantitative clause in a Mandatory Assertion needs a declared observation channel; a double recording only the last call cannot falsify "exactly N calls".
```

### Invariant
A Mandatory Assertion is checkable only if the test-double surface the blueprint declares records the fact it names. When the declared double stores only the most recent call (plus a "was any made" flag), an "exactly one / exactly N calls" clause has no falsifier, and a closed fake surface additionally forbids the accessor that would supply one — so the Coder must either drop the clause, invent a forbidden fake member, or finish BLOCKED.

### Role Guidance
- @Architect: For every Mandatory Assertion, state the observable in the exact shape the declared double records it in. If the assertion names a count ("exactly one call", "zero calls"), either give the double a counter and list it in both the closed fake-surface list and the Authorized Observation Channels table, or restate the clause in the observable form ("at least one call, whose recorded request carries ..."; "no call recorded" via the recorded-flag). Never leave a count clause to the Coder's invention.

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
- @Architect: Resolve every construction the test scope prescribes against the file it names and write the form that compiles: when a fake's constructor returns the port interface but a test needs a concrete-only method, state the type assertion explicitly; when an assertion depends on ordering a reflection API does not preserve, split it into a set leg and a source-text leg.

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
- @Architect: When an outcome mapping or ordering rule appears in more than one closed section (Mandatory Assertion, State Transition Table, outcome-alphabet table), state it in each with the same semantic predicate - which question the step answers - never with a positional proxy such as 'before/after the permission check'. After writing the sections, walk each reachable input and confirm exactly one row of each table applies, and agrees with the pseudocode step that produces it.

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
- @Architect: When you scope a sibling unit's whole-repository scan (as with AS-126's TestACLSwapA7 whole-repository Authorize* absence scan), state the scan set as data in the amended test and assert it: read the scan-set variable or file list and assert it equals the declared paths, each of which must exist. Keep the bound 'every other line byte-identical' in the entry note, but do not rely on it as the only guard against a vacuous narrowing.

---

## architect.prescribed-test-calls-unverified-against-named-code: Blueprint-prescribed test calls not verified against the code they name

```yaml
id: architect.prescribed-test-calls-unverified-against-named-code
title: Blueprint-prescribed test calls not verified against the code they name
roles: [Architect, Auditor]
steps: [blueprint_draft, blueprint_audit, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, blueprint]
unit_domains: [api, agent-infra]
triggers: [blueprint_prescribes_test_construction, constructor_returns_port_interface, frozen_test_cannot_compile]
summary: Blueprint test sections must prescribe calls in the form the named code actually declares; an uncompilable prescription freezes the Coder before any code exists.
```

### Invariant
Every call, constructor, receiver and accessor a blueprint's test section prescribes must resolve as written against the code it names — a create entry's declared contract or a directly-required module at its pinned version. A prescription whose receiver type, parameter list or result list does not match that code is a blueprint defect: the freeze happens before any implementation exists, so the Coder can neither compile it nor lawfully change it.

### Role Guidance
- @Architect: In every test-scope section, write each prescribed construction, receiver call and accessor as the code it names declares — read the constructor's real result type (port interface vs concrete pointer), the parameter and result lists, and the accessor signature — and state the exact compiling form (e.g. a type assertion on a constructor that returns the port interface). If a prescribed call cannot resolve as written, fix the blueprint or record the requirement as BLOCKED; never leave the Coder a call that cannot compile, and never authorise a shim to paper over it.

---

## architect.closed-parent-set-not-declared-on-create: Create Entry Breaks A Frozen Closed-Set Assertion Over Its Parent

```yaml
id: architect.closed-parent-set-not-declared-on-create
title: Create Entry Breaks A Frozen Closed-Set Assertion Over Its Parent
roles: [Architect, Auditor]
steps: [blueprint_audit, implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, tests]
triggers: [create_entry_adds_sibling_under_existing_parent, frozen_test_pins_closed_directory_or_glob_set, repo_test_gate_reddens_from_new_sibling]
summary: A create entry must account for every frozen test that pins a closed set (directory listing, glob, count) over the new file's parent path, as an owned entry or an explicit deferral.
```

### Invariant
When a unit creates a file in a path whose parent directory already exists, the blueprint must declare any live assertion that pins the parent's item set as a closed list; such a create is not a "no other file changes" unit. The collision is invisible to the mechanical blueprint checks because the new path appears nowhere in the colliding assertion — the assertion counts the parent's children — so only an explicit search for set/count assertions over the parent path finds it.

### Role Guidance
- @Architect: Before declaring "no other file changes", search the test tree for closed-set assertions over the parent directory of every create path (os.ReadDir/glob over that parent, assert.Equal over a []string of names, item counts). For each hit, either add the file to migration_scope as modify/kind:test with the new closed set inlined plus a mandatory assertion pinning it, or put it in manifest.deferred naming a unit that really takes it and state that the repository gate stays red meanwhile. Never paper over it by scoping a gate assertion down to the unit's own packages.

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
- @Architect: Before closing a blueprint, grep the frozen tests for assertions that enumerate a set the new path joins (os.ReadDir/ls/glob directory sets, kustomization or manifest resource lists, entrypoint or route tables, package lists). If the unit creates a member, add that test file as a modify/kind:test entry with the new expected set inlined, amend any 'no other file changes' / 'do not edit test files' constraint, and add a mandatory assertion pinning the new set; otherwise name the owning later unit in deferred and record that the repository test command is red in the interval.

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
- @Architect: In `# Test Scope`, discharge each clause of a source-read behaviour assertion with its site-pinning form from your pseudocode (exact statement, the count your pseudocode fixes, or the adjacency), never a list of bare call tokens; reuse the needles the repo's tests already hold sibling files to, and check that no needle is also satisfied by another clause's text.

---

## architect.surface-references-absent-sibling-artifact: Surface Registers Or Asserts Against A Sibling Artifact Absent From The Tree

```yaml
id: architect.surface-references-absent-sibling-artifact
title: Surface Registers Or Asserts Against A Sibling Artifact Absent From The Tree
roles: [Architect, Auditor]
steps: [blueprint_draft, blueprint_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [blueprint, infra]
unit_domains: [infra]
triggers: [registration_list_modify, kustomization_resources_registration, surface_references_sibling_artifact, sibling_artifact_missing_from_tree, index_integrity_test]
summary: A path the Public Surface registers or asserts against must exist in the tree the unit starts from or be a declared entry; blueprint_checks validates only declared paths.
```

### Invariant
Every path a `# Public Surface` entry registers in a list file, or asserts a content fact about, exists in the tree the unit starts from, or that artifact is itself a declared entry of the same unit. `blueprint_checks` validates only declared entry paths, so a registration pointing at a sibling that was never landed passes the mechanical check and makes the repository's own index-integrity test unsatisfiable for this unit.

### Role Guidance
- @Architect: When `# Public Surface` adds entries to a registry/list file (kustomization `resources`, package/workspace indexes) or asserts a content fact about a sibling artifact, verify each named path exists in the tree the unit starts from; if it does not, either add the artifact as a declared `create` entry with its content fully pinned, or drop it from the list and record the absence as pre-existing debt in `# Spec Discrepancies`. Never state a count over a list whose members you have not checked, and never require a currently-green test to keep passing while mandating a registration that turns it red.

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
- @Architect: Before publishing the blueprint, diff the ids in # Mandatory Assertions against # Test Scope and give each one a home: name the test that covers it, or state in Test Scope that it is a bound/legacy fact checked at code_audit only. Never leave a mandatory assertion naming a file the unit creates with no test named for it, and never rely on a per-sibling test table silently covering the new mirror.
