# agents_antipatterns

Shared anti-pattern knowledge base for the [Nexus Swarm](https://github.com/alberto-velo/agentic-infra) agent pipeline.

The Nexus Swarm runs one AI agent per pipeline step (Roadmap Agent, Risk Agent, Architect,
Auditor, Coder) to assist with feature development. During each run, the Auditor logs reusable
root causes it encounters. This repo is where those patterns accumulate, get reviewed, and
propagate to the whole team.

---

## How it works

1. **Auditor proposes** — during an audit step, if the Auditor finds a reusable root cause that
   no rule in force already covers, it calls its `propose_anti_pattern` tool with the fields of
   an entry (the file it belongs in, a name, title, domains, the lowest risk level it binds at,
   and the rule text) plus the evidence. The runner checks the entry against this repo's format
   — a malformed entry or an id already here is refused — and records it in
   `proposed_anti_patterns.json` in the local workspace. The developer can delete any proposal
   there that does not hold.
2. **`nexus submit` opens the PR** — at the end of a feature run, the runner clones this repo,
   creates the branch `nexus/proposals/<feature>`, appends each entry once to the file it names,
   validates the registry, pushes, and opens the PR with the GitHub CLI. The PR body shows each
   rule as it will be in force, the run's evidence under it (context for review, not part of the
   rule), and each changed file's rule count and size before and after. Running it again
   rebuilds the branch and updates the open PR.
3. **Human reviews** — a team member reads the PR, edits or drops noisy entries on its branch,
   and merges. When a file grows, merge overlapping rules or retire ones that no longer earn
   their place.
4. **Rules propagate** — on the next `nexus update-governance` / `nexus bootstrap`, the runner
   pulls the latest commit from this repo and puts every rule that applies to a step into that
   step's handoff.

No rule takes effect until it is merged. Unreviewed auto-learning never reaches the pipeline.

Opening the PR needs push access to this repo and an authenticated `gh` (`gh auth login`) on
the developer's machine. Without them, `nexus submit` warns, says what it did (for example,
the branch was pushed and here is the link to open the PR), and keeps the proposals for a
re-run. `nexus submit --no-governance-pr` skips the PR.

---

## Directory structure

```
anti_patterns/
  architect.md   ← mistakes in the blueprint          — read at blueprint_draft, blueprint_audit
  coder.md       ← mistakes in the unit's tests/code  — read at implementation, code_audit
  shared.md      ← mistakes either author makes       — read at all four steps above
  auditor.md     ← mistakes of an audit itself        — read at blueprint_audit, code_audit
CHANGELOG.md     ← record of significant rule changes
```

Each rule is in exactly one file, and the file is whose mistake the rule prevents — which is who
reads it. An audit reads the rules of the work it judges, `shared.md` and `auditor.md`; it never
reads the other author's rules. The Roadmap and Risk agents read none. A step receives **every**
rule in its files that applies to the unit's domain and risk level — nothing is ranked or cut,
so what a file holds is what its readers get.

The runner refuses a registry in an earlier layout (one file per role, each rule copied into the
file of every role it listed).

---

## Entry format

````markdown
## coder.my-new-pattern: Short Title

```yaml
id: coder.my-new-pattern
title: Short Title
domains: [api, frontend]
from_level: 1
```

What goes wrong, and what to do instead.
````

- **id** — `<file>.<kebab-name>`; the prefix is the file the entry is in.
- **domains** — the unit domains where the mistake can happen: `api`, `frontend`, `agent-infra`,
  `infra`, or `[all]` on its own for a rule that does not depend on the kind of code. Required.
- **from_level** — the lowest risk level (0-5) the rule binds at.
- **the rule** — plain text after the block, true in any repository: it names the kind of thing,
  never a unit, file or identifier of the codebase where it was found. No `#` headings, `---`
  lines or code fences inside it. A rule that holds for one language only says so.

These four fields are the whole format; anything else is refused.

---

## Adding a rule manually

1. Add the entry to the file of the role whose mistake it prevents.
2. Validate it locally from this repo's root: `nexus anti-patterns validate --registry-root
   anti_patterns` — there is no CI check on this repo.
3. Open a PR — title: `feat(antipatterns): add <id>`.
4. Get a single teammate approval and merge.

The rule will be active for the whole team on their next `nexus update-governance`.

---

## Pointing the runner at this repo

This repo's URL is built into the runner — no configuration needed. Do an initial pull
after installing the runner:

```bash
nexus update-governance
```

Run `nexus update-governance` again any time you want to pick up new rules before starting
a feature.

To override with a fork or a local clone:
```bash
# Remote fork
export NEXUS_GOVERNANCE_REMOTE=https://github.com/your-org/agents_antipatterns.git

# Local clone (useful when editing rules directly)
export NEXUS_GOVERNANCE_DIR=/path/to/local/agents_antipatterns
```
