---
name: SDLC-release
description: Cut a release of the AI SDLC Framework itself — version bump, changelog finalization, release commit, and tagging instructions. Operates ONLY on the framework repository, never on instantiated projects (the skill is Upstream-only and removed at instantiation). Use when framework changes are ready to be published as a new version.
---

## Instructions

You are releasing a new version of the AI SDLC Framework from its own repository. A release consists of: a finalized changelog entry, the version stamp updated in `FRAMEWORK.md`, a release commit, and a `v<version>` tag on that commit.

### Scope guard

This skill applies only to the **framework repository**. Before doing anything, verify:

- `CHANGELOG.md` and `RATIONALE.md` exist, and `README.md` is titled "AI SDLC Framework".
- `**Phase**:` in the Current State section of `AGENTS.md` is `Not initialized`.

If any check fails, you are most likely inside an **instantiated project** where the Quick Start `rm` step was skipped: stop, explain this, and suggest removing `.agents/skills/SDLC-release/` (and any other leftover Upstream-only files) instead of releasing.

### Versioning semantics

The framework uses [Semantic Versioning](https://semver.org/spec/v2.0.0.html), interpreted for a process framework:

- **MAJOR** — changes that require migration of existing instantiated-project content, or that break compatibility of the procedures: artifact or index format changes, Current State Protocol changes, ID-scheme changes, manifest recategorizations that move ownership of existing files, removal or renaming of skills.
- **MINOR** — backward-compatible framework changes: new skills, new templates, new or expanded procedures and instructions that existing project content satisfies as-is.
- **PATCH** — fixes and clarifications with no behavioral change to project content: wording, typos, corrections of procedure bugs that do not alter any format.

Versions only increase; a published tag is never moved or reused.

### Changelog conventions

`CHANGELOG.md` follows Keep a Changelog: an `## [Unreleased]` section accumulates entries as framework changes land (every framework change should add its entry there in the same operation), and releasing converts it into a dated version section. When a release requires action in instantiated projects (any MAJOR, occasionally a MINOR), its entry must include a `### Migration` subsection stating what a project on the previous version must do.

### Workflow

1. **Preconditions**: run the scope guard. Verify the working tree is clean (`git status`) — everything the release ships must already be committed. Read the current version from the `**Version**:` line in the `FRAMEWORK.md` header.
2. **Review what is being released**: list the changes since the last release — `git log v<current>..HEAD --oneline` if the tag is available locally, otherwise the commits since the previous release commit. Cross-check against the `## [Unreleased]` section of `CHANGELOG.md`; draft entries for anything missing and reconcile with the user.
3. **Propose the bump**: classify the changes per the semantics above, propose MAJOR/MINOR/PATCH with reasoning, and ask the user to confirm the new version number.
4. **Finalize the changelog**: turn `## [Unreleased]` into `## [X.Y.Z] - YYYY-MM-DD` (leaving a fresh empty `## [Unreleased]` above it), completing entries and the `### Migration` subsection where required.
5. **Update the stamp**: set the `**Version**:` line in the `FRAMEWORK.md` header to the new version.
6. **Release commit**: commit the changelog and stamp changes together with the message `Release vX.Y.Z`.
7. **Instruct the user to publish** — do not push or tag yourself (repository rights may not be available in this environment). Tell the user to:
   1. Push the release commit to the default branch.
   2. Create the tag `vX.Y.Z` on that exact commit — e.g., on GitHub: *Releases → Draft a new release → Choose a tag → type `vX.Y.Z` → Target: the release commit*, using the changelog entry as the release notes.
8. **Verify** (when possible): after the user confirms, check the tag exists and points to the release commit (`git ls-remote --tags <remote-url>`), and report the result.

### Rules

- The stamp in `FRAMEWORK.md` is guaranteed accurate only at release commits; never bump it outside this workflow.
- Never rewrite a released changelog section except to fix typos; corrections to a release's content are a new release.
- All output in English (Language Policy in `AGENTS.md`).
