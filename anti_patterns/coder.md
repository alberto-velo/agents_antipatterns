# Coder Anti-Patterns

Mistakes in the unit's tests or code. Read by the Coder at `implementation` and by the Auditor at `code_audit`.

---

## coder.ghost-coverage-placeholder-tests: Placeholder Or Uncollected Tests Counted As Coverage

```yaml
id: coder.ghost-coverage-placeholder-tests
title: Placeholder Or Uncollected Tests Counted As Coverage
domains: [all]
from_level: 0
```

A test counts only when an executable assertion is collected and run. A body that is empty, a TODO, a comment, skipped, or nested where the runner never collects it is missing coverage. Map every required surface item and assertion to an executable assertion the test command collects.

---

## coder.non-falsifying-contract-assertion: Test That Cannot Fail Against A Broken Implementation

```yaml
id: coder.non-falsifying-contract-assertion
title: Test That Cannot Fail Against A Broken Implementation
domains: [all]
from_level: 0
```

A test that only proves a scenario ran passes against an implementation that omits the required behaviour. For each test, name the observable signal it checks and the plausible broken implementation it would catch; implement the required behaviour even where a weak test would not notice its absence.

---

## coder.unreachable-mocked-failure-path: Mocked Failure Path Never Reached

```yaml
id: coder.unreachable-mocked-failure-path
title: Mocked Failure Path Never Reached
domains: [all]
from_level: 0
```

A test of a downstream failure path can fail earlier — on invalid preceding input — and still pass, without ever reaching the patched dependency. Give it valid preceding inputs and assert that the patched dependency was called, or that output carries a value only the patch produces.

---

## coder.mocked-composition-misses-public-symbol: Public Symbol Covered Only Through A Mock

```yaml
id: coder.mocked-composition-misses-public-symbol
title: Public Symbol Covered Only Through A Mock
domains: [all]
from_level: 4
```

An entrypoint test that mocks a public dependency does not verify that dependency's own contract. Every public symbol of a closed surface needs direct executable coverage: build a checklist of the surface's symbols before writing scenario coverage.

---

## coder.hardening-blocks-authorized-input: Hardening That Rejects Authorized Input

```yaml
id: coder.hardening-blocks-authorized-input
title: Hardening That Rejects Authorized Input
domains: [all]
from_level: 3
```

Tightened validation is a regression when it rejects an input shape the contract authorizes. Keep positive handling, and a test, for every legitimate input shape the contract lists.

---

## coder.convenience-api-erases-probe-semantics: Convenience API Erases A Required Failure Branch

```yaml
id: coder.convenience-api-erases-probe-semantics
title: Convenience API Erases A Required Failure Branch
domains: [agent-infra]
from_level: 4
```

A helper that collapses an I/O failure into "not found" or false erases a branch the contract may require: a missing artifact and a failed probe are distinct outcomes when the contract says so. Use the explicit operation whose failures reach every required branch.

---

## coder.constructor-bypasses-schema-validation: Untrusted Data Built Through A Constructor

```yaml
id: coder.constructor-bypasses-schema-validation
title: Untrusted Data Built Through A Constructor
domains: [agent-infra]
from_level: 3
```

Building a model from decoded untrusted data through its constructor can raise a host-language error before schema validation runs, outside the declared failure channel. Validate untrusted rows with the schema API that accepts arbitrary objects and reports through the declared channel.

---

## coder.defensive-fallback-branch-omitted: Declared Fallback Branch Dropped

```yaml
id: coder.defensive-fallback-branch-omitted
title: Declared Fallback Branch Dropped
domains: [all]
from_level: 4
```

A fallback branch the blueprint declares is part of the contract even when earlier validation usually prevents it. Implement it as declared, and reach it in a test with input that gets there, asserting its exact literal or payload.

---

## coder.side-effect-invariant-unproven: Side-Effect Obligation Asserted Through The Return Value

```yaml
id: coder.side-effect-invariant-unproven
title: Side-Effect Obligation Asserted Through The Return Value
domains: [all]
from_level: 4
```

A correct payload does not prove that a forbidden call was not made, that ordering held, or that state was preserved. Assert side-effect obligations directly on the collaborator that observes them, and implement them even when a test checks only a weaker signal.

---

## coder.import-masking-hides-failure: Test Import Failure Masked

```yaml
id: coder.import-masking-hides-failure
title: Test Import Failure Masked
domains: [agent-infra]
from_level: 0
```

A test that wraps the import of the code under test in a fallback passes green when that code is missing or broken. Import it directly, so a missing implementation fails collection.

---

## coder.test-invents-unlicensed-contract: Test Asserts A Contract The Blueprint Does Not Give

```yaml
id: coder.test-invents-unlicensed-contract
title: Test Asserts A Contract The Blueprint Does Not Give
domains: [all]
from_level: 3
```

A test validates the contract only when everything it asserts traces back to the blueprint or to existing behaviour: error codes, route strings, literals, helper names, import paths, patch targets, constructors. Never invent one, and never add an implementation shim, alias or helper only so an invented test surface resolves — record the gap instead.

---

## coder.partial-structured-error-assertion: Structured Error Asserted On One Field

```yaml
id: coder.partial-structured-error-assertion
title: Structured Error Asserted On One Field
domains: [all]
from_level: 3
```

When the contract defines a structured error, checking one convenient field lets a non-verbatim translation pass. Assert every field that distinguishes a compliant error from a partial one.

---

## coder.state-matrix-subcomponent-only-coverage: State Rows Tested Only At A Helper

```yaml
id: coder.state-matrix-subcomponent-only-coverage
title: State Rows Tested Only At A Helper
domains: [all]
from_level: 4
```

A helper test does not prove that the public entrypoint emits the right payload for each state. Test every externally observable row of a closed state matrix at the entrypoint that produces it.

---

## coder.anchored-regex-whole-file-matcher: Line-Anchored Pattern Matched Against A Whole File

```yaml
id: coder.anchored-regex-whole-file-matcher
title: Line-Anchored Pattern Matched Against A Whole File
domains: [all]
from_level: 0
```

A line-shape pattern (^...$) applied with a whole-string matcher anchors to the whole input, so it never matches a multi-line file and no correct implementation satisfies it. Make every line-shape match line-aware — split into lines or enable multiline mode — and check it against a representative multi-line body.

---

## coder.test-file-static-analysis-gate: Test Files Fail The Repository's Static Analysis

```yaml
id: coder.test-file-static-analysis-gate
title: Test Files Fail The Repository's Static Analysis
domains: [all]
from_level: 0
```

The repository's static-analysis commands collect test files too, and a finding in a frozen test can only be fixed through a TESTS round. Before freezing, compile and lint the test files with commands that report every error, not just the first. Fix every finding except an error that only names a symbol a code entry declares and has not written yet.

---

## coder.unasserted-carried-over-behaviour: Carried-Over Behaviour Left Unasserted In A Move

```yaml
id: coder.unasserted-carried-over-behaviour
title: Carried-Over Behaviour Left Unasserted In A Move
domains: [all]
from_level: 0
```

When a unit recreates files under new paths, a rewrite loses exactly the facts nobody checks. For each assertion that says behaviour is preserved or carried over unchanged, assert every literal and behaviour its signal names — startup and shutdown, exit paths, error handling — not only what distinguishes the new files.

---

## coder.multi-clause-signal-partially-asserted: Compound Signal Only Partly Asserted

```yaml
id: coder.multi-clause-signal-partially-asserted
title: Compound Signal Only Partly Asserted
domains: [all]
from_level: 0
```

An assertion whose signal names several files, literals or fields is covered only when each named fact is read and asserted by some test. List every fact a compound signal names and assert each one; a scanner that does not read a file does not cover it.

---

## coder.layering-assertion-without-matching-scan: Layering Assertion Without A Scan That Can Match It

```yaml
id: coder.layering-assertion-without-matching-scan
title: Layering Assertion Without A Scan That Can Match It
domains: [agent-infra, api]
from_level: 0
```

A dependency-direction or layering rule changes no behaviour, so behavioural and compile-time tests stay green when it breaks. Give each layering assertion its own scan of the unit's production imports, proven on a synthetic tree in both directions; never scan a transitive dependency graph, which also reports packages acquired indirectly and fails compliant code.

---

## coder.derivative-surface-asserts-deltas-only: Mirrored Artifact Asserted Only For Its Differences

```yaml
id: coder.derivative-surface-asserts-deltas-only
title: Mirrored Artifact Asserted Only For Its Differences
domains: [all]
from_level: 2
```

When a surface entry is defined as identical to another except for named differences, every carried-over fact binds the mirror too. Assert the base entry's facts against the mirror, not only the differences.

---

## coder.derived-order-assertion-over-random-keys: Order Assertion Over Randomly Generated Keys

```yaml
id: coder.derived-order-assertion-over-random-keys
title: Order Assertion Over Randomly Generated Keys
domains: [all]
from_level: 0
```

When fixtures are keyed by randomly generated identifiers and the code returns them sorted, comparing against seeding order fails on some runs against a correct implementation. Derive the expected order from the same rule — sort the seeded keys — or compare the set plus the returned order.

---

## coder.aliasing-non-mutation-assertion: No-Write Assertion Compared Against The Double's Own Object

```yaml
id: coder.aliasing-non-mutation-assertion
title: No-Write Assertion Compared Against The Double's Own Object
domains: [all]
from_level: 2
```

A check that a path writes nothing cannot fail if it re-reads a test double and compares against the object it seeded, when the double stores that same object. Snapshot the asserted fields and the record count into separate values before the call, or assert literals after it, on a success and a failure case of every operation the assertion names.
