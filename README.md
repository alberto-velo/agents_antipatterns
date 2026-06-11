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
  python.yaml          ← Python (all frameworks)
  fastapi.yaml         ← FastAPI-specific
  react.yaml           ← React / React Native
  universal.yaml       ← Language-agnostic
CHANGELOG.md           ← Record of significant rule changes
```

One YAML file per tech stack. Add a new file for a new stack; the runner loads all `*.yaml`
files in the `anti_patterns/` directory automatically.

---

## Anti-pattern YAML format

Each file is a list of entries:

```yaml
- id: python.no-bare-except
  description: >
    Bare `except:` catches SystemExit and KeyboardInterrupt, masking crashes
    and making the process unresponsive to signals.
  fix: Use `except Exception:` or a specific exception type.
  unit_domains: [api, agent-infra]   # which nexus domains this applies to
  severity: fail                     # fail | warn
  added_by: Auditor                  # who proposed it
  source_feature: auth-middleware    # which feature run surfaced it

- id: python.mutable-default-arg
  description: >
    Using a mutable object (list, dict) as a default argument is evaluated
    once at function definition — mutations persist across calls.
  fix: Use `None` as default and assign inside the function body.
  unit_domains: [api, agent-infra]
  severity: fail
  added_by: Auditor
  source_feature: user-service-refactor
```

**Required fields:** `id`, `description`, `severity`
**Optional fields:** `fix`, `unit_domains`, `added_by`, `source_feature`

`id` must be unique across all files. Convention: `<stack>.<short-name>`.

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

Set `NEXUS_GOVERNANCE_REMOTE` once and add it to your shell profile:

**bash / zsh** (add to `~/.bashrc` or `~/.zshrc`):
```bash
export NEXUS_GOVERNANCE_REMOTE=https://github.com/alberto-velo/agents_antipatterns.git
```

**PowerShell** (add to `$PROFILE`):
```powershell
$env:NEXUS_GOVERNANCE_REMOTE = "https://github.com/alberto-velo/agents_antipatterns.git"
```

Then do an initial pull:
```bash
nexus update-governance
```

Run `nexus update-governance` again any time you want to pick up new rules before starting
a feature.
