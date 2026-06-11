# Role-Specific Anti-Pattern Registry

This directory is the canonical anti-pattern registry for the Nexus swarm.
Each file is scoped to the agent that consumes it. The Nexus State Runner parses
these files, selects relevant entries for the active handoff, and renders
summaries in `.cursor/workflow/current_handoff.md`.

## Files

| File | Consumer |
| :--- | :--- |
| `architect.md` | @Architect |
| `staff-engineer.md` | @Staff-Engineer |
| `auditor.md` | @Auditor |
| `qa-sdet.md` | @QA-SDET |
| `coder.md` | @Coder |
| `recorder.md` | @Recorder |
| `shared.md` | Cross-role governance |
| `registry.json` | ID allocation and file index |

Entries may appear in more than one role file when the same invariant has
different role-specific guidance. IDs remain stable across files so historical
trace references continue to work.

## ID Allocation

`registry.json` is the source for the next available anti-pattern key. Before
adding a new reusable entry, run:

```powershell
nexus anti-patterns next-id
```

After adding the entry to the affected role file and updating `registry.json`,
run:

```powershell
nexus anti-patterns validate
```

The validator fails if the JSON index drifts from the markdown files or if
`next_id` is not greater than the highest allocated key.

## Entry Metadata Contract

Every entry must use this shape:

````md
## AP-000: Title

```yaml
id: AP-000
title: Title
roles: [Coder, Auditor]
steps: [implementation, code_audit]
risk_levels: [1, 2, 3, 4, 5]
domains: [tests, code]
triggers: [short_trigger_name]
summary: One concise sentence used in generated handoffs.
```

### Invariant
The reusable invariant at risk.

### Role Guidance
- @Role: Concrete instruction.
````

The test suite and `anti-patterns validate` helper validate required metadata,
known roles, known workflow steps, risk levels, duplicate IDs within a file, the
global registry index, and handoff selection behavior.
