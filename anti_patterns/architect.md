# Architect Anti-Patterns

Mistakes in the blueprint. Read by the Architect at `blueprint_draft` and by the Auditor at `blueprint_audit`.

---

## architect.stateful-component-missing-lifecycle: Stateful Component Without Lifecycle Semantics

```yaml
id: architect.stateful-component-missing-lifecycle
title: Stateful Component Without Lifecycle Semantics
domains: [agent-infra]
from_level: 2
```

State that persists across task or execution boundaries leaks between runs unless the blueprint defines how it is reset. When a blueprint introduces stateful storage, give it a lifecycle section: what clears it, when, and the clean-slate behaviour a test can observe.

---

## architect.unspecified-transformation-path: Unspecified Transformation Path

```yaml
id: architect.unspecified-transformation-path
title: Unspecified Transformation Path
domains: [all]
from_level: 2
```

Where bytes or comparisons matter, two reasonable implementations of an unnamed transformation produce different results. Spell out every intermediate serialization, encoding, hashing or normalization step the contract depends on; never leave a non-trivial transformation to inference.

---

## architect.blueprint-drift-adds-to-source-law: Blueprint Adds To The Source Contract

```yaml
id: architect.blueprint-drift-adds-to-source-law
title: Blueprint Adds To The Source Contract
domains: [all]
from_level: 4
```

At L4-5 an addition that looks stricter or more helpful than the source contract — an extra alias, a narrower type, a partial restatement of a subcontract — is a second contract, and the code then follows the wrong one. Reproduce the source surfaces and subcontracts verbatim; a change to them comes from revised developer input, not from the blueprint.

---

## architect.canonical-observation-channel-missing: Test-Relevant Dependency Or Observation Channel Not Named

```yaml
id: architect.canonical-observation-channel-missing
title: Test-Relevant Dependency Or Observation Channel Not Named
domains: [all]
from_level: 3
```

When the tests must observe a side effect or build an upstream type, and the blueprint does not name the canonical channel or constructor, the tests invent one and the code grows a shim to satisfy it. Name each observation channel and upstream constructor the tests need, in the form the code that provides it declares.

---

## architect.observability-closure-underspecified: Mandatory Side Effect Without A Legal Mechanism

```yaml
id: architect.observability-closure-underspecified
title: Mandatory Side Effect Without A Legal Mechanism
domains: [agent-infra]
from_level: 4
```

A mandatory operational side effect needs both a compliant way to produce it and a way for a test to observe it, within what the contract allows. When satisfying the input would need an invented hook or a forbidden import, do not encode the workaround: record the gap so the input is revised. Repeated downstream fixes oscillating around the same obligation are the sign of this gap.

---

## architect.blueprint-missing-transitive-source-law: Normative Detail Left Outside The Blueprint

```yaml
id: architect.blueprint-missing-transitive-source-law
title: Normative Detail Left Outside The Blueprint
domains: [agent-infra]
from_level: 4
```

The Coder works from the blueprint alone. A fixture matrix, literal or behaviour that is only referenced — present in a document outside the blueprint — is invisible to it. At L4-5, copy every implementable and testable obligation into the blueprint itself.

---

## architect.unanchored-import-surface-preimpl-tests: No Binding For Tests Written Before The Code

```yaml
id: architect.unanchored-import-surface-preimpl-tests
title: No Binding For Tests Written Before The Code
domains: [agent-infra]
from_level: 0
```

Tests are written and frozen before the code exists, so they can only reach the public surface through a binding the blueprint names. Do not leave a module or import path to the Coder's discretion unless the blueprint also defines how the tests bind to it.

---

## architect.hidden-ambient-dependency-pure-interface: Hidden Ambient Dependency In A Declared Pure Interface

```yaml
id: architect.hidden-ambient-dependency-pure-interface
title: Hidden Ambient Dependency In A Declared Pure Interface
domains: [all]
from_level: 3
```

A function described as pure or argument-driven must not change behaviour on runtime context it does not receive. Put each behaviour-changing input (tier, mode, phase, configuration) in the interface, or declare the ambient dependency explicitly.

---

## architect.divergent-validators-closed-failure-alphabet: Closed Failure Alphabet Ignores A Validation Layer

```yaml
id: architect.divergent-validators-closed-failure-alphabet
title: Closed Failure Alphabet Ignores A Validation Layer
domains: [all]
from_level: 4
```

A closed set of failure outcomes must account for every layer that can reject input. When the contract names a canonical validator and a later layer (a schema, a framework) can reject more, state whether that layer is representational only or may reject, and which outcome its rejection maps to — never let a later rejection surface under an unrelated canonical cause.

---

## architect.unreachable-operational-transition-restart: Valid Restart Path Never Reaches The Operational State

```yaml
id: architect.unreachable-operational-transition-restart
title: Valid Restart Path Never Reaches The Operational State
domains: [agent-infra]
from_level: 4
```

Every successful startup or restart path must say how the system leaves its initial restricted mode and reaches its declared operational state. Enumerate every successful transition source, not only first boot; a restart path with no transition leaves the Coder to invent one.

---

## architect.source-law-uses-nonexistent-upstream-api: Contract Uses An Upstream API That Does Not Exist

```yaml
id: architect.source-law-uses-nonexistent-upstream-api
title: Contract Uses An Upstream API That Does Not Exist
domains: [agent-infra]
from_level: 3
```

A unit that composes already-shipped units must consume the APIs they actually export, and may require only test rows its own ordering rules make reachable. Verify every consumed symbol against the upstream code, and reconcile each required row against the order of the steps that produce it.

---

## architect.unprobed-framework-error-literal: Framework Diagnostic Made Normative Without Probing It

```yaml
id: architect.unprobed-framework-error-literal
title: Framework Diagnostic Made Normative Without Probing It
domains: [agent-infra]
from_level: 4
```

Framework error codes, messages, locations and payloads are runtime facts, not deductions from annotations or documentation. Before making one normative, probe it against the project's own runtime; otherwise mark it non-normative.

---

## architect.incomplete-delete-set-orphan-directory: Relocation Leaves Files Behind In The Old Directory

```yaml
id: architect.incomplete-delete-set-orphan-directory
title: Relocation Leaves Files Behind In The Old Directory
domains: [all]
from_level: 0
```

When a unit moves a directory as create-at-new-path plus delete-at-old, every file left in the old directory must be deleted or deferred — including test files that only reference package-local symbols, which match no removed symbol or moved path and so escape the mechanical checks. A directory left holding only tests can break the build. List the old directory and declare a delete or a deferral for every file in it.

---

## architect.raw-grep-absence-signal: Absence Signal That Compliant Code Matches

```yaml
id: architect.raw-grep-absence-signal
title: Absence Signal That Compliant Code Matches
domains: [all]
from_level: 0
```

A repository-wide raw substring search for a retired name necessarily matches the test asserting its absence, a fixture proving a detector fires, and any stale comment, so no correct implementation satisfies it. State every absence signal in a form compliant code cannot match: declaration-shaped or structural (a syntax-tree or line-shape check, a symbol query, a needle assembled from parts).

---

## architect.unreachable-mandatory-outcome: Mandatory Outcome With No Path That Produces It

```yaml
id: architect.unreachable-mandatory-outcome
title: Mandatory Outcome With No Path That Produces It
domains: [api]
from_level: 3
```

For a unit with a closed set of outcomes, each mandated outcome needs a step in the pseudocode that produces it and an authorized test hook that drives execution there. When an earlier step maps an error onto a different outcome, an assertion demanding the original cause for that event contradicts the pseudocode. Walk the outcome table row by row: name the step that returns each value and how a test reaches it, and add any failure control the test needs to the closed test-double surface.

---

## architect.closed-edit-set-vs-repo-absence-scan: Closed Edit Set Collides With A Repository-Wide Absence Scan

```yaml
id: architect.closed-edit-set-vs-repo-absence-scan
title: Closed Edit Set Collides With A Repository-Wide Absence Scan
domains: [all]
from_level: 2
```

A blueprint that forbids touching any file outside its entries must check that what it mandates trips no existing repository-wide forbidden-pattern scan. If it does, either bring the scan file into scope with an explicit bound, or record the collision as a discrepancy for the developer — otherwise the unit cannot finish with the repository's tests green.

---

## architect.count-assertion-needs-observation-channel: Quantity Assertion Without A Double That Records It

```yaml
id: architect.count-assertion-needs-observation-channel
title: Quantity Assertion Without A Double That Records It
domains: [all]
from_level: 3
```

An assertion is checkable only if the declared test double records the fact it names. A double that keeps only the last call and a was-called flag cannot falsify "exactly N calls". State each assertion in the shape the double records, or give the double a counter and list it in the closed fake surface and the observation channels.

---

## architect.prescribed-test-calls-must-compile: Prescribed Test Code Does Not Match The Code It Names

```yaml
id: architect.prescribed-test-calls-must-compile
title: Prescribed Test Code Does Not Match The Code It Names
domains: [all]
from_level: 0
```

Tests are frozen before the code exists, so a constructor, receiver call or accessor the blueprint prescribes for a test must resolve exactly as the named code declares it: its result type (an interface or a concrete type), parameter list and result list. A prescription that does not compile cannot be fixed by any change the blueprint permits. Resolve each one against the file or pinned module it names, and write the compiling form explicitly (for example a type assertion on a constructor that returns an interface).

---

## architect.restated-rule-predicates-must-match: One Rule Restated With Different Predicates

```yaml
id: architect.restated-rule-predicates-must-match
title: One Rule Restated With Different Predicates
domains: [api]
from_level: 4
```

When one outcome or ordering rule appears in several sections — an assertion, a state-transition table, an outcome table — every restatement must use the same predicate as the pseudocode, at the step the pseudocode places it. A positional proxy ("before the permission check") maps one reachable input to two outcomes. Walk each reachable input and confirm exactly one row of each table applies and agrees with the pseudocode.

---

## architect.narrowed-absence-scan-must-assert-its-scan-set: Narrowed Inherited Absence Scan Without An Asserted Scan Set

```yaml
id: architect.narrowed-absence-scan-must-assert-its-scan-set
title: Narrowed Inherited Absence Scan Without An Asserted Scan Set
domains: [api, agent-infra]
from_level: 4
```

When a unit resolves a conflict with an earlier unit's repository-wide absence test by narrowing that scan to a declared set of paths, the scan set itself must be asserted: the amended test checks that it walks exactly the declared paths and that each exists. Otherwise a narrowing to an empty or renamed set passes and the earlier invariant dies silently.

---

## architect.create-joins-a-frozen-closed-set: New File Joins A Set An Existing Test Pins

```yaml
id: architect.create-joins-a-frozen-closed-set
title: New File Joins A Set An Existing Test Pins
domains: [all]
from_level: 0
```

A file created under an existing directory can break a test that pins that directory's contents as a closed list or count — a directory listing, a resource list, a route or package table. The mechanical checks cannot see it, because the new path never appears in the colliding test. For every create entry, search for tests that enumerate the set it joins; bring each into scope with the new expected set, or defer it to a named unit and state that the repository's tests are red until then.

---

## architect.surface-references-absent-artifact: Surface Registers An Artifact Missing From The Tree

```yaml
id: architect.surface-references-absent-artifact
title: Surface Registers An Artifact Missing From The Tree
domains: [infra]
from_level: 0
```

A path the public surface registers in a list file, or asserts content of, must exist in the tree the unit starts from or be an entry of the unit; the mechanical checks validate only declared entries. Check each named path; when one is missing, declare it as a create with its content pinned, or drop it and record the absence as a discrepancy. Never state a count over list members you have not checked.
