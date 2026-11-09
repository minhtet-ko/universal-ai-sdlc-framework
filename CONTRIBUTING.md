# Contributing

Thanks for your interest in contributing to **AI SDLC Framework**!

## License (inbound = outbound)
This project is licensed under the Apache License 2.0 (see `LICENSE`).

By submitting a contribution (code, documentation, prompts, configurations, or any other material)
to this repository, you agree that your contribution is provided under the **same license**
as the project (**Apache-2.0**).

## Contribution requirements
- Submit only work that you have the right to contribute.
- Do not include third-party code/assets unless you have permission and you include any required
  notices and license information.
- If you add third-party components, update `THIRD_PARTY_NOTICES` (if present) and/or reference
  the relevant license files in your PR description.

## Practical guidelines
- Keep changes focused and scoped.
- Prefer small PRs with clear descriptions.
- Add/update documentation when behavior or workflow changes.
- Add an entry to the `## [Unreleased]` section of `CHANGELOG.md` for any notable change.

## Releasing (maintainers)
Releases follow Semantic Versioning and are cut with the `/SDLC-release` skill, which finalizes
the changelog entry, updates the version in the `FRAMEWORK.md` header, creates the
release commit, and provides the tagging instructions (`vX.Y.Z`). See
`.agents/skills/SDLC-release/SKILL.md` for the version-bump semantics and the full procedure.
