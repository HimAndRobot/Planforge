---
name: execute-plan
description: Use when the user wants to implement a PlanForge plan in either subagent mode or inline mode, with optional branch creation and mandatory final review.
---

# Execute Plan

Use this skill to implement an existing PlanForge plan.

This skill keeps the reliable mechanics of Superpowers subagent-driven development while applying PlanForge's simplified execution policy:

- branch is optional and asked once at the start;
- no automatic commits, pushes, or PRs;
- subagent mode and inline mode are both supported;
- per-task formal review agents are not used by default;
- final review is always a separate review agent;
- fresh verification is required before completion claims.

Read these shared references as needed:

- `skills/_shared/plan-format.md`
- `skills/_shared/subagent-contract.md`
- `skills/_shared/final-review.md`

## Initial Flow

1. Read the full plan once.
2. Extract all tasks with their full text and shared context.
3. Create a visible task checklist for coordination.
4. Ask once: should I create a branch for this task?
5. If yes, propose and create a short descriptive branch name without any required prefix.
6. If no, continue on the current branch.
7. Ask execution mode:
   - Subagent mode
   - Inline mode

Do not ask "should I continue?" between tasks. Continue until all tasks are complete, genuinely blocked, or the user interrupts.

## Git Policy

- Never commit automatically.
- Never push automatically.
- Never create a PR automatically.
- Never ask to commit at the end.
- Only create a branch if the user approves at execution start.
- Do not use the `codex/` branch prefix unless the user explicitly asks for it.
- Propose the branch name yourself from the plan title or task, using a plain descriptive name such as `planforge-review-flow` or `fix-auth-timeout`; do not make the user name it unless they ask to.
- If the repository is on `main` or `master`, do not start implementation there without explicit user approval.

## Subagent Mode

Use the Superpowers dispatch pattern:

- Fresh implementer subagent per task.
- Implementers receive curated context, not the orchestrator's session history.
- The orchestrator provides the full task text, relevant design decisions, boundaries, likely files, and verification expectations.
- The implementer should not have to read the whole plan independently.
- Do not dispatch multiple implementation subagents in parallel when their edits could conflict.

Each implementer must report:

- `DONE`
- `DONE_WITH_CONCERNS`
- `NEEDS_CONTEXT`
- `BLOCKED`

## Per-Task Subagent Flow

1. Dispatch one implementer subagent with full task text and context.
2. If the implementer asks a question, answer clearly and provide more context before work continues.
3. If status is `DONE`, inspect changed files and verification evidence.
4. If status is `DONE_WITH_CONCERNS`, read concerns before proceeding.
5. If status is `NEEDS_CONTEXT`, provide context and redispatch.
6. If status is `BLOCKED`, decide whether to add context, split the task, use a stronger model, or ask the user.
7. Review locally as orchestrator for plan compliance, scope, verification, and obvious quality issues.
8. Fix small issues directly or send a short corrective follow-up to the implementer.
9. Update checklist status and continue to the next task.

PlanForge intentionally does not call separate spec-reviewer and code-quality-reviewer agents after every task by default. The orchestrator owns that lightweight review loop.

## Inline Mode

The current agent executes the plan directly.

Per task:

1. Execute the task step by step.
2. Review locally against purpose, boundaries, expected result, and verification.
3. Fix issues before moving on.
4. Update checklist status.

Inline mode still requires final separate review.

## Model And Task Judgment

Use the least expensive capable path:

- simple isolated tasks can use fast implementer subagents;
- multi-file integration or debugging tasks need stronger reasoning;
- final review should use a strong reviewer when available.

If a task is too large or tightly coupled, split it before dispatching.

## Red Flags

Never:

- ignore subagent questions;
- force the same retry after `BLOCKED` without changing context or approach;
- let the implementer self-review replace orchestrator review;
- skip final separate review;
- skip fresh verification;
- accept "close enough" when the plan requirement is unmet;
- commit, push, or create PRs automatically.

## Final Review

Final review is mandatory in both modes.

Use a separate review agent focused only on review. Provide:

- plan text;
- implementation summary;
- changed file list;
- relevant diff;
- verification commands and results;
- known concerns.

The reviewer checks plan compliance, missing requirements, regressions, scope drift, verification gaps, code quality risks, and Git policy compliance.

Fix valid Critical and Important findings, decide whether Minor findings belong in scope, then rerun fresh verification before claiming completion.

## Verification Before Completion

Before saying the work is complete:

1. Identify commands or checks that prove completion.
2. Run them fresh.
3. Read the output and exit codes.
4. Compare the result to the plan requirements.
5. Report passed checks, failed checks, checks that could not run, and remaining risk.

## Completion

Final response must state:

- what changed;
- what verification ran;
- what passed;
- what failed or could not run;
- remaining risk.
