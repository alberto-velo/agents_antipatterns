# Anti-Pattern Registry

This directory is the canonical anti-pattern registry for the Nexus swarm.
Each file is scoped to the agent that primarily consumes it. The runner parses
these files and injects relevant entries into agent handoffs.

## Files

| File | Consumer |
| :--- | :--- |
| `roadmap.md` | @Roadmap |
| `risk.md` | @Risk |
| `architect.md` | @Architect |
| `coder.md` | @Coder |
| `auditor.md` | @Auditor |
| `shared.md` | Cross-role — all agents |

Entries appear in more than one role file when the same invariant has different
role-specific guidance.

## ID format

IDs use a slug format: `<scope>.<kebab-case-title>`

- `scope` is the primary file the entry belongs to (`shared`, `coder`, `architect`, etc.)
- `kebab-case-title` is a short description of the invariant at risk

Examples: `shared.security-boundary-fail-open`, `coder.convenience-api-erases-probe-semantics`

IDs are unique by construction — no counter, no registry file needed.
Nexus validates the registry before it pushes a proposals PR; a hand-made change is validated
locally (see [Validation](#validation)).

## Adding a new entry

1. Add the entry to the relevant role file(s) using this format:

````markdown
## scope.my-new-pattern: Short Title

```yaml
id: scope.my-new-pattern
title: Short Title
roles: [Role1, Role2]
steps: [step_name, step_name]
risk_levels: [1, 2, 3, 4, 5]
domains: [domain]
unit_domains: [api]
triggers: [trigger_name]
summary: One concise sentence injected into agent handoffs.
```

### Invariant
The reusable invariant at risk.

### Role Guidance
- @Role1: Concrete instruction.
- @Role2: What to check.
````

2. Validate the registry (below), then open a PR. Proposals from a Nexus run don't need this
   step: `nexus complete` adds them to the role files and opens the PR itself.
3. Get a teammate approval and merge.
4. Run `nexus update-governance` on all machines to pull the new rule.

## Validation

Check every entry's metadata and the slug IDs across all files, from this repo's root:

```bash
nexus anti-patterns validate --registry-root anti_patterns
```

Without `--registry-root` it validates the registry Nexus last pulled, not your edits.
