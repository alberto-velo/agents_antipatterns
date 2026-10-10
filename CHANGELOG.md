# Governance Changelog

Record significant changes to anti-pattern rules.

## 2026-10-10 — `coder.test-file-static-analysis-gate` fits tests written before their code (nexus §92)

- The Coder guidance asked to fix every static-analysis finding in the test files before the freeze. Before the freeze the code entries are not written yet, so a test that uses them cannot compile cleanly, and in AS-109 the Coder read every error as expected and froze four compile errors. The guidance now separates the expected errors (a symbol a `kind: code` entry declares and has not written yet) from the defects, and asks for a command that reports every error. It matches the Coder prompt of nexus §92. The invariant says the same in both role files; the Auditor guidance is unchanged.

## 2026-10-05 — proposals arrive as PRs from `nexus complete` (nexus §87)

- No rule change. Proposals are now registry entries checked against this repo's format when
  the Auditor makes them, and `nexus complete` opens the PR (branch
  `nexus/proposals/<feature>`, one copy per listed role) instead of printing a manual recipe.
- READMEs updated: the new flow, the current role files (no `qa-sdet.md`), the full list of
  required fields, and local validation — this repo has no CI check.

## 2026-10-02 — the Coder writes the tests (nexus §80)

- `qa-sdet.md` removed: the QA-SDET role and its `test_authoring`/`test_audit` steps are retired.
  The Coder writes a unit's tests first, freezes them, then writes the code; `code_audit` judges
  both. Every QA-SDET entry moved into `coder.md` — an entry `coder.md` already had keeps its text
  and gains the test-side guidance as `@Coder (writing the tests)` lines.
- Every entry: role `QA-SDET` → `Coder`; steps `test_authoring` → `implementation`, `test_audit` →
  `code_audit`. Slugs `qa-sdet.*` → `coder.*` (they belong to `coder.md` now).
- A §80 Nexus release refuses the previous registry (it still has `qa-sdet.md`); an earlier
  release still reads this one, but its QA-SDET steps then get no role entries. Merge this with
  the runner release.

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
