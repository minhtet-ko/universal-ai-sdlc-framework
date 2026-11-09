# AI SDLC Framework

A process framework for **AI-first software development** with [Claude Code](https://claude.ai/code) that organizes the entire software development lifecycle (SDLC) into four phases — Specification, Design, Code, Deploy — each with folder structure, artifacts, and agent instructions that encode what to check, when to check it, and what to do, so that all project knowledge lives inside the repository and the user — a human or a supervising AI agent — can supervise at a high level.

It is not a tool, a library, or an application — there is nothing to install or import. It is a **process framework you adopt by copying it into your repository**: from then on its instructions, templates, and skills govern how the project is developed, and you fill in the structure as your real project takes shape.

The framework is built on four core principles:

1. **AI-first development model** — designed for AI agents doing the work while a user (human or supervising AI agent) supervises, defines specification, and steers direction.
2. **Everything-in-repo** — specification, requirements, architecture, decisions, and task tracking all live alongside the source code, versioned and always accessible to agents without external tools.
3. **Context-window efficiency** — hierarchical instructions, two-file decision records, and phase-level indexes minimize how many tokens an agent must load.
4. **Decision capture, not suppression** — agents decide autonomously within established patterns; all decisions are recorded in the repository for user review and consistency.

> For a deeper discussion of the rationale, core principles, and design choices see [`RATIONALE.md`](RATIONALE.md).

## Quick Start

### Option A — Use `degit` (recommended)

```bash
FRAMEWORK_VERSION=$(git ls-remote --tags --sort=-v:refname https://github.com/pangon/ai-sdlc-framework 'v*' | grep -v '\^{}' | head -1 | sed 's#.*refs/tags/##')
npx degit "pangon/ai-sdlc-framework#${FRAMEWORK_VERSION}" my-project
cd my-project
rm -f CHANGELOG.md CONTRIBUTING.md CONTRIBUTORS.md LICENSE NOTICE RATIONALE.md README.md
rm -rf .claude/skills/SDLC-release
git init && git add -A && git commit -m "Adopt AI SDLC Framework"
```

[`degit`](https://github.com/Rich-Harris/degit) copies the repository contents without carrying over git history, giving you a clean starting point. It requires Node.js but does not install any dependency in your project. The `rm` steps remove repo-specific files that are not part of the framework — including the `SDLC-release` skill, which releases the framework itself and must not exist in projects. Removing `LICENSE` and `NOTICE` from your copy is explicitly permitted: the framework is adopted by copying, and projects generated from it may adopt any license.

**Always instantiate from a release tag** (the first line of the snippet resolves the latest one): the framework version travels inside the copy — the `**Version**:` line in the `FRAMEWORK.md` header — and only release commits are guaranteed to carry an accurate stamp. That line is what lets you compare your copy against upstream releases.

### Option B — Manual copy

1. Download the source archive of the latest [release](https://github.com/pangon/ai-sdlc-framework/releases) (or clone and `git checkout v<version>`).
2. Copy all files and directories (including hidden ones like `.claude/`) into your new project folder.
3. Remove the `.git/` directory, if present, to start with a fresh history.
4. Remove repo-specific files: `rm -f CHANGELOG.md CONTRIBUTING.md CONTRIBUTORS.md LICENSE NOTICE RATIONALE.md README.md && rm -rf .claude/skills/SDLC-release`.
5. Initialize a new repository: `git init && git add -A && git commit -m "Adopt AI SDLC Framework"`.

### Then

1. **Run `/SDLC-init`** in Claude Code: the initialization skill sets up the project description in `CLAUDE.md` so agents have the right context from the start.
2. **Work through the phases** (Specification → Design → Code → Deploy) using the built-in [skills](#skills) — type `/` followed by a skill name to automate each step, from requirements gathering to deployment. Alternatively, you can use custom prompts: the `CLAUDE.<phase>.md` hierarchy provides all the context the agent needs to operate correctly in each phase.

> **Manual edits must never be made.** All changes to artifacts and code go through an AI agent whose behavior is governed by the instruction files, existing artifacts, and ideally a skill. This ensures that procedures are followed, decisions are recorded, and the system stays consistent. See the Working Agreement in [`CLAUDE.md`](CLAUDE.md) for the full rules, including the risks of free-prompting and of AI tools that do not follow these instructions.

## Structure

```
├── CLAUDE.md                         # Root AI instructions (start here)
├── FRAMEWORK.md                      # Framework version + framework/project boundary manifest
├── RATIONALE.md                      # Core principles and design rationale
├── CHANGELOG.md                      # Release history (removed at instantiation)
│
├── 1-spec/                           # WHAT and WHY
│   ├── CLAUDE.spec.md                # Phase instructions, decisions index, artifact indexes
│   ├── stakeholders.md               # Stakeholder definitions
│   ├── goals/                        # GOAL-kebab-name.md + _template.md
│   ├── user-stories/                 # US-kebab-name.md + _template.md
│   ├── requirements/                 # REQ-CLASS-kebab-name.md + _template.md
│   ├── assumptions/                  # ASM-kebab-name.md + _template.md
│   └── constraints/                  # CON-kebab-name.md + _template.md
│
├── 2-design/                         # HOW
│   ├── CLAUDE.design.md              # Phase instructions, design documents index, decisions index
│   ├── architecture.md               # System architecture (always present)
│   └── _template.md                  # Template for the project-defined design documents
│
├── 3-code/                           # BUILD
│   ├── CLAUDE.code.md                # Phase instructions and component guidelines
│   ├── tasks.md                      # Development task tracker
│   ├── verification.md               # Global verification index (manual and cross-component requirement→test rows)
│   ├── implementation-log/           # Per-task and per-fix logs (TASK-*.md, FIX-*.md) + templates
│   └── <component>/                  # Per-component directories (created by /SDLC-decompose)
│       ├── CLAUDE.md                 # Component description, decisions index, addressed requirements (auto-loaded when working in the component)
│       └── verification.md           # Component verification index (requirement→test rows)
│
├── 4-deploy/                         # SHIP
│   ├── CLAUDE.deploy.md              # Phase instructions, decisions index, runbooks index
│   ├── infrastructure/               # Infrastructure as code
│   ├── scripts/                      # Deployment and operational scripts
│   └── runbooks/                     # Operational procedures + _template.md
│
├── decisions/                        # DEC-kebab-name.md + DEC-kebab-name.history.md
│
└── .claude/skills/                   # Claude Code skills (automation layer)
    ├── shared/                       # procedures shared by multiple skills
    ├── SDLC-init/SKILL.md            # guided project initialization
    ├── SDLC-elicit/SKILL.md          # requirements elicitation
    ├── SDLC-design/SKILL.md          # design documents
    ├── SDLC-decompose/SKILL.md       # component identification
    ├── SDLC-implementation-plan/SKILL.md  # task generation
    ├── SDLC-execute-task/SKILL.md         # task execution
    ├── SDLC-fix/SKILL.md             # bug fixes and ad-hoc changes
    ├── SDLC-status/SKILL.md          # project dashboard
    ├── SDLC-validate/SKILL.md        # artifact consistency validation
    └── SDLC-release/SKILL.md         # framework releasing (removed at instantiation)
```

## Key Concepts

- **Phase-based development**: each phase has a directory and a `CLAUDE.<phase>.md` file that extends the root `CLAUDE.md`. Phase gates define minimum preconditions before advancing.
- **Traceability**: every artifact references others by descriptive ID (`GOAL-reduce-latency`, `REQ-F-search-by-name`, `DEC-use-postgres`), creating a chain from business need to running code — down to the tests: requirement-verifying tests carry greppable `Verifies:` markers (addressing individual acceptance criteria as `REQ-…/AC-…`), mirrored in verification indexes that map each requirement to its tests. Index tables link directly to artifact files for easy navigation.
- **Two-file decisions**: active record (`DEC-kebab-name.md`) for enforcement, history file (`DEC-kebab-name.history.md`) for audit trail. Indexed per phase with trigger conditions.
- **Context-window efficiency**: hierarchical instructions, phase-level indexes, and the active/history split minimize how many tokens an agent needs to load.
- **Framework/project boundary**: [`FRAMEWORK.md`](FRAMEWORK.md) declares which files the framework owns, which are project content, and what a future upgrade may replace, must merge, or must never touch.

## How the Framework Gets Populated

The framework structure starts empty. Project-specific artifacts are created progressively as you work through each phase — not all at once.

- **At project start** (`/SDLC-init`): set the project description in `CLAUDE.md` so agents have the right context from the start.
- **During the Specification phase** (`/SDLC-elicit`): define stakeholders, goals, constraints, assumptions, user stories, and requirements.
- **During the Design phase** (`/SDLC-design`): select the project's design-document set (the architecture plus the documents the project's nature calls for — data model, API design, CLI design, UX design, …), then draft them. Decisions are recorded as they emerge during design work.
- **At the start of the Code phase** (`/SDLC-decompose`, `/SDLC-implementation-plan`): identify components (creating per-component directories under `3-code/`), then generate the task backlog.
- **During the Code phase** (`/SDLC-execute-task`, `/SDLC-fix`): implement tasks one at a time (each call selects the next pending task by default, or uses any context you provide to pick a specific one, then writes code and tests and updates the task status), or apply bug fixes and ad-hoc changes as they arise.

Claude skills automate each phase of the lifecycle. Type `/skill-name` in Claude Code to invoke them. Each skill reads the root `CLAUDE.md` and the relevant phase instructions before acting.

### Setup

| Skill | Purpose |
|-------|---------|
| `/SDLC-init` | Guided project initialization — sets the project description in `CLAUDE.md` so agents have the right context from the start. |

### Specification Phase

| Skill | Purpose |
|-------|---------|
| `/SDLC-elicit` | Interactive requirements elicitation — guides you through stakeholders, goals, assumptions, constraints, user stories, and requirements in the prescribed order. Creates artifacts from templates, updates indexes. Also performs gap analysis on existing artifacts. |

### Design Phase

| Skill | Purpose |
|-------|---------|
| `/SDLC-design` | Select the project's design-document set, then draft or update the architecture and the other design documents based on approved requirements. Covers design completeness assessment. References requirements by ID for traceability. |
| `/SDLC-decompose` | Identify distinct software components from design artifacts and create per-component directories under `3-code/`. Use when transitioning from Design to Code phase. |

### Code Phase

| Skill | Purpose |
|-------|---------|
| `/SDLC-implementation-plan` | Create a phased implementation plan from design artifacts. Populates `tasks.md` with short tasks grouped into incremental development phases, each ending with a deployable/testable system. |
| `/SDLC-execute-task` | Execute a development task from the implementation plan. Selects the next pending task when invoked bare, or uses user-provided context to identify the task otherwise. Implements with tests, handles design gaps, and updates task status. |
| `/SDLC-fix` | Apply a user-reported fix, bug correction, or ad-hoc change. Gathers context interactively, identifies affected components, checks design artifacts and decisions, implements with tests, and handles design gaps. |

### Cross-Phase

| Skill | Purpose |
|-------|---------|
| `/SDLC-status` | Project-wide dashboard: artifact counts per phase, task progress, phase-gate readiness, traceability health, and assessment freshness. |
| `/SDLC-validate` | Read-only consistency validation: index↔file synchronization, link resolution, status legality and coherence, ID uniqueness, traceability orphans, requirement↔test verification, and Current State accuracy with git-verified assessment freshness. Reports findings with suggested corrections. |

### Typical Workflow

```
/SDLC-init                  ← set up project
/SDLC-elicit                ← define stakeholders, goals, requirements
                            ← the user approves artifacts (Draft → Approved)
/SDLC-design                ← select the design-document set, draft the documents
/SDLC-decompose             ← identify components, create directories
/SDLC-implementation-plan   ← generate task backlog
/SDLC-execute-task          ← execute tasks one by one
/SDLC-fix                   ← apply bug fixes or ad-hoc changes
/SDLC-status                ← anytime: full project overview
/SDLC-validate              ← anytime: artifact consistency validation
```

## Versioning and Releases

The framework follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html), interpreted for a process framework: **MAJOR** versions require migrating instantiated-project content, **MINOR** versions are backward-compatible framework changes, **PATCH** versions are fixes and clarifications. Releases are tagged `vX.Y.Z` and documented in [`CHANGELOG.md`](CHANGELOG.md); entries that require project action carry a **Migration** subsection. Releases are cut with the `/SDLC-release` skill, which exists only in this repository (it is removed at instantiation).

## License

Licensed under the Apache License, Version 2.0. See [`LICENSE`](LICENSE).

Contributions are accepted under the same license (inbound = outbound). See [`CONTRIBUTING.md`](CONTRIBUTING.md).
