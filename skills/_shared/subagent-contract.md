# PlanForge Subagent Contract

Use this reference when executing a PlanForge plan in subagent mode.

PlanForge keeps the Superpowers dispatch mechanics because they are reliable. The only intended per-task change is replacing the original spec-reviewer and code-quality-reviewer subagent loops with orchestrator review and correction. PlanForge adds one separate final review for the whole implementation.

## Core Principles

- Fresh implementer subagent per task, executed sequentially.
- Implementers get curated context, not the orchestrator's session history.
- The orchestrator reads the plan and extracts the task.
- The orchestrator gives the implementer the full task text and only the needed shared context.
- The orchestrator gathers task context before dispatching the implementer, or after the implementer reports back during review before the next task.
- The implementer should not have to read the whole plan independently.
- The implementer may ask questions before or during work.
- The orchestrator answers questions clearly before letting work continue.
- The implementer implements, tests, verifies, self-reviews, and reports back.
- Do not dispatch implementation subagents in parallel.

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

Also include the Superpowers implementer guardrails:

- ask questions before starting when requirements, acceptance criteria, approach, dependencies, or assumptions are unclear;
- implement exactly what the task specifies;
- write tests when appropriate and verify the implementation;
- do not commit, push, or create PRs;
- self-review before reporting;
- follow the file structure in the plan;
- keep files focused and follow existing codebase patterns;
- escalate with `NEEDS_CONTEXT` or `BLOCKED` rather than guessing.

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

PlanForge does not call the original Superpowers per-task spec-reviewer and code-quality-reviewer subagents by default.

The orchestrator reviews each task for:

- plan compliance;
- scope control;
- changed files;
- verification result;
- obvious code quality problems;
- no automatic commits.

The orchestrator corrects valid per-task issues after review. Small corrections may be edited directly. Larger missing implementation should become a focused corrective task instead of hidden "central integration" work.

The orchestrator is not an extra parallel implementer or side investigator. While an implementer subagent is running, it should wait, answer implementer questions, and keep coordination state. It should tell the user it is waiting for the implementer rather than claiming it is doing side work. It must not read files, inspect another part of the app, trace UI/backend integration paths, prepare hidden implementation, or implement other plan tasks in parallel. Read and review after the implementer reports back, then resolve issues before starting the next task. If the active implementer needs missing context, gather only that requested context and hand it back to the implementer.

The final separate review agent handles the deeper review after all tasks.
