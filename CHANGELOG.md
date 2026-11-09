# Changelog

All notable changes to the **AI SDLC Framework** are documented in this file. The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/); versions follow [Semantic Versioning](https://semver.org/spec/v2.0.0.html) with the semantics defined in the `SDLC-release` skill (MAJOR = requires migration of instantiated-project content, MINOR = backward-compatible framework change, PATCH = fix or clarification with no behavioral change).

When a release requires action in instantiated projects, its entry carries a **Migration** subsection describing what to do.

This file is Upstream-only (see `FRAMEWORK.md`): it exists in the framework repository and is removed at instantiation.

## [Unreleased]

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
