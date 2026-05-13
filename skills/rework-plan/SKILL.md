---
name: rework-plan
description: Use when the user wants to revise, refine, extend, shrink, or restructure an existing PlanForge plan.
---

# Rework Plan

Use this skill to update an existing PlanForge plan without restarting brainstorming.

## Workflow

1. Read the existing plan file.
2. Identify the requested change.
3. Inspect project context only if the change depends on current code.
4. Preserve completed checklist state unless the user asks to reset it.
5. Update the same plan by default.
6. Create a new plan only if the new direction materially forks from the old plan.

## Compatibility Rules

Use `skills/_shared/plan-format.md`.

The reworked plan must remain consumable by `execute-plan`.

Do not remove:

- agentic worker header;
- implementation tasks;
- verification checklist;
- final review instructions;
- no-commit Git policy.

## Output

After editing, summarize:

- what changed;
- whether checklist state was preserved;
- whether the plan is ready to execute.
