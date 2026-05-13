---
name: brainstorm-plan
description: Use when the user wants to turn a new feature, behavior change, app, tool, plugin, or broad implementation idea into a final PlanForge implementation plan.
---

# Brainstorm Plan

Use this skill to turn a new idea into a final PlanForge plan.

This merges the useful parts of Superpowers brainstorming and writing-plans into one output:

```text
conversation -> approved design -> final implementation plan
```

Do not implement code during this skill. The output is a plan.

## Workflow

1. Inspect project context before asking detailed questions.
2. Ask one question at a time.
3. Keep questions concise and useful.
4. Propose alternatives only when there is a real tradeoff.
5. Present the design/spec in chat and get approval.
6. Write one final plan to `docs/planforge/plans/YYYY-MM-DD-topic.md`.
7. Ask whether the user wants to execute the plan now.

## Plan Requirements

Use `skills/_shared/plan-format.md`.

The plan must include:

- spec/design;
- implementation tasks;
- boundaries;
- verification;
- final review instructions;
- no automatic commit steps.

## Style

Be lighter than full Superpowers when the scope is clear. Do not restart the conversation after the user has already approved the design.

If the request is a bug, regression, failing test, or localized correction, use `brainstorm-fix` instead.
