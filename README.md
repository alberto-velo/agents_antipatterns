# agents_antipatterns

Shared anti-pattern knowledge base for the [Nexus Swarm](https://github.com/alberto-velo/agentic-infra) agent pipeline.

The Nexus Swarm runs AI agents (Architect, Staff-Engineer, Auditor, Coder, QA-SDET, Recorder)
to assist with feature development. During each run, the Auditor logs reusable root causes it
encounters. This repo is where those patterns accumulate, get reviewed, and propagate to the
whole team.

---

## How it works

1. **Auditor proposes** — during an audit step, if the Auditor finds a reusable root cause
   it appends a candidate entry to `proposed_anti_patterns.md` in the local workspace.
2. **`nexus complete` batches the proposals** — at the end of a feature run, the runner
   prints instructions to open a PR against this repo with the new entries.
3. **Human reviews** — a team member reads the PR, edits or rejects noisy entries, and merges.
4. **Rules propagate** — on the next `nexus bootstrap`, the runner pulls the latest commit
   from this repo and injects the anti-patterns into every audit step's dispatch context.

No rule takes effect until it is merged. Unreviewed auto-learning never reaches the pipeline.

---

## Directory structure

```
anti_patterns/
  architect.md         ← patterns for the Architect agent
  staff-engineer.md    ← patterns for the Staff-Engineer agent
  coder.md             ← patterns for the Coder agent
  auditor.md           ← patterns for the Auditor agent
  qa-sdet.md           ← patterns for the QA-SDET agent
  recorder.md          ← patterns for the Recorder agent
  shared.md            ← cross-role patterns
  registry.json        ← ID allocation index (next available key, file mapping)
CHANGELOG.md           ← record of significant rule changes
```

One file per agent role. An entry may appear in more than one role file when the same
invariant has different role-specific guidance — IDs remain stable across files.

---

## Entry format

Each entry is a markdown section with an embedded YAML metadata block:

````markdown
## AP-030: Short Descriptive Title

```yaml
id: AP-030
title: Short Descriptive Title
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [code]
unit_domains: [api]
triggers: [short_trigger_name]
summary: One concise sentence — injected into agent handoffs.
```

### Invariant
The reusable invariant at risk — what must always be true.

### Role Guidance
- @Coder: Concrete instruction for this role.
- @Auditor: What to check and how to fail it.
````

**Required metadata fields:** `id`, `title`, `roles`, `steps`, `summary`

See `anti_patterns/README.md` for the full field reference and validation instructions.

---

## Adding a rule manually

If you spot a recurring pattern that isn't being caught, you can add it directly:

1. Open the relevant `anti_patterns/<stack>.yaml` (or create a new file for a new stack).
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
