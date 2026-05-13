---
name: execute-plan
description: Use when the user wants to implement a PlanForge plan in either subagent mode or inline mode, with optional branch creation and mandatory final review.
---

# Execute Plan

Use this skill to implement an existing PlanForge plan.

Read these shared references as needed:

- `skills/_shared/plan-format.md`
- `skills/_shared/subagent-contract.md`
- `skills/_shared/final-review.md`

## Initial Flow

1. Read the plan.
2. Extract tasks and shared context.
3. Ask once: should I create a branch for this task?
4. If yes, create the branch and continue there.
5. If no, continue on the current branch.
6. Ask execution mode:
   - Subagent mode
   - Inline mode

## Git Policy

- Never commit automatically.
- Never push automatically.
- Never create a PR automatically.
- Never ask to commit at the end.
- Only create a branch if the user approves at execution start.

## Subagent Mode

Use the Superpowers dispatch pattern:

- Orchestrator reads the plan.
- Orchestrator curates task context.
- Implementer subagent receives full task text and needed context.
- Implementer reports `DONE`, `DONE_WITH_CONCERNS`, `NEEDS_CONTEXT`, or `BLOCKED`.

Per task:

1. Dispatch one implementer subagent.
2. Handle `NEEDS_CONTEXT` or `BLOCKED`.
3. Review the result locally as orchestrator.
4. Fix small issues directly or send a short corrective follow-up.
5. Update checklist status.
6. Continue to the next task.

Do not call separate formal review agents after every task by default.

## Inline Mode

The current agent executes the plan directly.

Per task:

1. Execute the task step by step.
2. Review locally against purpose, boundaries, expected result, and verification.
3. Fix issues before moving on.
4. Update checklist status.

## Final Review

Final review is mandatory in both modes.

Use a separate review agent focused only on review. Provide:

- plan text;
- implementation summary;
- changed file list;
- relevant diff;
- verification commands and results;
- known concerns.

Fix valid Critical and Important findings, decide whether Minor findings belong in scope, then rerun fresh verification before claiming completion.

## Completion

Final response must state:

- what changed;
- what verification ran;
- what passed;
- what failed or could not run;
- remaining risk.
