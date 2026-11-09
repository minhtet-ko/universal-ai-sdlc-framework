Phase-specific instructions for the **Design** phase. Extends [../AGENTS.md](../AGENTS.md).

## Purpose

This phase defines **how** we're building the system. Focus on the architecture, the project's design concerns (data, interfaces, UX, security, performance, …), and key technical decisions.

## Design Documents Index

The design surface is project-defined: `architecture.md` is mandatory and always present; every other document is selected and created during design work (see `/SDLC-design`), from [`_template.md`](_template.md). This index is the single source of truth for which design documents the project maintains.

| File | Purpose | Status |
|------|---------|--------|
| [`architecture.md`](architecture.md) | System architecture: components, responsibilities, interactions | Stub |
<!-- Add a row for each design document created, in the same operation as the document change. If a listed document turns out not to be needed, remove its row and delete the file — a design change that follows the /SDLC-design procedures. -->

Each document carries a `**Status**:` field — `Stub` (placeholder) | `Draft` (content in progress) | `Approved (YYYY-MM-DD)` — maintained through the `/SDLC-design` procedures together with its index row; modifying an `Approved` document reverts it to `Draft`.

## Document Granularity

Keep each document focused on a single concern and reasonably small — documents are loaded selectively, and small focused documents cost fewer tokens. When a document outgrows its high-level scope, split it: the parent keeps the high-level view and links to sub-documents (e.g., `architecture.md` stays high-level while `architecture-<component>.md` details one component's internals; the same applies to data models, interface designs, and any other concern). Every sub-document gets its own index row and `**Status**:` field.

---

## Decisions Relevant to This Phase

| File | Title | Trigger |
|------|-------|---------|
<!-- Add rows as decisions are recorded. File column: [DEC-kebab-name](../decisions/DEC-kebab-name.md) -->

---

## Linking to Other Phases

- Reference requirements from `1-spec/` to justify design choices
- Design documents guide implementation in `3-code/`
- Infrastructure design informs deployment in `4-deploy/`
