# Anti-Pattern Registry

This directory is the canonical anti-pattern registry for the Nexus swarm. Each rule is in
exactly one file, and the file decides who reads it:

| File | Mistakes of | Read at |
| :--- | :--- | :--- |
| `architect.md` | the blueprint | `blueprint_draft`, `blueprint_audit` |
| `coder.md` | the unit's tests and code | `implementation`, `code_audit` |
| `shared.md` | either author, on their own | all four steps above |
| `auditor.md` | an audit itself | `blueprint_audit`, `code_audit` |

A step receives every rule in its files whose `domains` hold the unit's domain and whose
`from_level` is at or below the unit's risk level.

The entry format is in the [repository README](../README.md#entry-format).

## Validation

Check every entry and that no id repeats, from this repo's root:

```bash
nexus anti-patterns validate --registry-root anti_patterns
```

Without `--registry-root` it validates the registry Nexus last pulled, not your edits.
