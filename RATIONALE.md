# Project Rationale

## What Is *AI SDLC Framework*

A process framework for **AI-first software development** with any AI coding agent — Claude Code, OpenAI Codex, Gemini CLI, and others. (This is the Universal fork of [pangon/ai-sdlc-framework](https://github.com/pangon/ai-sdlc-framework); its rationale for tool neutrality and team collaboration is in the section "Multi-Agent Teams" below.) It provides a folder structure, file templates, and AI agent instructions that encode a complete software development lifecycle (SDLC) where all project knowledge lives inside the repository itself.

It is not a tool, a library, or an application — there is nothing to install or import. By nature it is a **process framework**: a persistent set of instructions, procedures, and automation that governs how the project is developed for its whole life, with agents operating inside its rules rather than merely starting from its files. By distribution it is template-like: you adopt it by copying it into your repository, then fill in the structure as your real project takes shape.

## Core Principles

1. **AI-first development model.** The framework is designed for a scenario where AI agents do the development work and a user supervises at a high level. The user — a human, or a supervising AI agent in fully automated teams — steers direction, reviews important decisions, and defines specification, assisted by agents that facilitate the process, surface gaps, and flag inconsistencies. The user does not write code. Some structural choices are deliberately optimized for agent consumption over human ergonomics.

2. **Everything-in-repo.** Every artifact that explains the software — goals, requirements, assumptions, constraints, design, decisions, task tracking — lives alongside the source code, version-controlled and always accessible. This gives agents the full context that human developers would normally carry in their heads or scattered across external tools.

3. **Context-window efficiency.** The framework is structured to minimize how many tokens an AI agent must load to do its job. This drives several design choices: hierarchical instruction files, separation of decision history from active records, phase-level indexes with trigger conditions.

4. **Decision capture, not decision suppression.** AI agents are increasingly capable autonomous decision-makers. Rather than preventing them from deciding, the framework ensures decisions are *recorded in the repository* — not lost in reasoning tokens. This enables user review, consistency enforcement, and documentation. The system distinguishes decisions that require the user's approval from those an agent can auto-accept, and this boundary is expected to shift as agent capabilities advance.

## The Core Problem It Solves

In most software projects, the "why" behind the code is scattered across wikis, Confluence pages, Jira tickets, Slack threads, meeting notes, and people's heads. When an AI agent is given such a project, it sees only the code — it has no access to the context that produced it. This leads to:

- Agents making changes that contradict unrecorded architectural decisions
- Inability to trace why a feature exists or why it was built a certain way
- Repeated reasoning about decisions that were already made (wasting tokens and risking inconsistency)
- Important decisions buried in agent conversation logs, invisible to future sessions

## The Solution: Everything-in-Repo, AI-First

The framework makes the full project rationale — from business goals to deployment procedures — available as structured markdown files that agents can navigate efficiently.

The framework's AI guidance is split into three complementary layers:

**Instruction files** (`AGENTS.md`, `AGENTS.<phase>.md`) provide *context*. They describe the structure of the repository and its artifacts, maintain indexes for efficient navigation, and define safeguards and invariants that apply regardless of what task the agent is currently performing. They answer the question "what is here".

**Skills** (`.agents/skills/SDLC-*/`) provide *operational procedures*. Each skill encodes the step-by-step "how" for a specific SDLC activity — eliciting requirements, drafting design documents, decomposing components, planning implementation, executing tasks, reporting status. Skills are loaded on demand when the agent performs that activity, keeping the always-loaded instruction files lean.

**Project artifacts** are the structured markdown files that skills produce and maintain over the course of the project: stakeholders, goals, constraints, assumptions, user stories, requirements, decisions, architecture, data model, API design, tasks, and runbooks. These are the project-specific knowledge that accumulates as work progresses — the actual content that the instruction files index and the skills know how to use and manipulate. Together, they form the repository's memory of every choice made and why.

This three-layer separation matters for context-window efficiency. Instruction files are loaded automatically and must stay small. Skills can be large and detailed because they are loaded only when invoked. Artifacts grow with the project but are accessed selectively through indexes. Cross-cutting rules (like traceability maintenance or status downgrade on modification) live in the always-visible instruction layer, so they are enforced even when the agent is operating outside a skill.

**For human readers**, the same files create a self-documenting project where every decision is traceable from business goal to deployed code. The traceability chain is useful for review even though the primary audience is agents.

## How It Is Organized

The project is divided into four sequential phases, each in its own directory:

| Phase | Purpose |
|---|---|
| **1-spec** | Capture *what* to build and *why*: stakeholders, goals, user stories, requirements, assumptions, constraints |
| **2-design** | Define *how* to build it: the architecture plus a project-defined set of design documents, and formal decision records |
| **3-code** | Build it: starts with component decomposition and implementation planning, then source code, tests, and a task tracker linked to requirements |
| **4-deploy** | Ship and operate it: infrastructure-as-code, deployment scripts, runbooks |

Each phase has:
- A `AGENTS.<phase>.md` file with AI-specific instructions for that phase
- Templates (`_template.md`) for creating new artifacts
- Advisory phase gates defining minimum preconditions before advancing to the next phase

## Key Design Choices

### Context-Window Efficiency

Several structural choices exist specifically to reduce token consumption:

- **Hierarchical instructions** (`AGENTS.md` → `AGENTS.<phase>.md`): agents load only the global file and the relevant phase file, not all four phases at once.
- **Two-file decision records**: the active record (`DEC-kebab-name.md`) contains only what an agent needs during normal work; the history (`DEC-kebab-name.history.md`) is loaded only when evaluating or changing a decision.
- **Phase-level indexes with trigger conditions**: each `AGENTS.<phase>.md` lists which decisions apply and when, so agents can skip irrelevant ones without opening them.

### English as the Working Language

All AI outputs — responses, reasoning, code, comments, documentation, configuration, commit messages — are produced in English regardless of the language used in prompts (the Language Policy in `AGENTS.md`). Three motivations:

1. **Model performance.** LLMs are pretrained predominantly on English and measurably perform best in it. For smaller and earlier-generation models the gap is broad: [self-translating input to English consistently beats direct non-English inference](https://aclanthology.org/2024.naacl-short.46/), and mechanistic analysis shows that the internal "concept space" of English-dominated models [lies closest to English](https://arxiv.org/abs/2402.10588). On frontier models the knowledge gap for high-resource languages has nearly closed, but the reasoning gap persists: [English chain-of-thought outperforms target-language reasoning, with the gap widening on long multi-step tasks and not closing with model scale](https://arxiv.org/abs/2508.14828), and [rewarding language consistency in reasoning traces has a measured accuracy cost](https://arxiv.org/abs/2501.12948). An English-only pipeline avoids fighting the models' dominant language exactly where it matters most: reasoning and code.

2. **Context-window efficiency.** Mainstream tokenizers spend more tokens on non-English text for the same content (e.g., [roughly 25% overhead for Italian with general-purpose tokenizers](https://arxiv.org/abs/2504.17025)). Framework artifacts — indexes, decisions, instructions — are re-read into context on every session, so keeping them in English directly reduces recurring token cost, in line with the context-window efficiency principle.

3. **Uniformity of the produced material.** A single language across artifacts, code, and commit history keeps the repository consistently searchable (English keywords match code identifiers and ecosystem terminology), avoids mid-document language switches — where output quality degrades most — and future-proofs the pipeline for smaller or local models, whose English advantage is far larger than that of frontier models.

Trade-off explicitly accepted: for final conversational responses, frontier models answering in a high-resource language lose little measurable quality — on that surface the policy is primarily about standardization rather than capability.

### Two-File Decision Records

Every significant decision produces two files:
- `DEC-kebab-name.md` — the active record: what was decided and how to enforce it (optimized for AI agent consumption during normal work)
- `DEC-kebab-name.history.md` — the audit trail: what alternatives were considered, who decided, and a changelog (read only when evaluating or changing the decision)

### User Involvement Taxonomy

Each decision records *how* it was made, on a spectrum from `user-decided` to `ai-proposed/auto-accepted`. This serves multiple purposes:
- Lets the supervising user prioritize review effort on agent-originated decisions
- Tracks the evolving boundary between user and agent authority
- Enables future relaxation of review requirements as agent capabilities improve

### Traceability Chain

Artifacts link to each other explicitly:
```
Stakeholders → Goals → User Stories → Requirements → Design → Tasks → Code → Tests
```

Artifacts carry `Source` and/or `Source stakeholder` fields where applicable, linking each item back to its origin in the chain. Indexes in the phase instruction files provide navigable summaries.

The final link of the chain — requirements to tests — gets the same treatment as every other link, adapted to the fact that one end lives in source code. Acceptance criteria carry stable sub-IDs (`AC-kebab-name`) so tests can reference an individual criterion, not just the whole requirement. Each requirement-verifying test declares what it verifies with an in-place `Verifies:` marker (verbatim IDs, greppable), and the reverse map is recorded in **verification indexes** (one per component, plus a global one for manual runbook scenarios and cross-component checks) so agents answer coverage questions without scanning test suites. The marker/index redundancy is the same trade-off the framework already makes for index tables: duplicated state kept honest by `/SDLC-validate`, which checks marker↔index synchronization and reports coverage health. Coverage is deliberately **health, not a gate**: requirement status (`Implemented`) stays mechanically derived from task completion, while coverage gaps are surfaced in reports — completeness of verification is a judgment for the user, not an automated blocker.

### Framework/Project Boundary

[`FRAMEWORK.md`](FRAMEWORK.md) declares which files belong to the framework (skills, templates, procedures, the instructional parts of the `AGENTS.*` files) and which are project content, with modification and upgrade semantics per category. This makes explicit what distinguishes the framework from a one-shot template: its rules and automation remain present — and upgradable from upstream — for the project's whole life, instead of dissolving into the project at instantiation. The manifest, together with the framework version stamped in the same file, is the foundation for a future upgrade procedure.

### Graduated Safeguards

The framework defines three tiers of agent autonomy:

- **Always ask**: conflict resolution, design gaps, decision deprecation/supersession, phase gate advancement
- **Ask first time, then follow precedent**: naming conventions, error handling patterns, test structure
- **Decide and record**: routine implementation choices within established patterns

This is not a philosophical rejection of AI autonomy; it is a pragmatic safeguard for the current capability level. Agents are autonomous within their development tasks, but consequential project-level decisions are captured in the repository and flagged for review when appropriate. As agent reliability improves, these checkpoints can be relaxed.

### Phase Gates

Advisory checklists define minimum preconditions for advancing between phases (e.g., at least one requirement must be Approved before Design begins). Gates are not blocking — the agent warns the user if preconditions are unmet, but proceeds if the user confirms.

### Artifact Status Lifecycle

All artifacts with a Status field follow a defined lifecycle with explicit transition rules:
- **Draft → Approved**: only the user can approve
- **Approved → Implemented**: the agent marks this when all linked tasks reach Done
- **Any → Deprecated**: only the user can deprecate; the agent proposes and waits for approval

## What the Framework Provides Out of the Box

- A layered AI instruction system (`AGENTS.md` → `AGENTS.<phase>.md`) with phase-level indexes and cross-skill artifact procedures
- A declared framework/project boundary (`FRAMEWORK.md`) with per-category ownership and upgrade semantics
- Automation skills across the lifecycle: project initialization, requirements elicitation, design drafting, component decomposition, implementation planning, task execution, bug fixing and ad-hoc changes, status reporting, and artifact consistency validation (deploy work has no dedicated skill — it flows through task execution and fixes, governed by the deploy phase instructions)
- Decision management procedures (creation, deprecation, supersession)
- Complete folder structure for all four phases
- File templates for every artifact type (goals, user stories, requirements, assumptions, constraints, decisions, runbooks)
- Phase gates and artifact status lifecycle
- A task tracker linked to requirements
- A mandatory architecture stub plus a template for the project-defined design documents (selected per project during design work)
- A `.gitignore` with technology-agnostic defaults

## Multi-Agent Teams

_Added by the Universal fork._

The upstream framework targets one agent tool and, implicitly, one agent working at a time. Real teams are rarely that uniform: people have different preferred tools, licenses, and models, and the strongest model for a task changes over time. Binding a process to one vendor makes the process as fragile as that vendor choice.

- **The process is the product, not the tool.** The framework's value is in its procedures, written in plain language. Any capable agent can follow them, so the instructions are kept in tool-neutral files (`AGENTS.md`, `.agents/skills/`) and each tool gets a thin, generated adapter. Generating rather than copying keeps a single source of truth: no tool can drift into a different version of the process.
- **Parallel work needs explicit ownership.** With several agents in flight, "the one `In Progress` task" stops being a reliable signal of interrupted work. Ownership (`<person> (<agent>)`) restores it: each person's agent reasons only about its own work in flight, and leaves teammates' work alone.
- **Publish intent before effort.** The cheapest conflict is the one that never happens. A claim pushed before coding turns a costly duplicate implementation into an immediate, cheap "already taken".
- **Merge by rule, not by luck.** Shared files (indexes, `tasks.md`, Current State) will conflict. Fixed rules (union of rows, recompute derived state, surface contradictions) make those merges deterministic for any agent.
- **Diversity as a safeguard.** Different models make different mistakes. Recording which agent produced each change, and preferring a reviewer who uses a different agent than the author, turns tool diversity from a risk into a quality check.
