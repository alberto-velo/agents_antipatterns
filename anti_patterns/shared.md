# Shared Anti-Patterns

Mistakes either author can make on their own. Read by the Architect, the Coder and the Auditor at both audits.

---

## shared.security-metadata-open-type: Open Types In Access-Control Fields

```yaml
id: shared.security-metadata-open-type
title: Open Types In Access-Control Fields
domains: [all]
from_level: 4
```

An unconstrained string or number in an access-control field lets unexpected values through. In the blueprint, specify a closed or constrained type for every access-path field; in the code, never implement one as an open type.

---

## shared.security-boundary-fail-open: Security Boundary Fails Open

```yaml
id: shared.security-boundary-fail-open
title: Security Boundary Fails Open
domains: [all]
from_level: 4
```

Malformed or unexpected input at a security boundary must be denied, never fall through to allow. In the blueprint, define the fail-closed outcome for every malformed-input class; in the code, make every exception handler and default path on a security check deny explicitly.

---

## shared.format-mandate-breaks-byte-identity: Formatter Mandate Contradicts A Byte-Identity Assertion

```yaml
id: shared.format-mandate-breaks-byte-identity
title: Formatter Mandate Contradicts A Byte-Identity Assertion
domains: [all]
from_level: 0
```

A formatter run on every changed file rewrites pre-existing formatting defects in a moved file whose content is pinned byte for byte; the byte-identity assertion wins. In the blueprint, scope a formatting mandate to new and rewritten code and name the pinned files it excludes; in the code, leave such a defect untouched, and if the assertion goes red on a formatting-only diff, revert the formatting, never the test.

---

## shared.test-literal-trips-forbidden-token-scan: Asserted Literal Trips A Repository-Wide Forbidden-Token Scan

```yaml
id: shared.test-literal-trips-forbidden-token-scan
title: Asserted Literal Trips A Repository-Wide Forbidden-Token Scan
domains: [all]
from_level: 0
```

A test that states, as one contiguous literal, a value containing a token an existing repository-wide scan bans passes its own command and fails the repository's full test run. In the blueprint, name such a scan when it mandates the value; in the tests, assemble the literal from fragments so the compared value is unchanged, and run the repository's full test command before freezing.

---

## shared.reflection-cannot-report-declaration-order: Reflection Cannot Report Declaration Order (Go)

```yaml
id: shared.reflection-cannot-report-declaration-order
title: Reflection Cannot Report Declaration Order (Go)
domains: [all]
from_level: 2
```

Go's reflection lists an interface's methods sorted by name, whatever order they are declared in, so a test that maps a mandated declaration order onto reflection indexes fails for every compliant interface. In the blueprint, say that order is observed from the source text; in the tests, compare reflected names as a sorted set and check order by reading the declaring file.

---

## shared.source-scan-needle-must-pin-its-clause: Source-Scan Needle Satisfied By Any Implementation

```yaml
id: shared.source-scan-needle-must-pin-its-clause
title: Source-Scan Needle Satisfied By Any Implementation
domains: [all]
from_level: 2
```

When an assertion with several behaviour clauses is checked by reading a file's source, each clause needs a needle only that clause satisfies: the exact statement, an occurrence count the pseudocode fixes, or the adjacency of a guarded call and its handler. A bare token every implementation contains, or a literal also present on another clause's path, still passes when the clause is dropped. In the blueprint, prescribe the site-pinning form; in the tests, check every occurrence of each needle and prove each clause by deleting it from a scratch copy.

---

## shared.mandatory-assertion-without-a-test: Mandatory Assertion With No Test That Checks It

```yaml
id: shared.mandatory-assertion-without-a-test
title: Mandatory Assertion With No Test That Checks It
domains: [all]
from_level: 0
```

The tests are written from the test list, so an assertion no listed test covers ships unverified — absence-shaped and carried-over assertions most often. In the blueprint, give every mandatory assertion a named test, or declare it checked at code audit only; in the tests, walk the assertion list itself, not only the test list, and name the test that would fail if each fact were removed.

---

## shared.canonical-artifact-path-drift: Handoff Artifact Away From Its Canonical Path

```yaml
id: shared.canonical-artifact-path-drift
title: Handoff Artifact Away From Its Canonical Path
domains: [agent-infra]
from_level: 0
```

A pipeline artifact that serves as a handoff contract must be found at one canonical path, or through a deterministic entrypoint — never by searching the filesystem. Write such artifacts at their canonical path, and never work from a fragment found by search.
