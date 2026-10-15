# Auditor Anti-Patterns

Mistakes of an audit itself. Read by the Auditor at `blueprint_audit` and `code_audit`.

---

## auditor.prove-coverage-by-mutation: Test Coverage Judged By Reading Instead Of Mutating

```yaml
id: auditor.prove-coverage-by-mutation
title: Test Coverage Judged By Reading Instead Of Mutating
domains: [all]
from_level: 0
```

Whether a test covers an assertion is decided by breaking what it covers, not by reading the test's name or body. At code audit, remove or change each asserted fact in a disposable copy with the sandbox — one clause at a time, leaving the others intact — and confirm a test fails. A suite that stays green is missing coverage, routed TESTS when the blueprint gave what the test needed and ARCHITECT when it did not.

---

## auditor.absence-signal-hit-in-test-literal: Absence Signal Hit In A Test Literal Treated As A Violation

```yaml
id: auditor.absence-signal-hit-in-test-literal
title: Absence Signal Hit In A Test Literal Treated As A Violation
domains: [all]
from_level: 0
```

An absence signal for a retired name means the name is not declared or referenced by live code. A hit inside a test string literal, an assertion argument or a comment is not a violation — never fail the Coder for it. A signal stated as a raw substring search is a blueprint defect, routed ARCHITECT at blueprint audit.
