# Framework Boundary

**Version**: pangon/ai-sdlc-framework@1.0.0

This file declares which files belong to the **AI SDLC Framework** and which are project content. It is the authority for deciding whether a file may be modified as part of project work, and the basis for the future framework-upgrade procedure — which files an upgrade may replace, must merge, or must never touch.

The boundary governs **instantiated projects**. In the framework repository itself every file is under framework development, and this boundary imposes no constraint there.

## Ownership Categories

| Category | Meaning | On framework upgrade |
|----------|---------|----------------------|
| **Framework** | Owned by the framework, identical across projects. Project work must not modify these files (see Rules). | Replaced wholesale |
| **Mixed** | The framework owns the file's structure and instructional text; the project owns the designated content regions and any project-added sections. | Merged: framework-shipped text updated, project content preserved |
| **Seeded** | The framework provides the initial content; the project takes full ownership at adoption. | Never touched |
| **Project** | Created and owned by project work. **Default: any path not listed in the manifest is project content.** | Never touched |
| **Upstream-only** | Exists only in the framework repository; removed at instantiation (the Quick Start `rm` step). A project file reusing the same name is project content. | Not applicable |

## Manifest

| Path | Category | Project content (for Mixed) / notes |
|------|----------|-------------------------------------|
| `FRAMEWORK.md` | Framework | This file |
| `.claude/skills/SDLC-*/SKILL.md` | Framework | The automation skills — except `SDLC-release`, which is Upstream-only (see its row). The `SDLC-` name prefix is reserved for framework skills; project-created skills (see the Working Agreement) use other names and are project content |
| `.claude/skills/SDLC-release/SKILL.md` | Upstream-only | Framework release procedure (version bump, changelog, tagging) — operates only on the framework repository; removed at instantiation |
| `.claude/skills/shared/*.md` | Framework | Procedures shared by multiple skills |
| `1-spec/goals/_template.md`, `1-spec/user-stories/_template.md`, `1-spec/requirements/_template.md`, `1-spec/assumptions/_template.md`, `1-spec/constraints/_template.md` | Framework | Specification artifact templates |
| `2-design/_template.md` | Framework | Design document template |
| `3-code/implementation-log/_template.md`, `3-code/implementation-log/_fix_template.md` | Framework | Implementation log templates |
| `4-deploy/runbooks/_template.md` | Framework | Runbook template |
| `decisions/_template.md`, `decisions/_template.history.md` | Framework | Decision templates |
| `decisions/PROCEDURES.md` | Framework | Decision recording/deprecation/supersession procedures |
| `4-deploy/scripts/README.md` | Framework | Directory rules for deployment scripts |
| `CLAUDE.md` | Mixed | `## Project Overview` (including `### Current State`) |
| `1-spec/CLAUDE.spec.md` | Mixed | Rows of the decisions index and of the five artifact indexes |
| `2-design/CLAUDE.design.md` | Mixed | Rows of the Design Documents Index and of the decisions index |
| `3-code/CLAUDE.code.md` | Mixed | `## Components` entries |
| `4-deploy/CLAUDE.deploy.md` | Mixed | Rows of the decisions index and of the Runbooks Index |
| `1-spec/stakeholders.md` | Mixed | Stakeholder Table rows |
| `3-code/tasks.md` | Mixed | Task rows, phase groupings, and the Execution Plan content |
| `3-code/verification.md` | Mixed | Rows of the global verification index (per-component `verification.md` files are Project content) |
| `2-design/architecture.md` | Seeded | Stub, drafted into the project's architecture |
| `4-deploy/infrastructure/README.md` | Seeded | Accumulates the project's resource-dependency documentation |
| `.gitignore` | Seeded | Technology-agnostic defaults, extended per project |
| `README.md`, `RATIONALE.md`, `CHANGELOG.md`, `CONTRIBUTING.md`, `CONTRIBUTORS.md`, `LICENSE`, `NOTICE` | Upstream-only | Removed at instantiation |

Everything else — specification artifacts, design documents, decision records (`DEC-*`), component directories, implementation logs, runbooks, infrastructure and script files, and any file the project adds — is **Project** content.

## Rules

1. **Project work stays out of framework files**: never modify Framework files or the framework-owned parts of Mixed files during project work. If a task seems to require it — including fixing an apparent framework bug — surface it to the user instead; framework corrections belong upstream.
2. **Local customization is a fork**: deliberately diverging from the framework (editing a skill, a template, or a procedure) is an "always ask" action (see Graduated Safeguards in `CLAUDE.md`) and must be recorded as a decision, because it changes framework behavior for every future task and complicates upgrades.
3. **Manifest maintenance is upstream work**: this manifest is framework-owned; the framework repository updates it in the same operation that adds, moves, or removes framework files. Projects never edit it.
