# agents_antipatterns

Shared anti-pattern knowledge base for the [Nexus Swarm](https://github.com/alberto-velo/agentic-infra) agent pipeline.

The Nexus Swarm runs one AI agent per pipeline step (Roadmap Agent, Risk Agent, Architect,
Auditor, Coder) to assist with feature development. During each run, the Auditor logs reusable root causes it
encounters. This repo is where those patterns accumulate, get reviewed, and propagate to the
whole team.

---

## How it works

1. **Auditor proposes** — during an audit step, if the Auditor finds a reusable root cause
   it calls its `propose_anti_pattern` tool with the fields of a registry entry (id, roles,
   steps, risk levels, one instruction per role, …) plus the evidence. The runner checks the
   entry against this repo's format — a retired role or step, a role without an instruction,
   or an id already here is refused — and records it in `proposed_anti_patterns.json` in the
   local workspace. The developer can delete any proposal there that does not hold.
2. **`nexus complete` opens the PR** — at the end of a feature run, the runner clones this
   repo, creates the branch `nexus/proposals/<feature>`, adds each entry to the file of every
   role it lists, validates the registry, pushes, and opens the PR with the GitHub CLI, the
   evidence in its body. Running it again rebuilds the branch and updates the open PR.
3. **Human reviews** — a team member reads the PR, edits or drops noisy entries on its branch,
   and merges.
4. **Rules propagate** — on the next `nexus update-governance` / `nexus bootstrap`, the runner
   pulls the latest commit from this repo and injects the entries relevant to each agent step
   into that step's handoff.

No rule takes effect until it is merged. Unreviewed auto-learning never reaches the pipeline.

Opening the PR needs push access to this repo and an authenticated `gh` (`gh auth login`) on
the developer's machine. Without them, `nexus complete` warns, says what it did (for example,
the branch was pushed and here is the link to open the PR), and keeps the proposals for a
re-run. `nexus complete --no-governance-pr` skips the PR.

---

## Directory structure

```
anti_patterns/
  roadmap.md           ← patterns for the Roadmap Agent (roadmap_draft)
  risk.md              ← patterns for the Risk Agent (risk_classification)
  architect.md         ← patterns for the Architect (blueprint_draft)
  auditor.md           ← patterns for the Auditor (blueprint_audit, code_audit)
  coder.md             ← patterns for the Coder (implementation: the unit's tests, then its code)
  shared.md            ← cross-role patterns, read by every agent
CHANGELOG.md           ← record of significant rule changes
```

One file per agent role; the runner reads exactly these files and refuses a registry that
still has a retired role file (`staff-engineer.md`, `qa-sdet.md`). An entry may appear in more
than one role file when the same invariant has different role-specific guidance — IDs remain
stable across files, and each copy carries that role's guidance line. Entries the runner adds
from a proposal follow this convention: one copy in the file of every role the entry lists.

---

## Entry format

Each entry is a markdown section with an embedded YAML metadata block:

````markdown
## scope.my-new-pattern: Short Title

```yaml
id: scope.my-new-pattern
title: Short Title
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [code]
unit_domains: [api]
triggers: [short_trigger_name]
summary: One concise sentence — injected into agent handoffs.
```

### Invariant
The reusable invariant at risk.

### Role Guidance
- @Coder: Concrete instruction for this role.
- @Auditor: What to check and how to fail it.
````

**Required fields:** id, title, roles, steps, risk_levels, domains, triggers, summary
(`unit_domains` is optional; omit it when the rule applies in every domain).

See `anti_patterns/README.md` for the full field reference and validation instructions.

---

## Adding a rule manually

If you spot a recurring pattern that isn't being caught, you can add it directly:

1. Open the relevant `anti_patterns/<role>.md` (or `shared.md` for a cross-role rule).
2. Add an entry following the format above.
3. Validate it locally from this repo's root: `nexus anti-patterns validate --registry-root
   anti_patterns` — there is no CI check on this repo.
4. Open a PR — title: `feat(antipatterns): add <id>`.
5. Get a single teammate approval and merge.

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
