---
name: rework-plan
description: Use when the user wants to revise, refine, extend, shrink, or restructure an existing PlanForge plan.
---

# Rework Plan

Use this skill to update an existing PlanForge plan without restarting brainstorming from zero.

This is a planning skill. Do not implement code while reworking a plan.

## Workflow

1. Read the existing plan file.
2. Identify what the user wants changed.
3. Inspect project context only if the change depends on current code.
4. Ask one focused question at a time only when the requested change is ambiguous.
5. Preserve completed checklist state unless the user asks to reset it.
6. Update the same plan by default.
7. Create a new plan only if the new direction materially forks from the old plan.
8. Self-review the updated plan before handing it back.

## Compatibility Rules

Use `skills/_shared/plan-format.md`.

The reworked plan must remain consumable by `execute-plan`.

Do not remove:

- agentic worker header;
- spec/design context;
- implementation tasks;
- files to inspect and likely files to modify;
- verification checklist;
- final review instructions;
- no-commit Git policy.

## Plan Self-Review

Before handing the plan back:

1. Check that the user's requested change is reflected.
2. Check that completed checklist state was preserved or intentionally reset.
3. Check that tasks still form a coherent execution order.
4. Check for placeholders, contradictions, and stale references.
5. Check that `execute-plan` can still consume the plan.

## Output

After editing, summarize:

- what changed;
- whether checklist state was preserved;
- whether the plan is ready to execute;
- any remaining ambiguity or risk.
