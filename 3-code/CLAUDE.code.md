Phase-specific instructions for the **Code** phase. Extends [../CLAUDE.md](../CLAUDE.md).

## Purpose

This phase contains the **implementation**. Focus on clean, tested, maintainable code.

---

## Components

<!-- Add an entry for each component/codebase -->

---

## Component Isolation

All source code, configuration, and assets for a component **must reside within that component's directory**. Specifically:

- **No code outside component directories** — never place source files, configuration files, or build artifacts in `3-code/` itself or anywhere else outside the owning component's directory.
- **No cross-component configuration** — configuration that spans multiple components should never be necessary. If such a situation arises, treat it as a potential design flaw or incorrect component separation. Stop work, notify the user with a clear description of the conflict, and propose alternative actions (e.g., refactoring responsibilities, introducing a new component, or adjusting the design).
- **Do not rename or move component directories** — the directory names listed above are fixed; renaming or relocating them breaks cross-phase references and tooling assumptions.

---

## Build Commands

Scripts and commands for each component are documented in that component's own codebase (package.json, Makefile, README, or equivalent). Check there first.

When invoking any command, apply active decisions from the component's `CLAUDE.md` whose trigger conditions match.

---

## Task Tracking

All development tasks are tracked in [`tasks.md`](tasks.md).

To create the initial implementation plan (phased tasks from design artifacts), run `/SDLC-implementation-plan`. This should be done after `/SDLC-decompose` and before starting any coding work.

---

## Implementation Logs

Each task execution and each fix execution produces a durable log under [`implementation-log/`](implementation-log/). There is one log file per task or fix:

- **Task logs** — file name `<TASK-ID>.md` (e.g., `TASK-model-service-list.md`). Created and maintained by the `SDLC-execute-task` skill.
- **Fix logs** — file name `<FIX-ID>.md` (e.g., `FIX-asset-list-pagination-off-by-one.md`). Created and maintained by the `SDLC-fix` skill.

Each log captures the synthesized understanding of the task or fix followed by a chronological append-only record of non-trivial operations and evaluations performed during execution (writes, questions to the user, reconsiderations, problems, in-flight corrections, design gaps, tensions, test outcomes).

---

## Testing Conventions

### Requirement references in tests (`Verifies:` marker)

Every test that verifies a requirement carries a **verification marker**: a line in the test's docstring, or in a comment immediately preceding or inside the test definition, with the exact form

```
Verifies: REQ-CLASS-kebab-name/AC-kebab-name, REQ-CLASS-kebab-name
```

- The keyword `Verifies:` is followed by one or more comma-separated references; multiple `Verifies:` lines per test are allowed.
- A reference is a **verbatim** requirement ID, optionally followed by `/AC-kebab-name` to target one acceptance criterion of that requirement. Prefer AC-level references; use a bare requirement ID only for holistic tests that exercise the requirement as a whole.
- IDs appear verbatim (kebab-case, never mangled into the language's identifier style) so the trace stays greppable.
- Not every test needs a marker — tests of internal behavior with no requirement trace carry none. Coverage is checked from the requirement side, not the test side.

### Verification indexes

The reverse map (requirement → tests) is recorded in **verification indexes**, so agents can answer coverage questions without scanning the test suites:

- **Per component**: `<component-name>/verification.md` — rows for the automated tests that live in that component (regardless of what they verify).
- **Global**: [`verification.md`](verification.md) — rows for verification not owned by a single component: manual runbook scenarios (`4-deploy/runbooks/`) and cross-component checks.

One row per (requirement, acceptance criterion, test) triple, grouped by requirement:

| Column | Content |
|--------|---------|
| Requirement | Relative link to the requirement file |
| AC | The acceptance criterion ID (`AC-kebab-name`), or `-` for requirement-level verification |
| Test | Automated: `<path>::<test-name>`, path relative to the component root (per-component index) or repo root (global index). Manual: link to the runbook plus the scenario name |
| Kind | `unit` \| `integration` \| `e2e` \| `manual` |
| Added | Date the row was recorded |

Markers and index rows are two views of the same links and are maintained **together, in the same operation as the test change** — adding, renaming, moving, or deleting a requirement-verifying test updates the owning index in the same operation (procedures: `/SDLC-execute-task`, `/SDLC-fix`). `/SDLC-validate` checks marker ↔ index synchronization and reports coverage health. Coverage is **health, not a gate**: requirement status (`Implemented`) remains driven by task completion, and coverage gaps are reported, never blocking.

---

## Linking to Other Phases

- Implementation follows designs in `2-design/`
- Tests verify requirements from `1-spec/` — each requirement-verifying test carries a `Verifies:` marker and a row in a verification index (see Testing Conventions)
- Infrastructure code goes in `4-deploy/`; when a coding task modifies IaC, the deploy phase instructions ([`CLAUDE.deploy.md`](../4-deploy/CLAUDE.deploy.md)) apply as well
