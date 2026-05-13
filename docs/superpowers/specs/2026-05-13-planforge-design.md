# PlanForge Design

## Summary

PlanForge is a lightweight Codex plugin inspired by Superpowers. It keeps the parts that work well: guided brainstorming, written plans, agent-readable task structure, reliable subagent dispatch, independent review, and verification before completion. It removes the parts that are too expensive or too heavy for this workflow: mandatory separate spec and implementation-plan phases, automatic commits, per-task formal review agents, and plans that prewrite large blocks of code.

The purpose is to turn ideas, fixes, and existing plans into practical implementation plans that Codex can execute later, either inline or with subagents.

## Goals

- Build a Codex plugin named `planforge`.
- Keep the workflow close to Superpowers instead of inventing a new methodology.
- Merge brainstorming and writing-plans into one planning output.
- Generate one final `.md` plan by default, containing spec/design plus implementation guidance.
- Support both new-feature planning and focused fix planning.
- Support reworking existing plans.
- Support execution in both subagent mode and inline mode.
- Preserve the reliable Superpowers approach for calling subagents: curated context, full task text, clear status contract, and orchestrator control.
- Require final review by a separate review agent in every execution mode.
- Avoid automatic Git commits, pushes, or PRs.

## Non-Goals

- Do not recreate the full Superpowers plugin.
- Do not add automatic commit workflows.
- Do not add HTML approval UI in the first version.
- Do not require separate spec and plan files by default.
- Do not run formal spec-review and code-quality-review agents after every task by default.
- Do not generate giant implementation plans that duplicate full file contents unnecessarily.
- Do not add hooks, MCP servers, apps, or marketplace automation in the first version.

## Plugin Shape

PlanForge should be a standard Codex plugin that can be published to GitHub and installed into Codex.

Required structure:

```text
.codex-plugin/
  plugin.json
skills/
  brainstorm-plan/
    SKILL.md
  brainstorm-fix/
    SKILL.md
  rework-plan/
    SKILL.md
  execute-plan/
    SKILL.md
  _shared/
    plan-format.md
    subagent-contract.md
    final-review.md
```

The manifest should identify the plugin as:

- Name: `planforge`
- Display name: `PlanForge`
- Category: `Coding`
- Skills path: `./skills/`
- Capabilities: `Interactive`, `Read`, `Write`

## Core Workflow

PlanForge has four user-facing skills:

```text
brainstorm-plan
brainstorm-fix
rework-plan
execute-plan
```

The main flow is:

```text
brainstorm-plan or brainstorm-fix
-> final plan .md
-> optional execute-plan
-> branch question
-> mode question
-> implementation
-> final separate review
-> corrections
-> final verification
```

Plans are saved by default to:

```text
docs/planforge/plans/YYYY-MM-DD-topic.md
```

PlanForge may support writing separate files later, but the default behavior is one final plan file containing both design/spec and implementation guidance.

## Plan Format

The generated plan must be detailed enough for agents to follow without drifting, but it must not become a duplicate code-writing exercise.

Each plan should include:

- title;
- agentic worker header;
- goal;
- project context;
- spec/design;
- important decisions;
- boundaries and non-goals;
- implementation tasks;
- verification checklist;
- final review instructions;
- risks or concerns.

Each implementation task should include:

- purpose;
- context for the agent;
- files to inspect;
- likely files to modify;
- boundaries;
- expected result;
- verification;
- review notes;
- subagent status contract.

The plan should guide the executor strongly enough to stay on track, while leaving the actual code implementation to the executing agent.

## brainstorm-plan

Use `brainstorm-plan` for new features, behavior changes, tools, apps, plugins, or broad implementation ideas.

Expected behavior:

1. Inspect current project context before asking detailed questions.
2. Ask questions one at a time.
3. Prefer concise questions and avoid unnecessary ceremony.
4. Propose alternatives only when there is a real tradeoff.
5. Present the design/spec in chat for approval.
6. After approval, write one final plan file in `docs/planforge/plans/`.
7. Ask whether the user wants to execute the plan now.

This skill combines the original Superpowers sequence:

```text
brainstorming -> design/spec
writing-plans -> implementation plan
```

into:

```text
brainstorm-plan -> final implementation plan with design/spec included
```

## brainstorm-fix

Use `brainstorm-fix` for bugs, regressions, failing tests, corrections, and localized updates.

It is like `brainstorm-plan`, but more focused on reading code and diagnosing the issue. It should ask fewer questions because the user usually already described the problem.

Expected behavior:

1. Treat the user report as the main problem statement.
2. Inspect relevant code, tests, logs, and errors first.
3. Ask questions only when blocked or when intended behavior is ambiguous.
4. Produce a focused diagnosis.
5. Write a final fix plan in `docs/planforge/plans/`.
6. Ask whether the user wants to execute the plan now.

If the request is actually a large feature or open-ended redesign, route to `brainstorm-plan`.

## rework-plan

Use `rework-plan` when the user wants to update an existing PlanForge plan.

Expected behavior:

1. Read the existing plan.
2. Identify the requested change.
3. Inspect project context only if needed.
4. Preserve completed checklist state unless the user asks to reset it.
5. Update the same plan by default.
6. Create a new plan only if the new direction materially forks from the old one.

The output must remain compatible with `execute-plan`.

## execute-plan

Use `execute-plan` when the user wants to implement a PlanForge plan.

Initial execution flow:

1. Read the plan.
2. Extract tasks and shared context.
3. Ask once whether to create a branch.
4. If approved, create the branch.
5. If not approved, continue on the current branch.
6. Ask execution mode:
   - subagent mode;
   - inline mode.

Git policy:

- Never commit automatically.
- Never push automatically.
- Never create a PR automatically.
- Never ask to commit at the end.
- Only create a branch if the user approves at execution start.

### Subagent Mode

Subagent mode should preserve the reliable Superpowers dispatch pattern:

- The orchestrator reads the plan.
- The orchestrator curates context.
- The implementer subagent receives the full task text and only the needed context.
- The implementer does not need to read the full plan independently.
- The implementer reports one of:
  - `DONE`
  - `DONE_WITH_CONCERNS`
  - `NEEDS_CONTEXT`
  - `BLOCKED`

Per task:

1. Dispatch one implementer subagent.
2. Handle `NEEDS_CONTEXT` or `BLOCKED` with more context, task splitting, stronger model selection, or user escalation when genuinely necessary.
3. The orchestrator reviews the result locally.
4. The orchestrator fixes small issues directly or sends a short corrective follow-up.
5. The orchestrator updates checklist status.
6. Continue to the next task.

The default does not call separate spec-reviewer and code-quality-reviewer agents after every task. That expensive loop is intentionally removed.

### Inline Mode

Inline mode executes tasks directly in the current agent session.

Per task:

1. Execute the task step by step.
2. Review locally against purpose, boundaries, expected result, and verification.
3. Fix issues before moving on.
4. Update checklist status.

Inline mode still requires final separate review.

## Final Review

Final review is mandatory for both execution modes.

The final reviewer must be a separate agent focused only on review. It receives:

- the plan;
- implementation summary;
- changed file list;
- relevant diff;
- verification commands and results;
- known concerns.

The reviewer checks:

- plan compliance;
- missing requirements;
- regressions;
- scope drift;
- tests and verification gaps;
- code quality issues;
- Git policy compliance.

The orchestrator fixes valid findings and reruns verification before claiming completion.

## Verification

PlanForge should follow the Superpowers principle of verification before completion.

Before saying implementation is done, the executor must run fresh verification commands. The final answer must clearly say:

- what passed;
- what failed;
- what could not be run;
- what risk remains.

## Design Decisions

The main design decision is to merge brainstorming and writing-plans into one planning skill output. This keeps the strongest part of Superpowers, which is disciplined thought before implementation, while avoiding two separate documents and a long second planning pass.

The second important decision is to keep subagent invocation close to Superpowers. The workflow changes, but the mechanics of giving subagents curated task context and requiring clear status responses should stay familiar.

The third decision is to move heavy review to the end. Per-task formal review is expensive. The orchestrator handles task-level review, while the final separate reviewer performs the deeper validation.

The fourth decision is to remove automatic Git actions except optional branch creation. The user controls commits, pushes, and PRs.

## Open Questions

None for the first version. The design is approved for planning.
