# Verification Index (global)

Maps requirements and acceptance criteria to verification not owned by a single component: **manual runbook scenarios** and **cross-component checks**. Automated tests that live in a component are recorded in that component's own `verification.md` instead. Structure and maintenance rules: see Testing Conventions in [`CLAUDE.code.md`](CLAUDE.code.md).

| Requirement | AC | Test | Kind | Added |
|-------------|----|------|------|-------|

<!-- One row per (requirement, acceptance criterion, test) triple, grouped by requirement.
     AC: AC-kebab-name, or "-" for requirement-level verification.
     Test: for manual rows, a link to the runbook plus the scenario name, e.g. [phase-1-smoke](../4-deploy/runbooks/phase-1-smoke.md) — "Search with empty query"; for automated cross-component suites, <repo-relative-path>::<test-name>.
     Kind: unit | integration | e2e | manual. -->
