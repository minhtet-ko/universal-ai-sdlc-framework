# Changelog

All notable changes to the **Universal AI SDLC Framework** — a multi-agent fork of [pangon/ai-sdlc-framework](https://github.com/pangon/ai-sdlc-framework) — are documented in this file. Entries up to and including `[1.0.0]` are upstream history (written with upstream paths such as `CLAUDE.md`). The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html) with the semantics defined in the `SDLC-release` skill (MAJOR = requires migration of instantiated-project content, MINOR = backward-compatible framework change, PATCH = fix or clarification with no behavioral change).

When a release requires action in instantiated projects, its entry carries a **Migration** subsection describing what to do.

This file is Upstream-only (see `FRAMEWORK.md`): it exists in the framework repository and is removed at instantiation.

## [Unreleased]

Forked from `pangon/ai-sdlc-framework@1.0.0` and made usable with any AI coding agent.

### Changed

- Instruction files renamed to tool-neutral names: `CLAUDE.md` → `AGENTS.md`, `<phase>/CLAUDE.<phase>.md` → `<phase>/AGENTS.<phase>.md`, component `CLAUDE.md` → `AGENTS.md`.
- Skills moved from `.claude/skills/` to `.agents/skills/` (Agent Skills standard location, read natively by Codex and Gemini CLI).
- Memory Policy now forbids any agent's private memory, not only Claude's.
- `SDLC-decompose` creates component `AGENTS.md` files and generates their Claude Code shims.
- `SDLC-execute-task` only treats the current person's own `In Progress` tasks as interrupted; it claims tasks with an owner and publishes the claim before working.
- `SDLC-validate` allows several `In Progress` tasks with different owners and checks adapter sync.
- `SDLC-status` reports work in flight per owner.

### Added

- `.agents/bin/sync-adapters.sh`: generates `CLAUDE.md` shims, `.claude/skills/` wrappers, `.gemini/commands/` slash commands and `.gemini/settings.json`; `--check` mode for CI.
- `.agents/skills/shared/multi-agent-collaboration.md`: actor identity, task ownership, claim protocol, branch-per-task, shared-file merge rules, attribution, non-interactive behavior.
- `Owner` column in `3-code/tasks.md`; `**Agent:**` header and `[HANDOFF]` entry in implementation logs.
- Multi-Agent Interoperability and Team Collaboration sections in `AGENTS.md`.
- CI workflow (`.github/workflows/agent-adapters.yml`) checking adapter sync.

### Migration

- Projects on upstream `pangon/ai-sdlc-framework@1.0.0`: rename files per the Upstream Mapping table in `FRAMEWORK.md`, add an `Owner` column (value `-`, or the owner of `In Progress` tasks) to every Task Table, then run `.agents/bin/sync-adapters.sh`.

## [1.0.0] - 2026-08-23

First versioned release. The framework as released here comprises:

### Added

- Four-phase repository structure (`1-spec/`, `2-design/`, `3-code/`, `4-deploy/`) with per-phase `CLAUDE.<phase>.md` instruction files extending the root `CLAUDE.md`, phase gates, and the Current State Protocol.
- Specification artifact system (stakeholders, goals, user stories, requirements with acceptance criteria, assumptions, constraints) with descriptive-ID traceability and phase-level indexes.
- Two-file decision records (`DEC-*.md` active record + `DEC-*.history.md` trail) with per-phase decision indexes and recording/deprecation/supersession procedures.
- Requirement-to-test traceability: `AC-*` acceptance-criterion IDs, greppable `Verifies:` markers, and per-component plus global verification indexes.
- Automation skills: `SDLC-init`, `SDLC-elicit`, `SDLC-design`, `SDLC-decompose`, `SDLC-implementation-plan`, `SDLC-execute-task`, `SDLC-fix`, `SDLC-status`, `SDLC-validate`, plus shared procedures in `.claude/skills/shared/`.
- Framework/project boundary manifest (`FRAMEWORK.md`) with ownership categories and per-category upgrade semantics.
- Release versioning: semver tags, this changelog, the version stamped in the `FRAMEWORK.md` header, and the `SDLC-release` skill (Upstream-only) that maintains them.
