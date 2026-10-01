# agents_antipatterns

Shared anti-pattern knowledge base for the [Nexus Swarm](https://github.com/alberto-velo/agentic-infra) agent pipeline.

The Nexus Swarm runs one AI agent per pipeline step (Roadmap Agent, Risk Agent, Architect,
Auditor, QA-SDET, Coder) to assist with feature development. During each run, the Auditor logs reusable root causes it
encounters. This repo is where those patterns accumulate, get reviewed, and propagate to the
whole team.

---

## How it works

1. **Auditor proposes** — during an audit step, if the Auditor finds a reusable root cause
   it calls its `propose_anti_pattern` tool, and the runner appends the candidate entry to
   `proposed_anti_patterns.md` in the local workspace.
2. **`nexus complete` batches the proposals** — at the end of a feature run, the runner
   prints instructions to open a PR against this repo with the new entries.
3. **Human reviews** — a team member reads the PR, edits or rejects noisy entries, and merges.
4. **Rules propagate** — on the next `nexus bootstrap`, the runner pulls the latest commit
   from this repo and injects the entries relevant to each agent step into that step's handoff.

No rule takes effect until it is merged. Unreviewed auto-learning never reaches the pipeline.

---

## Directory structure

```
anti_patterns/
  roadmap.md           ← patterns for the Roadmap Agent (roadmap_draft)
  risk.md              ← patterns for the Risk Agent (risk_classification)
  architect.md         ← patterns for the Architect (blueprint_draft)
  auditor.md           ← patterns for the Auditor (blueprint_audit, test_audit, code_audit)
  qa-sdet.md           ← patterns for QA-SDET (test_authoring)
  coder.md             ← patterns for the Coder (implementation)
  shared.md            ← cross-role patterns
CHANGELOG.md           ← record of significant rule changes
```

One file per agent role; the runner reads exactly these files and refuses a registry that
still has the old `staff-engineer.md`. An entry may appear in more than one role file when the same
invariant has different role-specific guidance — IDs remain stable across files.

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

**Required fields:** id, title, roles, steps, summary

See `anti_patterns/README.md` for the full field reference and validation instructions.

---

## Adding a rule manually

If you spot a recurring pattern that isn't being caught, you can add it directly:

1. Open the relevant `anti_patterns/<role>.md` (or `shared.md` for a cross-role rule).
2. Add an entry following the format above.
3. Open a PR — title: `feat(antipatterns): add <id>`.
4. Get a single teammate approval and merge.

The rule will be active for the whole team on their next `nexus bootstrap`.

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
