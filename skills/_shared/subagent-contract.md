# PlanForge Subagent Contract

Use this reference when executing a PlanForge plan in subagent mode.

PlanForge keeps the Superpowers dispatch mechanics because they are reliable. The review strategy is simplified, but subagent context discipline should stay strong.

## Core Principles

- Fresh implementer subagent per task.
- Implementers get curated context, not the orchestrator's session history.
- The orchestrator reads the plan and extracts the task.
- The orchestrator gives the implementer the full task text and only the needed shared context.
- The implementer should not have to read the whole plan independently.
- The implementer may ask questions before or during work.
- The orchestrator answers questions clearly before letting work continue.
- Do not dispatch implementation subagents in parallel when their edits may conflict.

## Implementer Prompt Requirements

Every implementer dispatch includes:

- plan name and goal;
- current task text;
- relevant design decisions;
- relevant boundaries;
- files to inspect;
- likely files to modify;
- verification expected for the task;
- Git policy: no commits, pushes, or PRs;
- status contract.

## Status Contract

Implementers must finish with one of:

- `DONE`: task completed and verified.
- `DONE_WITH_CONCERNS`: task completed, but there are concerns the orchestrator must read.
- `NEEDS_CONTEXT`: task cannot continue without more information.
- `BLOCKED`: task cannot be completed with the current plan or context.

## Orchestrator Handling

If status is `DONE`, inspect changed files, review verification evidence, and continue only if the task meets the plan.

If status is `DONE_WITH_CONCERNS`, read concerns before continuing. If the concern affects correctness or scope, address it before moving on.

If status is `NEEDS_CONTEXT`, provide missing context and redispatch.

If status is `BLOCKED`, choose one:

- provide better context;
- split the task smaller;
- use a stronger model if available;
- stop and ask the user if the blocker is genuinely ambiguous.

Never force the same retry after `BLOCKED` without changing context, task shape, or model capability.

## Per-Task Review

PlanForge does not call separate formal review agents after every task by default.

The orchestrator reviews each task for:

- plan compliance;
- scope control;
- changed files;
- verification result;
- obvious code quality problems;
- no automatic commits.

The final separate review agent handles the deeper review after all tasks.
