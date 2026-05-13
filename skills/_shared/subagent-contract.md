# PlanForge Subagent Contract

Use this reference when executing a PlanForge plan in subagent mode.

PlanForge keeps the Superpowers dispatch mechanics because they are reliable:

- The orchestrator reads the plan.
- The orchestrator extracts the current task and shared context.
- The orchestrator gives the implementer subagent the full task text and only the needed context.
- The implementer should not have to read the whole plan independently.
- The implementer reports a clear status.

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

If status is `DONE`, review locally and continue if the task meets the plan.

If status is `DONE_WITH_CONCERNS`, read concerns before continuing. Fix small issues directly or send a short corrective follow-up.

If status is `NEEDS_CONTEXT`, provide missing context and redispatch.

If status is `BLOCKED`, choose one:

- provide better context;
- split the task smaller;
- use a stronger model if available;
- stop and ask the user if the blocker is genuinely ambiguous.

## Per-Task Review

PlanForge does not call separate formal review agents after every task by default.

The orchestrator reviews each task for:

- plan compliance;
- scope control;
- changed files;
- verification result;
- obvious code quality problems;
- no automatic commits.
