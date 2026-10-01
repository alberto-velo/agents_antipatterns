# Governance Changelog

Record significant changes to anti-pattern rules.

## 2026-10-01 — one agent per step (nexus §72/§74)

- Role layout follows the pipeline: `roadmap.md`, `risk.md`, `architect.md` (the blueprint
  author, formerly `staff-engineer.md`), `auditor.md`, `qa-sdet.md`, `coder.md`, `shared.md`.
  The ADR author's file (the old `architect.md`) was merged into the blueprint author's.
- Retired steps `adr_draft`/`policy_audit` (and role `Architect` in its old meaning) removed
  from every entry. Slug IDs unchanged.
- `recorder.md` removed (the Recorder was retired 2026-07-13).
- `shared.control-plane-marker-in-executable-artifact` removed: the text markers it guarded
  against were retired with the text channel (nexus §68).

## Initial

- Anti-patterns scaffold created via `scripts/migrate_to_governance_repo.py`
