# Shared Procedure: Multi-Agent Collaboration

_Included by `SDLC-execute-task`, `SDLC-fix`, `SDLC-implementation-plan`, `SDLC-status`, and `SDLC-validate`, and referenced from the Team Collaboration section of the root `AGENTS.md`. Applies whenever more than one person — each possibly driving a different AI agent (Claude Code, Codex, Gemini CLI, Cursor, Copilot, …) — works on the same repository._

The upstream framework assumes a single agent working at a time. In a team, several agents work **in parallel on different branches** and meet through git. This procedure keeps that safe: every unit of work has a visible owner, claims are published before work starts, and shared files are merged by rules instead of by luck.

## 1. Actor identity

Every agent run acts on behalf of an **actor**, written as `<person> (<agent>)` — for example `alice (codex)`, `bob (claude-code)`, `chen (gemini-cli)`.

- `<person>`: the human's short handle. Derive it from `git config user.name` (lower-cased, first word) unless the team has recorded another convention in a decision; if git has no user configured, ask the user.
- `<agent>`: the lower-case name of the AI tool executing the run (`claude-code`, `codex`, `gemini-cli`, `cursor`, `copilot`, …). An agent that cannot tell which product it runs in asks the user.

Determine the actor once at the start of any run that writes task or log state, and use it consistently.

## 2. Task ownership

The Task Table in `3-code/tasks.md` has an **`Owner`** column:

- `-` — nobody owns the task (always the case for `Todo` tasks that were never claimed).
- `<person> (<agent>)` — the actor who claimed it. Ownership belongs to the **person**: if the same person continues with a different agent, they keep ownership (update the `(<agent>)` part and log a `[HANDOFF]`).

Rules:

1. A task in `In Progress` **must** have an owner.
2. An actor may only execute, resume, block, or complete a task they own. A task owned by **another person** is never touched without that person's agreement — the user must explicitly confirm that a handover was agreed (record it as a `[Q&A]` and a `[HANDOFF]` entry in the task's log, then change the owner).
3. Ownership is kept after `Done`, `Blocked`, `Cancelled`, or `Decomposed` as historical record. Resetting a task to `Todo` resets `Owner` to `-`.
4. When selecting the "next" task automatically, tasks `In Progress` owned by other people are **skipped**, not treated as interrupted work. Only the current person's own `In Progress` tasks count as interrupted.

## 3. Claiming a task (publish before you work)

Two people must never start the same task. Before writing any product code for a task:

1. **Sync**: fetch and update from the team's integration branch (default `main`; a team may record another in a decision) so `tasks.md` is current. Re-check that the task is still unclaimed (`Owner` is `-`) or already yours.
2. **Claim**: set `Status` to `In Progress`, `Owner` to your actor, and `Updated` to today's date.
3. **Publish the claim** — after showing the claim to the user and getting confirmation to push (pushing is an outward-facing action):
   - **Direct claim (default)**: commit only the `tasks.md` change with the message `Claim <TASK-ID> — <actor>` and push it to the integration branch.
   - **Protected integration branch**: create the work branch (see §4), commit the claim there, push it, and open a **draft pull request** titled `<TASK-ID>: <task summary>`. Before claiming, teammates also check open pull requests for the task ID.
4. **Lost race**: if the push is rejected or the sync shows the task now has another owner, **do not force**. Pull, release your local claim, tell the user, and select a different task.

Free-prompted work that changes component code without a task (an `SDLC-fix`) has no Task Table row to claim; announce it instead through the draft pull request of its branch (`FIX-<slug>: <summary>`).

## 4. Branches

- One short-lived branch per unit of work: `task/<TASK-ID>` or `fix/<FIX-ID>` (lower-case is fine; keep the ID recognizable).
- Keep the branch up to date by regularly merging or rebasing the integration branch, and **always** immediately before opening or updating the pull request.
- Merge back through a pull request. A teammate — ideally driving a **different** agent than the author — reviews it; cross-model review catches mistakes a single model repeats.
- Before requesting review, run `SDLC-validate` and `.agents/bin/sync-adapters.sh --check` on the branch; both must be clean or their findings explained in the pull request.

## 5. Merging shared framework files

Some files are touched by almost every run: `3-code/tasks.md`, the phase indexes in `AGENTS.<phase>.md` and component `AGENTS.md` files, `3-code/verification.md`, `1-spec/stakeholders.md`, and the `### Current State` block in the root `AGENTS.md`. Resolve conflicts in them with these rules — never by blindly taking "ours" or "theirs":

1. **Index and table rows are a union.** Keep every row added on either side. Never drop a row someone else added.
2. **The same row changed on both sides**: keep the most advanced lifecycle status (e.g., `Done` over `In Progress` over `Todo`), the most recent `Updated` date, and both sides' `Notes` content. If the two sides contradict each other (different owners, different requirement links, `Cancelled` vs `Done`), **stop and ask the user** — this is an "always ask" conflict resolution.
3. **Current State is recomputed, not merged.** After the textual merge, re-derive every standardized status line (task progress counts, components list, design document statuses, …) from the merged artifacts, following the Current State Protocol. For assessment lines, keep the most recent run; if the other side changed artifacts after that run, apply the staleness rule.
4. **Execution Plan ordering**: keep the integration branch's phase structure and insert the incoming tasks where the incoming branch placed them; if both sides re-planned, ask the user.
5. **Decisions** (`decisions/DEC-*`): two new decisions on the same subject from different branches are a conflict to surface to the user, even when git merges them cleanly.
6. **Implementation logs** are per task or fix and append-only, so they should not conflict; if they do, keep both sides' entries in chronological order.
7. After resolving any conflict in these files, run `SDLC-validate` before finishing the merge.

## 6. Attribution in logs

- Every implementation log (task or fix) records its initial actor in the `**Agent:**` header line.
- Whenever a run on an existing log is performed by a different actor than the last one (another person after an agreed handover, or the same person with a different agent), append a `[HANDOFF]` entry naming the previous and the new actor before any other entry of the run.
- This makes it possible to trace which model produced which change — useful when one agent's output needs extra scrutiny.

## 7. Agent-neutral behavior

The skills are written for any agent. Where a skill says to "ask the user", use whatever interaction mechanism the tool offers (a question in chat is always acceptable). An agent running **non-interactively** (batch or CI mode, no human available) must not guess at an "always ask" point: it stops, leaves the repository consistent, and reports what it needs.
