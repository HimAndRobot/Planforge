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

Use the Superpowers `subagent-driven-development` pattern, with only these PlanForge changes:

- remove the per-task spec-reviewer subagent loop;
- remove the per-task code-quality-reviewer subagent loop;
- let the orchestrator review and correct after each implementer task;
- add one separate final reviewer for the whole implementation.

Keep the rest of the Superpowers mechanics:

- Fresh implementer subagent per task, executed sequentially.
- Implementers receive curated context, not the orchestrator's session history.
- The orchestrator provides the full task text, relevant design decisions, boundaries, likely files, and verification expectations.
- Any codebase exploration needed to frame a task must happen before dispatching that task, as part of context curation, or after the implementer reports back as part of review before the next task.
- The implementer should not have to read the whole plan independently.
- Implementers may ask questions before or during work; answer before letting them proceed.
- Implementers implement, test, verify, self-review, and report back.
- Do not dispatch multiple implementation subagents in parallel.

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
7. Review locally as orchestrator for plan compliance, scope, verification, and obvious quality issues. This replaces the original Superpowers per-task spec-reviewer and code-quality-reviewer loops.
8. Correct valid per-task issues. Small corrections may be edited directly by the orchestrator; larger missing implementation should be sent back as a focused corrective task.
9. Update checklist status and continue to the next task.

The orchestrator must not invent a new "central integration" feature slice after subagents finish. If substantial integration work is required, it must already be an explicit plan task or become a focused corrective task. Integration after subagents means reviewing, reconciling, fixing valid issues, updating checklists, and verifying the planned work.

While an implementer is working, the orchestrator stays in the Superpowers controller role: it waits for the implementer report, answers implementer questions, and keeps coordination state. It should tell the user it is waiting for the implementer rather than claiming it will work "around" the task. Do not read files, trace integration paths, or inspect another area of the codebase in parallel. Read/review only after the implementer reports back, then resolve issues before starting the next task. If the active implementer needs missing context, gather only that requested context and give it back to the implementer.

## Implementer Prompt Requirements

Each implementer dispatch should preserve the Superpowers implementer template, adjusted only for PlanForge's no-commit policy.

Include:

- task name and full task text from the plan;
- scene-setting context: where the task fits, dependencies, and architecture;
- a "Before You Begin" section telling the implementer to ask questions about requirements, acceptance criteria, approach, dependencies, or assumptions before starting;
- a job list: implement exactly the task, write tests when appropriate, verify, self-review, report back, and do not commit;
- working directory;
- permission to pause and ask questions if anything unexpected appears;
- code organization guidance: follow plan structure, keep files focused, follow existing patterns, avoid unplanned restructuring;
- escalation guidance: use `NEEDS_CONTEXT` or `BLOCKED` rather than guessing;
- self-review checklist covering completeness, quality, discipline, and testing;
- report format with status, implemented work, verification results, changed files, self-review findings, and concerns.

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
- read files, trace integration paths, or inspect another code area in parallel while an implementer is working;
- implement plan tasks in parallel as an extra implementer while subagents are running;
- dispatch multiple implementation subagents in parallel;
- invent unplanned central implementation work after subagents finish;
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
