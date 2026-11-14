---
name: task-planner
description: Plans and tracks substantial multi-step work with persistent Markdown TODO files. Use for large implementations, multi-module changes, complex debugging, work with dependencies or validation phases, or tasks likely to span context compaction or sessions. Avoid for trivial one-step requests.
---

# Task Planner v2

## Mission

Keep substantial work organized without turning every request into project management. Use the lightest planning method that reliably protects the user's goal, and use a Markdown TODO file as durable execution state when the task warrants persistence.

The plan is a flexible aid, not an authority over the user's goal. Actual repository state and verification outrank TODO status.

## 1. Choose the planning level

Classify the task before creating files. Use judgment; the signals below are heuristics, not a scoring system.

### Level 1 — Direct execution (no TODO file)

Use for a single obvious change or short action, such as:

- Answering a question or explaining code.
- Fixing a typo or changing one small isolated function.
- Making a narrowly scoped edit with an obvious validation step.

Proceed directly. Do not create a TODO merely to satisfy this Skill. A brief internal plan is enough.

### Level 2 — Lightweight plan

Use when the task has a few related steps but is bounded, low-risk, and unlikely to outlive the current context. Examples include a small feature across a few files or a focused bug investigation.

Use a short checklist in the response or working context. Create a persistent TODO file only if the task has meaningful dependencies, requires several validation stages, the user asks for tracking, or interruption/resumption is plausible.

### Level 3 — Persistent TODO

Create or maintain a TODO file when one or more strong signals apply:

- Multiple phases or dependent subtasks must be coordinated.
- Work spans several modules/subsystems or many files.
- Investigation, implementation, integration, and validation are distinct stages.
- The task is high-risk or omissions would be costly.
- Work is likely to span context compaction, multiple sessions, or long-running execution.
- The user explicitly requests a persistent plan or progress tracking.

Examples: kernel subsystem work, large refactors, multi-component features, complex debugging, migrations, and substantial test/documentation work.

If uncertain between Level 2 and Level 3, prefer Level 2 unless persistence materially reduces risk.

## 2. Choose a safe file location

1. Determine the actual workspace/repository root from the available environment. Do not assume the process working directory is the repository root.
2. For one active substantial task, use `TODO.md` at the workspace root by default. Respect a user-specified path or filename.
3. Before creating anything, check whether the target file exists.
4. Never blindly overwrite, truncate, or delete an existing TODO:
   - Read it and identify its purpose, status, and task scope.
   - If it tracks this same task, update it in place and preserve useful history and completed work.
   - If it tracks a different active task, leave it untouched and create a task-specific file under `.agent/tasks/<short-task-slug>/TODO.md`.
   - If it is completed or stale, preserve it unless the user authorizes cleanup. Use a separate file for new work when needed.
5. For multiple concurrent tasks, prefer one directory per task under `.agent/tasks/<slug>/TODO.md`. Use a concise, stable slug; do not derive it from sensitive information.
6. Do not assume two agents can safely edit the same TODO concurrently. Assign one coordinator to update the shared plan, or give parallel workers separate task notes and merge verified outcomes. Avoid concurrent writes to one file.
7. Do not create or modify files outside the workspace unless explicitly requested.

## 3. Inspect, then plan only as much as needed

Before substantial implementation:

1. Translate the request into a concrete outcome and observable acceptance criteria.
2. Inspect only the relevant project structure, code, docs, tests, and current state needed to make a sound plan.
3. Identify dependencies, constraints, unknowns, risks, and required user decisions.
4. Distinguish verified facts from assumptions. Turn important unknowns into investigation tasks.
5. Ask one focused question only if essential ambiguity prevents safe progress. Otherwise state reasonable assumptions and proceed.

Do not perform broad implementation before a usable plan exists. Minimal reconnaissance is allowed. Do not create speculative tasks for every possible future improvement.

## 4. Create a useful TODO

Use `templates/TODO.template.md` when available, adapting it to the task. Keep the file proportionate to the work.

A persistent plan should normally contain:

- Title and one-sentence objective.
- Overall status and last-updated date when available.
- A short scope/constraints note if useful.
- Ordered, actionable tasks.
- Dependencies only where order actually matters.
- Acceptance criteria for meaningful tasks.
- Blockers, key decisions, and next action when relevant.

Task design rules:

- Start each task with a verb and describe one coherent deliverable.
- Make tasks independently executable and verifiable where practical.
- Prefer a small number of meaningful tasks over command-by-command microtasks.
- Include testing/integration/documentation only when relevant to the requested outcome.
- Separate required work from optional follow-ups.
- Do not invent deadlines, estimates, or progress percentages.

Recommended task states:

- `[ ] TODO` — not started.
- `[>] IN_PROGRESS` — actively being worked on.
- `[x] DONE` — completed and supported by verification/evidence.
- `[!] BLOCKED` — cannot proceed; explain the blocker and next action.
- `[-] CANCELLED` — no longer needed; explain why. Never use DONE for cancelled work.

Keep at most one task `IN_PROGRESS` per execution thread unless work is genuinely parallel and ownership is explicit.

## 5. Review and begin

Before execution, check that:

- The plan covers the user's requested outcome, not unrelated enhancements.
- Dependencies and order make sense.
- Completion criteria are testable or otherwise observable.
- The plan is not more detailed than the task warrants.

For Level 3, write the initial TODO to disk before substantial implementation. Briefly tell the user the path and begin, unless the user asked for a plan only or approval first. Do not ask for approval by default.

For Level 2, do not create a file just to announce a plan. Keep the checklist lightweight unless persistence is useful.

## 6. Execute with efficient updates

For each meaningful task:

1. Choose the next unblocked task whose prerequisites are satisfied.
2. Mark it `IN_PROGRESS` before substantial work on it.
3. Execute the task and perform the appropriate validation.
4. Mark it `DONE` only when its acceptance criteria are met and validation has succeeded, or when the result has been independently inspected where automated tests are unavailable.
5. Record concise evidence when it will help future recovery (for example, test command/result, commit/hash, file path, or manual check).
6. If work fails or cannot proceed, keep it open or mark it `BLOCKED`, and record the actual reason and next action.

### Avoid excessive bookkeeping

- Update after meaningful milestones, not after every command, edit, or tiny substep.
- Batch several related status changes into one file update when safe.
- Do not repeatedly reread the entire TODO if the relevant section is already in context and no other actor can have changed it.
- Before context compaction, session end, handoff, or a long interruption, do a deliberate checkpoint and save the current state.
- Keep progress notes concise; do not copy conversation transcripts, full logs, or reasoning into the TODO.

## 7. Adapt the plan when reality changes

The user's desired outcome outranks the original plan.

When a discovery changes the work:

1. Decide whether the new work is necessary to achieve the requested outcome or protect correctness.
2. Add necessary work before doing substantial extra implementation; place it in the right dependency order.
3. Put optional improvements under `Out of scope / Follow-ups` rather than silently expanding scope.
4. If a task becomes unnecessary, mark it `CANCELLED` with a reason.
5. If the approach changes materially, update the objective/tasks and record a concise decision explaining why.
6. If the user changes the request, revise the plan to match the latest instruction while preserving useful history.

Do not continue mechanically down a plan that has become incorrect. Do not let planning become an excuse to avoid sensible autonomous decisions.

## 8. Recover after compaction or interruption

When resuming substantial work, first identify and read the TODO file associated with the current task. Do not blindly pick the first TODO found.

Then reconcile the plan with reality:

1. Inspect repository status/diff and the files relevant to the current and recently completed tasks.
2. Check whether changes described as complete are actually present.
3. Re-run tests or checks when evidence is absent, stale, or invalidated by later changes. Do not rerun every test automatically if reliable evidence remains applicable.
4. Correct stale statuses. A TODO checkbox is not proof that work exists or works.
5. Identify the next unblocked task and continue.
6. Avoid repeating completed work unless inspection or validation shows it is necessary.

Before handing off or ending a session, ensure the TODO captures:

- Current task and status.
- Files/components changed (briefly).
- Verification performed and result.
- Decisions/discoveries that affect next steps.
- Blockers and the next concrete action.

The TODO should preserve execution state, not every detail of the agent's thought process.

## 9. Completion rules

The overall plan may be marked `COMPLETED` only when:

- All required tasks are `DONE`, or explicitly `CANCELLED` with a valid reason that does not compromise the requested outcome.
- Relevant validation has passed, or unavailable validation is clearly disclosed and the task is not falsely represented as fully verified.
- No unresolved blocker is hidden.
- The TODO accurately reflects the final repository state.

At completion:

1. Add a concise completion summary and meaningful verification evidence.
2. Leave the TODO file in place unless the user asks for cleanup.
3. Report the outcome, tests/checks, remaining limitations, and TODO path.
4. If only part of the requested work is complete, use `PARTIAL` or `BLOCKED`, not `COMPLETED`.

## 10. Minimal template for a persistent task

```markdown
# TODO: <Task title>

> Objective: <Desired outcome>
> Status: PLANNING | IN_PROGRESS | BLOCKED | PARTIAL | COMPLETED
> Updated: <YYYY-MM-DD, if available>

## Tasks
- [ ] TODO — <Actionable deliverable>
  - Acceptance: <Observable completion condition>
  - Depends on: <Task, if applicable>

## Current state
- Current task:
- Next action:
- Changed files/components:

## Blockers
- None

## Decisions / discoveries
- None

## Verification
- [ ] <Check> — Not run

## Progress notes
- <Concise milestone, if useful>

## Completion summary
- Pending.

## Out of scope / Follow-ups
- None
```

## Core principles

- **Scale planning to the task:** no TODO for trivial work; persistent TODO for substantial work.
- **Outcome over checklist:** the plan serves the user's goal and may change.
- **Reality over status:** repository state and verification outrank checkbox labels.
- **Persist only useful state:** save next actions, evidence, blockers, and decisions—not transcripts.
- **Update at milestones:** frequent enough for recovery, not so frequent that bookkeeping dominates.
- **Protect concurrent work:** separate task files or use a single coordinator for shared state.
- **Preserve user data:** inspect existing plans before modifying; never delete them without permission.
